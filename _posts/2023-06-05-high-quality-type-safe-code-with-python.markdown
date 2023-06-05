---
layout: post
title: High Quality Type-Safe Code With Python
date:   2023-06-05 7.23.00 +0300
categories: Metamatic Systems
---

If you are experienced in [Java](https://en.wikipedia.org/wiki/Java_%28programming_language%29) or 
[TypeScript](https://en.wikipedia.org/wiki/TypeScript) and then start coding
in [Python](https://en.wikipedia.org/wiki/Python_%28programming_language_%28) language, my bet is that the first thing that will be making
you annoyed is the lack of strong typing!

There is a reason why TypeScript language has become immensely popular
in recent years. And the reason is - surprise - the [strong typing](https://en.wikipedia.org/wiki/Strong_and_weak_typing) that it 
provides! If this wasn't the reason, everybody would be using just [JavaScript](https://en.wikipedia.org/wiki/JavaScript) -
it is not to be forgotten that TypeScript is really just a hack
on top of the actual underlying programming language beneath, which is JavaScript.
All TypeScript code is transpiled into the actual JavaScript code before its execution.

# Why lack of strong typing in Python sucks

If you never really wrote code in any other language than Python,
you may not even be aware what sucks with Python. But if you arrive at the planet
Python from the planet TypeScript, you will immediatelly realize the problem.

Your functions may perfectly work in your unit test bench but when you
use them in your execution code, nothing works anymore and everything just
blows up. This is because in the laborary environment of your unit tests,
you are at least passing the right kind of objects as arguments to 
your functions. But when you are writing the actual execution code, 
there is nothing to prevent you from passing wrong type of objects as
parameters to your functions. 

It is commonplace in Python code that an object you have at hand isn't actually 
the kind of an object you *believe* it is. This makes you passing objects 
to functions that aren't expecting them and referring to object properties that do not exist. 

*And you will be spending a lot of time debugging your code!*

In TypeScript, this problem rarely occurs because you *can't* make these kinds of mistakes. Firstly, the editor
will already warn you about the mistakes when you are only starting to write
them, and secondly, even if you ignore this warning, the code won't even 
compile so you won't be running it before it's right. 

None of these gatekeepers really work in Python. Well, there is a feature
called [hints](https://docs.python.org/3/library/typing.html) in Python, 
but in my honest opinion, it doesn't really work that well. Because
it does just what it promises. It may give your editor *hints* about
what kinds of objects should be passed as arguments but it won't actually prevent
you from typing and running erratic code.  

But no worries, there is a fix. Let's have a look at it!

# Fix Python with Pydantic library

The good news is that you actually *can* achieve about the same level
of type safety in Python code as you natively get in TypeScript, if you use
[Pydantic](https://pydantic.dev/). Let's use Pydantic now and define 
a user that has many addresses:

```python
from typing import List, Optional
from pydantic import BaseModel, StrictStr, StrictInt

class Address(BaseModel):
  street_name: StrictStr
  street_number: StrictInt
  postal_code: StringStr
  country: StrictStr

class User(BaseModel):
  id: StrictInt
  name: StrictStr
  addresses: List[Address] = []
```

And *now* whe are starting to get here some robust schema definitions,
familiar from TypeScript language! Let me instantiate a user with 
some addresses:

```python
user = User(**{
  'id': 124,
  'name': 'Jon Doe',
  'addresses': [{
    'street_name': 'Sesam Street',
    'street_number': 1,
    'postal_code': 54321,
    'country: 'Everland'
  },
  {
    'street_name': 'Vinegar Valley',
    'street_number': 12,
    'postal_code': 123456,
    'country: 'Neverland'
  }]
})
```

Now, this code executes because it correctly instantiates the structure
of a user. But if you don't have the constructor object right, your
code fails.

# It's a list of strings, but is it really?

Let me show another example. You are writing code that utilizies a certain
external library. You believe that your function is getting a list of strings
when invoking some function from the library. But is your assumption correct?

You will be better off debugging your code if your functions cannot be accessed
at all by wrong kinds of objects.  You are fortunate if you can be sure
that you really have a list of strings when you think that you have a list of strings - 
because the execution would never even start on that code otherwise. So let's have a look at [my
implementation of a wrapper](https://github.com/develprr/gensim-utility/blob/main/src/stringlist.py) 
that ensures that you really have a list of strings when you believe you have a list of strings:

```python
from typing import List
from pydantic import BaseModel, StrictStr

class StringList(BaseModel):
  items: List[StrictStr]
  
  @staticmethod
  def new(strings: list[str]):
    return StringList(**{
      'items': ['list', 'of', 'words']
    })

def test_instantiation():
  assert(type(StringList.new(['list', 'of', 'words']))) == StringList
```

Using these kinds of encapsulations in your code can really boost your 
productivity and make debugging easier when you can be sure that the 
objects that your code handles actually are the kinds of objects your 
code assumes them to be. And if not, your gatekeeping constructor functions
will alarm you about the case, so you won't be spending your time debugging
places thar aren't the problem.

*In contrast to a common belief, it's a sign for good quality,
not bad quality, when you are actually getting stuff done!*

# But it's only runtime checking

Pydantic provides robust TypeScript-like schema validation and checking
but it only works in runtime. Unlike with TypeScript code, your editor won't still be nagging in 
the first place when you are actually writing the mistake. But is this really so 
bad? There are two sides also on this coin!

Namely, TypeScript's [compile-time](https://en.wikipedia.org/wiki/Compile_time) 
type checking does come with its own problems. It may prevent you
from *writing* faulty code, but it won't be helping you in production environment
when your system receives and serializes objects from external interfaces,
HTTP requests, database queries or files. If those objects aren't
what expected, coding-time or compile-time type-checking won't help you
at all. It helped you write your code right for the ideal case and
it even helped you to write your tests right. 

But when your code is in production and receiving objects over HTTP requests
it won't be checking anything when it gets a mismatching one - a property that your schema 
defined as obligatory may still be missing and enter into your function
crashing it from inside, because the lack of runtime type checking. And 
the same may happen as a result of a database query or when the system serializes
an object from the disk. 

On the contrary, runtime type checking that you get with Pydantic, 
prevents erratic and malformed objects from entering your system when it is in production. 
It will be easier to debug what is wrong in your production system when you have robust
runtime gatekeepers on your instances - you will immediately see from 
the error logs that the error was caused because a HTTP request object
didn't match the expectation. This saves you from a lot of debugging effort.

Of course you can also add runtime validation in TypeScript (for example
with [Yup](https://www.npmjs.com/package/yup) library) but it's then 
always manual extra effort on top of TypeScript's native compile-time type
checking. Can't have one with another!


I'll be back coding in Python, you can be sure about that. Cheers!
