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

But that's for the next time. Talk to you then!

