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
EventAssignment.build_one_to_one_lookup('player')*
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
have to be looked up from a joint collection but the system would implicitly know it?

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

Clearly *exists_collection()* needs to be invoked all over again. We definitely
don't want the system to execute a database query every time this happens.
Therefore *@cache* decorator helps us here to make the DB client class 
actually remember the results. To use the cache, import it to your code:

```python
from functools import cache
```

That's all for now. Next time let's have a look how to automate MongoDB
queries even further to painlessly deserialize database objects into Pydantic
models!
