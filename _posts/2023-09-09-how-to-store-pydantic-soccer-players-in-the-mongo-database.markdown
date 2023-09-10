---
layout: post
title: How To Store Pydantic Soccer Players in the Mongo Database
date:   2023-09-09 12.28 +0300
categories: Python Pydantic MongoDB
---

![soccer-database]({{ site.baseurl }}/assets/soccer-database.png)

I have written already some articles about how to use [Pydantic](https://pydantic.dev/) data validation
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
Anyway, in this example I want to model soccer players, games, excercises
and events and store everything in a database so I can execute
complex queries on them and calculate different kinds of statistical 
truths about all possible aspects related to them. So I start by creating a model "Player"
to represent a soccer player. I extend my player model from the base class MSModel to enable
robust data validation and database communication:

```python
from pydantic import StrictStr, validate_call, create_model
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
first [Player](https://github.com/develprr/utility/blob/main/src/player.py) using this model:

```python
player = Player.new("21", "Ronaldinho Gaucho")
```

At this point the model is yet quite simple, allowing it to have
just two fields, name and ID. As ID is defined as a string, 
it can be any string. In this case, I have decided to use Ronaldinho's
jersey number 21 as identifier. Since our model uses Pydantic (through
its super class MSMOdel), it would not allow erratic instantiation
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
Now, this looks pretty much like standard MongoDB query (using [PyMongo](https://pymongo.readthedocs.io/en/stable/index.html))
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

Next time, let me dive deeper into the actual implementation details
of this new ORM.

Until then, keep safe!
