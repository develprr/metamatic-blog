---
layout: post
title: How To Store Pydantic Soccer Players in the Mongo Database
date:   2023-09-09 12.28 +0300
categories: Python Pydantic MongoDB
---

![soccer-database]({{ site.baseurl }}/assets/soccer-database.png)

I have already written some articles about how to use [Pydantic](https://pydantic.dev/) data validation
library to improve your Python code.

Now, if I was able to inspire you and you are on your way to become 
a great data scientist (or an excellent application developer!)
you will inevitably start wondering at some point 
how to store your data objects in a database. 

After all, when you have modeled your problem domain into wonderful
data objects validated with Pydantic library, the next big thing for 
you is to find a way to conveniently persist your data objects in
a database. 

To make things simpler, I wrote for you a new base model class 
[MSModel](https://github.com/develprr/utility/blob/main/src/msmodel.py). 

By extending it you can easily save your Pydantic data objects
to Mongo database. Likewise, MSModel allows you to load your objects
directly as Pydantic models from the database. I chose MongoDB to serve
as the database solution for this use case because MongoDB provides
a powerful approach to allow the developer to very easily 
make their data objects persistent in the database without the eternal 
headache about SQL schemas. Yet MongoDB allows you to execute complex queries similar
to what you can expect from SQL-based databases. Read more about my
thoughts about MongoDB in my [previous posts](https://www.metamatic.net/metamatic/systems/2023/03/30/why-mongodb-still-kicks-ass.html).

Be inspired by what I have created and be free to copy my code 
and use it as the starting point for your own database-aware
Pydantic models that serve *your* needs!

By extending your data objects from my exemplary MSModel, you make them both
Pydantic and data-aware at the same time.

# An example with soccer players

Imagine for a second that you are a great data scientist obsessed with
soccer statistics... *(Or just imagine that you are another pathetic "soccer-dad":)*
Both ways, in this example I want to model soccer players, games, excercises
and events and store everything in a database so I can execute
complex queries on them and calculate different kinds of statistical 
truths about all possible aspects related to them. So I start by creating a model "[Player](https://github.com/develprr/utility/blob/main/src/player.py)"
to represent a soccer player. I extend my player model from the base class MSModel to enable
robust data validation and database communication:

```python
from pydantic import StrictStr, validate_call
from msmodel import MSModel
  
class Player(MSModel):

  id: StrictStr
  name: StrictStr
  
  @staticmethod
  @validate_call
  def new(id: StrictStr, name: StrictStr):
    return Player(**{
      'id': id,
      'name': name
    })
    
  def get_description(self):
    return f"{self.name}: {self.id}"
```
Now, this is the first implementation of a Player model. Create your
first Player using this model:

```python
player = Player.new("21", "Ronaldinho Gaucho")
```

At this point the model is yet quite simple, allowing it to have
just two fields, name and ID. As ID is defined as a string, 
it can be any string. In this case, I have decided to use Ronaldinho's
jersey number 21 as identifier. Since our model uses Pydantic (through
its super class MSModel), it would not allow erratic instantiation
of the model, for example using some other data type as constructor
parameter than two strings. Giving the ID as an integer would cause
this model to throw an error. Without Pydantic, erratic construction
would just pass without objections!

To insert your player to the database, just code:
```python
player.insert()
```
To find your player from the database by its ID, you can:
```python
found_player = Player.find_one({ "_id": "21" }) 
```
Now, this looks pretty much like a standard MongoDB query (using [PyMongo](https://pymongo.readthedocs.io/en/stable/index.html))
but the difference here is that using just PyMongo you would get just
a dictionary object from the database. But when using our Player model
that extends from MSModel class, the object that was loaded from the
database isn't just a dictionary object but instead, the framework
already converted the object into our Player model.
To verify this, you can directly invoke instance methods for the 
player found in the database:

```python
player.get_description()
```
Note that method *get_description()* is a custom method that 
we defined by ourselves to the model. So our Player model
is fully database-aware and natively supports both persisting
the object to Mongo dtabase *and* deserializing it directly as
a Player object from the database.

So what we have here is our own self-implemented [ORM](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping)
(object-relational-mapping) for Python (on Pydantic) and MongoDB!

## How this magic works

Let's have a look how MSModel can deserialize objects from the database
to the targeted models.

Using the fundamental MongoDB client, PyMongo,
you'd find your player from "player" collection by coding:
```
player_document = mongo_client["player"].find_one({ "_id": "21" })
```
What you get is a basic JSON-like dictionary data object, in this case
with name "Ronaldinho Gaucho".

Now this is fine but when you have implemented some logic specific
to player data objects, the returned data object does not contain those
methods, such as *get_description()* like in my example. 
Instead, you are required to explicitly instantiate your data object
with the database result document as follows:

```
pydantic_player = Player(**player_document)
```

It is just awkward if you need to do this sort of type casting extra in your code.
It is so much better when your database connection layer does
these basic conversions for you on the fly.
Therefore I wrote a method inside MSModel that dynamically wraps a 
dictionary object (retrieved from the MongoDB) into the actual Pydantic class
that provides the logic to operate on it:

```python
@classmethod
def new_from_document(cls, document):  
    # determine the target class into wich the Mongo document must be converted:
    classname = cls.__name__ 

    # determine the module in which the target class resides: 
    modulename = classname.lower()
    
    # import the actual module by the module name that we reasoned:
    module = importlib.import_module(modulename)
  
    # get a reference to the class object of the target class inside the module:
    class_ref = getattr(module, classname)

    # since our Pydantic objects are expected to have "id" field
    # whereas MongoDB document has an "_id" field
    # make a clone of the document that has "id" field:  
    document_with_id = {**document, "id": document["_id"]}

    # finally, create an instance of target class passing the document as constructor parameter:
    return class_ref(**document_with_id)
```

As a small nuance, my implementation requires the module of the target class
to be the same as the class name in lower case: class *Player* must be placed into 
module *player*. This is fine for me because that's actually how I like to write Python code - at least for the time being. 

## Finding many players

When you wanted to find all documents from MongoDB collection and using the plain
PyMongo driver, you'd be writing something like this:

```python
documents = mongo_client["player"].find({})
```

As a result, you will get a list of dictionary objects. This is fine,
but when you actually want to use your nice business domain objects made with
Pydantic, you will inevitably need to convert those dictionaries
returned by MongoDB into your own business objects. It would lead to
infuriatingly verbose, repetitive and boilerplatish code if you had 
to explicitly remember to apply the conversion code after
each code line that makes an actual MongoDB query. 

Instead, you certainly want to have a query function that automatically does
this magic for you. Let's define such in our database mapping layer
*MSModel*:

```python
@classmethod
def find(cls, query):
  collection_name = cls.__name__
  documents = MSMongoClient.singleton.find(collection_name, query)
  return list(map(cls.new_from_document, documents))
```

It wasn't too complicated, was it? :) Now, let us find our players
from the database using our Player model with this new capability:

```python
found_players = Player.find({})
```
The returned objects are readily converted to our Player objects
which have all the neat instance methods available that we
defined for them.

If you find my examples insightful, I encourage you to use these ideas 
to create your own masterpiece!

Next time, I might be diving even deeper into the intriguing topic 
of mapping objects into database entities.

Until then, keep safe!
