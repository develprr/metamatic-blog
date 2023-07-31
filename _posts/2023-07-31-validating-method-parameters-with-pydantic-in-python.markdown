---
layout: post
title: Validating Method Parameters in Python
date:   2023-07-31 5.49.00 +0300
categories: Metamatic Systems
---

![type-checking]({{ site.baseurl }}/assets/method-with-input-parameters.png)

When you start coding in Python programming language as a coder who is routined in some strongly
typed language such as TypeScript Java or C#, my bet is that first thing
you want to enable in Python is strong typing. 

At least, this was one of the first things I started doing on Python when I finally
decided to go for it. It turns out, [Python](https://www.python.org/) is a fantastic programming language
and it has the best selection of scientific libraries available so far.
Luckily, it is also possible to fix that final annoyance that a former [TypeScript](https://en.wikipedia.org/wiki/TypeScript)
developer encounters while getting used to Python syntax.

As I described in [my previous article](https://www.metamatic.net/metamatic/systems/2023/06/05/high-quality-type-safe-code-with-python.html),
there is a way to introduce strong typing into your Python code and it can
be done with [Pydantic](https://pydantic.dev/) library.

In my last article, I showed how to define [Python classes](https://www.w3schools.com/python/python_classes.asp)
peppered with Pydantic type checking. Defining classes as Pydantic models 
helps you enable validation on the class constructor level. However,
this alone is not enough when you really want to use strong typing in Python
the way it is done in typical strongly typed languages. Of course, you also want
every method to validate their input parameters!

#  Validating method input parameters with Pydantic

The use case is clear - if you write a method that excepts an integer value 
as its input parameter, you want Python to type check that and immediately
throw an error if anything else is fed in - a float, a string or whatever else.

Let's check how to make it happen!

In my example, I want to write a wrapper class for [Numpy](/https://numpy.org/) library's ndarray class.
That wrapper class must encapsulate the underlying type-unsafe [ndarray](https://numpy.org/doc/stable/reference/arrays.ndarray.html) 
object and provide alternative constructors for initializing my ndarray 
for quite specific ways that serve my shady purposes (that
cannot stand the light of day!) - therefore I write a special constructor method to get
a new instance of my MSArray class with a specially shaped ndarray object within: 

```python
@staticmethod
@validate_call
def new_shaped(item_count: int, axis_count: int, elements_per_axis_count: int):
  return MSArray(**{
    'array': np.arange(item_count).reshape(axis_count, elements_per_axis_count)
  })
```

Nice, isn't it! The constructor **new_shaped** to create a new MSArray instance with a shaped
ndarray inside needs to be a "static" class method because if it wasn't,
it would be an instance method. But you can't possibly invoke an instance
method when you are only creating your instance at this point! 

And applying **@validate_call** annotation, Pydantic will throw an error if
you try to invoke the method with wrong kind of parameters. Bam!

# Validating custom and third party objects

That's how Pydantic's parameter validation works for primitive data types (such as numbers and strings)
but you need some additional definitions to make the validation framework to
work also for more complex third party objects, such as Numpy's ndarray object et cetera.

Let's make another constructor for our MSArray class that requires an ndarray as its
input parameter:

```python
@staticmethod
@validate_call(config=dict(arbitrary_types_allowed=True))
def new_from_ndarray(array: ndarray):
  return MSArray(**{
    'array': array
  })
```
Adding definition **config=dict(arbitrary_types_allowed=True)** to your *@validate_call* annotation
does the trick!

By the way, when you use these custom objects as properties in your classes,
remember to configure your classes to allow arbitrary types:

```python
class MSArray(BaseModel):
  model_config = ConfigDict(arbitrary_types_allowed=True)
    
  array: ndarray
```

Have a look at my full implementation of [MSArray class on GitHub](https://github.com/develprr/numpy-utility/blob/main/src/msarray.py).
By the way, the "MS" prefix refers to Metamatic Systems which is the title of this blog :)

This is very much all I want to say to the humanity right now. Enjoy your time with Python!
