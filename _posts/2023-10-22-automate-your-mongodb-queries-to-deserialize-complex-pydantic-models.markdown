---
layout: post
title: Automate Your MongoDB Queries to Deserialize Complex Pydantic Models
date:   2023-10-22 17.14 +0300
categories: Python Pydantic MongoDB
---

![giant mongo deserializer]({{ site.baseurl }}/assets/giant-mongo-deserializer.png)

Relational databases are a conventional way to model and solve real world business
problems. This is because relational thinking is natural for the human
brain. Other than that, they ensure data accuracy and integrity
by avoiding duplication and inconsistency of data. 

As crazy as this may sound, I repeat my earlier claim that MongoDB, 
although surely being a document database, is a relational
database as well. No, it does not support SQL query language. Instead, 
it has its own object query format not too different from [GraphQL](https://graphql.org/). But despite
this it allows to organize collections in a quite relational fashion. 

Let's think about a practical example of a data entity that is very
relational by nature. Imagine that we want to manage a soccer club
and we have to assign players to different kinds of soccer events, such
as excercises and matches. 

So we have a many-to-many relationship between players and events. 
One player can be assigned to multiple events during a week, sometimes
even during one day, and every soccer event has multiple attendees.

Everybody familiar with database modeling understands that modeling
this kind of a relation can be achieved by implementing a connector
table that binds players and events to each other in a many-to-many fashion.

This means we would have a *SoccerEvent* table to store available soccer events,
a *Player* table to keep track of players and an **EventAssignment** table 
to model which players are assigned to which events. 

When we have an *EventAssignment* object we have to be aware that it
is quite a beast since it spans over three different tables in the database!

Let's have a look at it in detail. To describe an event assignment as a JSON
file we would have something like this:
```json
{
  'id': 'some-unique-identifier-here',
  'player': {
      'id': '21',
      'name':  'R. Gaúcho'
   },
   'event': {
      'id': 'some-event-id',
      'name': 'First match'
   }
}
```
This event assignment connects a certain player named R. Gaúcho to a specific 'First match' event.
Note that tables are called *collections* in MongoDB. Let's assume that we load such JSON document
from 'EventAssigment' collection and want to deserialize it into a similarly
named Pydantic model. The fantastic thing about Pydantic is that we are
able to directly instantiate this kind of a composite object that has two object attributes ("player"
and "event") from a similarly nested JSON document.

Let's define first our EventAssignment object as a Pydantic model:
```python
class EventAssignment(BaseModel):
  id: StrictStr
  event: SoccerEvent
  player: Player
```
And then let me write the smallest possible Pydantic model to define players:
```python
class Player(BaseModel):
  id: StrictStr
  name: StrictStr
```
Finally, we write our SoccerEvent model to define soccer events:
```python
class SoccerEvent(BaseModel):
  id: StrictStr
  name: StrictStr
```
Little by little, we begin to discover that the innocent object *EventAssignment* is actually 
a horrible Frankenstein monster piled up by smashing together rusty old metal limbs!

No worries, with Pydantic we can still instantiate this whole structure of nested objects
directly from just one nested JSON document:

```python
my_event_assigment = EventAssignment(**{
  'id': 'some-unique-identifier-here',
  'player': {
      'id': '21',
      'name':  'R. Gaúcho'
   },
   'event': {
      'id': 'some-event-id',
      'name': 'Final match'
   }
})
```

After this, we can access event assigment and its attribute in a conventional
object oriented way, for example:
```python
player_name = my_event_assigment.player.name
```

I can almost hear you saying, *"this is great, but didn't you just say that
players and events reside in separate collections?"*

Sure I did! Now we are coming to the point that if we can instantiate
composite Pydantic models from complex nested JSON files, then we are actually
able to instantiate them from MongoDB structures split over multiple collections as well.

# Understanding query aggregation pipelines

Let's write a MongoDB aggregation pipeline that composes a complete JSON document as described
above from three different collections:

```python 
mongodb_client.aggregate([
  {
    "$match": query
  },
  {
    '$lookup': {
      'from': 'Player', 
      'localField': 'player_id', 
      'foreignField': '_id', 
      'as': 'player'
    }
  }, 
  {
    '$unwind': {
      'path': '$player', 
      'preserveNullAndEmptyArrays': True
    }
  },
  {
    '$lookup': {
      'from': 'SoccerEvent', 
      'localField': 'event_id', 
      'foreignField': '_id', 
      'as': 'event'
    }
  }, 
  {
    '$unwind': {
      'path': '$event', 
      'preserveNullAndEmptyArrays': True
    }
  },
  {
    '$project': {
      'event.id': '$event._id',
      'event.name': 1,
      'player.id': '$player._id',
      'player.name': 1
    }
  },
]);
```

This aggregation query might look a bit scary at first glance
but when we look carefully enough, it becomes evident that it has
quite some repetitive patterns that we could easily automate in 
a conventient and repeatable way. That would then suddenly give us an ad-hoc
framework to facilitate fetching multi-collection data structures
directly into Pydantic models.

We get here a quite handy data persistence pattern if we introduce a code convention
that each attribute of a model is to be stored in a collection whose name equals the attribute's class name.
For example, if we have an attribute "event" whose type is *SoccerEvent*,
then that attribute will be stored in a collection that is also called "SoccerEvent".

Being faithful to this convention, we'll be able to always obtain each attribute's
collection name through reflection. So I expand my ORM solution's base class, [MSModel](https://github.com/develprr/utility/blob/main/src/msmodel.py),
by adding a method to deduce any attribute's collection name: 

```python
@classmethod
def get_field_collection_name(cls, field_name):
  return cls.model_fields[field_name].annotation.__name__
``` 

When looking carefully at the aggregation query above, it consists of two lookups, one
that joins a Player object Player collection and another that joins a SoccerEvent
object from SoccerEvent collection. By default, lookups join the target collection's objects
as an array. However, since we only want one Player object and one Soccerevent object,
it is not purposeful to attach those objects as arrays. Instead, after both lookups there
is an *unwind* command that actually transforms the attached array into singular object,
taking the first and only object from the array. This fits perfectly to our use case
because then the JSON structure that we get perfectly matches our Pydantic model.

Finally, we polish the result with a *project* command which converts MongoDB's *_id* field
into *id* field to fit the pydantic model. We also tell that we want from both object's also
name property. 

# Automating MongoDB queries

A closer look at MongoDB query's syntax makes it evident that it is quite feasible 
to automatically construct the query from Pydantic model's structure.

Let's define a method to automatically get the list of the properties from which
to build the MongoDB query:

```python
@classmethod
def get_field_names(cls):
  return list(cls.model_fields.keys())
```

In *EventAssignment* example case, this method returns fields *id*, *player*
and *event*.  So why not automate things a bit and write a method **build_one_to_one_lookup**
which allows us to build the actual lookup query simply by invoking:

```python
EventAssignment.build_one_to_one_lookup('player')
```

A huge improvement versus manually typing almost identical code pattern every time
we want to implement a one-to-one relation in our database queries!
Now let's go to the actual implementation of the method:

```python
@classmethod
def build_one_to_one_lookup(cls, field_name):
  collection_name = cls.get_field_collection_name(field_name)
  local_field = f'{field_name}_id'
  return [
    {
      '$lookup': {
        'from': collection_name,
        'localField': local_field,
        'foreignField': '_id',
        'as': field_name
      }
    },
    {
      '$unwind': {
          'path': f'${field_name}',
          'preserveNullAndEmptyArrays': True
      }
    }
  ]
```

Taking advantage of this builder method, the logic to generate the original MongoDB query becomes
quite a bit smaller:

```python
@classmethod
def fetch_one(cls, query):
  return cls.aggregate_one([
    {
      "$match": query
    },
    *cls.build_one_to_one_lookup('event'),
    *cls.build_one_to_one_lookup('player'),
    {
      '$project': {
        'event.id': '$event._id',
        'event.name': 1,
        'player.id': '$player._id',
        'player.name': 1
      }
    },
  ]);
```

# To save or not to save

Looking at the semi-automated query, the first question that an observer might
ask is, would it be better if the user didn't need to expliclitly declare which attributes
have to be looked up from a joint collection but instead, the system would rather implicitly know it?

Sure!

To achieve this, there is a temptation to take advantage of *get_field_names()*
method that we just implemented. Now the problem is that this method returns all attributes
of the model and not all attributes are lookup references to other collections - starting
with "id" field. To create a smarter method that can return those attributes that
actually map one-to-one to another collection's objects we need to write some logic
that helps our framework find out whether a particular object type name actually exists
as a collection in the database. So let's [extend our MSMongoClient](https://github.com/develprr/utility/commit/4668d835f2a30d6c0c3a8a42763c86ae1c3b7428) class to provide this
functionality:

```python
def exists_collection(self, collection_name):
  return collection_name in self.collection_names()
  
@cache
def collection_names(self):
  return self.client.list_collection_names()
```

The method *exists_collection()* needs to be invoked all over again. We definitely
don't want the system to execute a database query every time this happens.
Therefore *@cache* decorator helps us here make the DB client class 
actually remember the results. To use the cache, import it to your code:

```python
from functools import cache
```

Now we are ready to implement a method to get only those fields of a model that
actually are collection references. Let's go for it and equip our base model with this code:

```python
@classmethod
def get_collection_references(cls):
  field_names = cls.get_field_names()
  return list(filter(cls.is_field_collection_reference, field_names))
```
The method above loops through all field names and filters those that are actually
collection references. So this needs a helper method to check whether a given
individual field name is a collection reference:

```python
@classmethod
def is_field_collection_reference(cls, field_name):
  field_type = cls.get_field_type(field_name)
  collection_names = MSMongoClient.singleton.collection_names()
  return True if field_type in collection_names else False
```

From this position, it is only a small step to write a true workhorse method
to automatically build all one-to one lookups:
```python
@classmethod
def build_one_to_one_lookups(cls):
  references = cls.get_collection_references()
  return np.array(list(map(cls.build_one_to_one_lookup, references))).flatten().tolist()
```

With these helper methods, *EventAssigment's*
*fetch_one()* method can now be simplified as follows:
```python
@classmethod
def fetch_one(cls, query):
  return cls.aggregate_one([
    {
      "$match": query
    },
    *cls.build_one_to_one_lookups(),
    {
      '$project': {
        'event.id': '$event._id',
        'event.name': 1,
        'player.id': '$player._id',
        'player.name': 1
      }
    },
  ]);
```

If we go even further with this query automation madness, clearly the
same truncation procedure can also be applied to the projection part at the 
end of the aggregation. But should we really sink deeper into this madness?
I think **yes.**

## Automating projections
The reason why I have formulated a *$project* statement at the end of my aggregation pipeline
is that the result object returned by MongoDB doesn't exactly fit into the Pydantic model.
First of all, the object IDs are presented with **id** attribute in the model, whereas MongoDB objects
contain prefixed variants, **_id** fields. Therefore *$project* statement helps convert those *_id* fields 
into their Pydantic form *id*. Additionally projection defines which properties 
we will use from the included foreign objects *event* and *name*:
```json
{
      '$project': {
        'event.id': '$event._id',
        'event.name': 1,
        'player.id': '$player._id',
        'player.name': 1
      }
    }
```

Now let's write another magical method to MSModel to automatically generate the projection:
```python
@classmethod
def build_one_to_one_projection(cls):
  references = cls.get_collection_references()
  sub_projections = list(map(cls.build_sub_projection_by_reference, references))
  projection = {}
  for sub_projection in sub_projections:
    projection = {**projection, **sub_projection}
  return {
    '$project': projection
  }
```
The method iterates model's those fields that are one-to-one references to other collections and builds a sub-projection for each one.
Finally it merges them into the resulting projection and returns that object.
For example, calling *EventAssignment.get_collection_references()* returns list ["player", "event"]. Each field in the list is elaborated
by invoking **buid_sub_projection_by_reference** method: 

```python
@classmethod
def build_sub_projection_by_reference(cls, reference):
  sub_projection = {
    f"{reference}.id": f"${reference}._id"
  }
  field_class = cls.get_field_class(reference)
  data_fields = field_class.get_data_field_names()
  for data_field in data_fields:
    sub_projection[f"{reference}.{data_field}"] = 1
  return sub_projection
```
This method gets a "reference" as argument, which actually means a field that refers to another collection.
Therefore building up the sub-projection for that collection requires to check what data fields that collection
actually contains. 

With data field names I mean all field names model other than its ID field:
```python
@classmethod
def get_data_field_names(cls):
  return list(filter(lambda field_name: field_name != "id", cls.get_field_names()))
```
Sure ID is also data but it isn't exactly model's data but rather a generic mechanism to identify them.
We need this method because **id** field needs to be projected differently from other fields as explained above.
But even so, we can't invoke that class method on a particular model before we actually get that class.
Therefore I made another method to get the Pydantic model class referred to by a data field:

```python
@classmethod
def get_field_class(cls, field_name):
  classname = cls.get_field_type(field_name)
  modulename = classname.lower()
  module = importlib.import_module(modulename)
  return getattr(module, classname)
```

Do you really believe me when I tell you this turpentine works? Well, you shouldn't! Because I don't believe it either before
I test it:
```python
def test_build_one_to_one_projection():
projection = EventAssignment.build_one_to_one_projection()
assert(projection == {
  '$project': {
    'event.id': '$event._id',
    'event.name': 1,
    'player.id': '$player._id',
    'player.name': 1
  }
})
```
Alright, I tested it. It works. Now you can believe me!

## The closing ceremony

This is the last chapter of my article about automating MongoDB queries to instantiate Pydantic models.
This journey has lead us together all the way up to the hill where this particular piece of code, 
**fetch_one** method, can stand upright and proudly gaze at the horizon with the realization in the heart that it doesn't depend anymore on its original host model *EventAssignment*. At this point we are honored to move it to the upper class *MSModel*, where it can begin to serve all models of the future.

```python
@classmethod
  def fetch_one(cls, query):
    return cls.aggregate_one([
      {
        "$match": query
      },
      *cls.build_one_to_one_lookups(),
      cls.build_one_to_one_projection(),
    ]);
```

## Coming soon...

Next time we will learn how to whistle. Whistling is a great skill that helps you express emotions at home, in the neighborhood, at work - and maybe even in the garden.

Cheers!
