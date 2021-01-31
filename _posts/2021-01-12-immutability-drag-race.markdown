---
layout: post
title:  The Immutability Drag Race
date:   2021-01-12 08.48.06 +0200
categories: metamatic
---

There's been a trend toward functional programming in the recent years,
especially in the field of web programming. The trend emphasizes
certain coding principles and loosely bunch them together under one
loose concept of "functional programming paradigm".

One of the principles labeled under the functionality umbrella is
the immutability principle. Let's address some practical cases
to highlight when immutability is great - and when it isn't that great!

## When Immutability Is Great

With immutability, when one part of the software
has a reference to a certain object, we can be sure that no
other parts of the software will be sneakily altering that object
in the background, since such act would risk creating random errors
that might be hard to detect.

This can be highlighted with a practical example. Let's say that
you have an online store UI and it contains a
shopping cart which is an array of purchase items. You have
a function that calculates the total sum of all items. 

At the same time, there is another function that modifies the shopping
cart by changing prices or adding new items. When these functions
execute in parallel, which may very well happen in a typical UI
with asynchronous calls and event based actions, then both functions doing
different things on the same object in parallel may affect the result
in many different ways that are hard to predict. 

Therefore, letting many functions access one object through 
reference is inherently a bad thing, opening the gates to buggy software.

The solution to the multiple access problem is to code immutably,
which means that all functions modifying some state clone the state when
modifying it and then just replace the original state with the new 
state. So no objects "mutate" but are rather replaced by the "next
generation" of the object.

## When Immutability Is Not That Great

While following the immutability principle in the UI code clearly
helps avoid some serious problems in the software, it's always
good to think about performance. The case with UI is that one may
seldom need to think about performance, but the things may be drastically
different on the server side.

In the UI, especially a modern browser based UI that communicates
with the server over asynchronous HTTP calls, the amounts of data 
remain rather small - you may rarely have arrays containing thousands
of objects. And even better, it's the user's CPU that is going to do
the number wrenching!

On the server side, however, you may need to implement procedures
that chronically iterate through several thousands of lines of database
entries, just in order to do some mandatory conversions to serve the UI.

Such tasks may be generating an Excel sheet from database rows or
any background converters that fetch data from external APIs and convert
them into format that your software can better handle.

It turns out, dogmatic sticking to immutablility principle will kill
your software!

## The Immutable Vs Mutable Performance Drag Race

### Creating New Arrays 

Here is a JavaScript function that generates an array in an immutable way,
using the spread operator to actually clone the array under construction
during every iteration:

```JavaScript
const createArrayImmutableWay = (arraySize) => {
  let arrayUnderConstruction = [];
  for (i = 0; i < arraySize; i++) {
    const newEntry = {
      name: `entry ${i}`
    };
    arrayUnderConstruction = [
      ...arrayUnderConstruction,
      newEntry
    ]
  }  
  return arrayUnderConstruction;
}
```

In comparison, another function that just mutates the array under construction
with "push" function:

```JavaScript
const createArrayMutableWay = (arraySize) => {
  const arrayUnderConstruction = [];
  for (i = 0; i < arraySize; i++) {
    const newEntry = {
      name: `entry ${i}`
    };
    arrayUnderConstruction.push(newEntry);
  }  
  return arrayUnderConstruction;
}
```

Ready, steady, go!

```JavaScript
const arraySize = 60000;

let timeStart = new Date();
createArrayImmutableWay(arraySize);
let timeStop = new Date();

const immutableFunctionDuration = timeStop - timeStart;

timeStart = new Date();
createArrayMutableWay(arraySize);
timeStop = new Date();

const mutableFunctionDuration = timeStop - timeStart;

console.log(`immutable creation took ${immutableFunctionDuration} ms.`);
console.log(`mutable creation took ${mutableFunctionDuration} ms.`);
```

On my PC, the immutable execution with spread operator took whopping 
*30 298* milliseconds while the pushy old way took only *22* milliseconds.

Immutable array generation was **1377 times slower!**

## Iterating Through Arrays

"Wait, this is unfair", some might say now. In the test you were
creating arrays, which is not the most common use case! 
Typically, arrays are just altered using *map* and *reduce* functions.
You take one already existing array, and map it into another array,
when you want to create an array with somewhat altered properties.

Okay, let's compare immutable and mutable array modification performances then!

Here we have a typical immutable array modifier function that uses 
a combination of map function and spread operator:

```JavaScript
const changeArrayPropertiesImmutableWay = (array) => array.map((entry) => ({
    ...entry,
    name: "new name"
}));
```

Let's then code a mutable array modifier douchebag that relies on 
a rusty forEach function together with an awkwardly offending 
yet totally tasteless property assignment approach:

```JavaScript
const changeArrayPropertiesMutableWay = (array) => {
    array.forEach((entry) => {
      entry["name"] = "some other name"
    })
    return array;
}
```

We prepare the test by creating a rather big array with one million entries, 
so we can be sure to find out who is who. I will use now the mutable 
array generator I just made to generate the test data, because I just don't
have time to wait until the end of the world to get my test data:

```JavaScript
const reallyBigArray = createArrayMutableWay(1000000)
```

The race may start... Ready, steady, go!

```JavaScript

// Running the immutable modifier:
let timeStart = new Date();
changeArrayPropertiesImmutableWay(reallyBigArray);
let timeStop = new Date();

const immutableDuration = timeStop - timeStart;

// Running the mutable modifier:
timeStart = new Date();
changeArrayPropertiesMutableWay(reallyBigArray);
timeStop = new Date();

const mutableDuration = timeStop - timeStart;

// Releasing the results:
console.log(`immutable array modification took ${immutableDuration}`);
console.log(`mutable array modification took ${mutableDuration}`);
```

On my PC, the immutable array modification took *613 milliseconds*,
whereas it only took *21 milliseconds* to modify the array with the
mutable strategy. This means: 

Immutable array modification was almost **30 times slower!**

## Does This Matter?

I can easily imagine a web developer who might say this comparison is pointless, 
because we are running just some small iterations on rather tiny array snippets 
in JavaScript based web UI that lives inside the client's browser.
And it's taking toll anyway on *client's* CPU - smart phone or laptop,
which won't cost us anything. But wait, not so fast...

### At The Front

Definitely immutable programming approach can be good for the UI due to
the earlier mentioned reasons. But be careful not to process any big arrays 
out there at the front and letting the browser app to grow too heavy.
With this I don't mean the size of the code bundle but rather the burden
the logic puts on CPU's shoulders. 

The digital world isn't entirely separate from the physical world. 
Also a browser-based  apps take the toll on CPU and can make the battery drain out faster.
Be fearful of the day when you get the first report from a user
that their browser app is randomly crashing - an inherently difficult
problem to nail down and fix, if the UI has just slowly grown into a true CPU glutton.

### In The Backend

Quite needless to say, peformance issues can get easily get very critial 
in the backend. The calculation is quite straightforward: if you code
a backend data conversion process that is 30 times slower to perform
than the optimal solution, you will have to rack up 30 times more machines to  
match the performance of just a single properly coded piece of software.

Spending 30 times more money is always 30 times more expensive 
than spending just one unit of money!
