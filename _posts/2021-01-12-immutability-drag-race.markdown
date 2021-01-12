---
layout: post
title:  The immutability Drag Race
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

## When Immutability is Great

With immutability, when one part of the software
has a reference to a certain object, we can be sure that no other
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
in many different ways which are hard to predict. 

Therefore, granting many functions an access to one object through 
reference is inherently a bad thing, opening the gates to buggy software.

The solution to the multiple access problem is to code in an immutable way,
which means that all functions modifying some state clone the state when
modifying it and then just replace the original state with the new 
state. So no objects "mutate" but are rather replaced by the "next
generation" of the object.

## When Immutability is not that Great

While following the immutability principle in the UI code clearly
helps avoid some serious problem in the software, it's always
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

## The Game is On!

Here is a JavaScript function that generates an array in an immutable way,
using the spread operator to actually clone the array under construction
during every iteration:

```JavaScript
const createArrayInImmutableWay = (arraySize) => {
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
const createArrayInMutableWay = (arraySize) => {
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
createArrayInImmutableWay(arraySize);
let timeStop = new Date();

const immutableFunctionDuration = timeStop - timeStart;

console.log("immuta

timeStart = new Date();
createArrayInMutableWay(arraySize);
timeStop = new Date();

const mutableFunctionDuration = timeStop - timeStart;

console.log(`creating an array in immutable way took ${immutableFunctionDuration} ms.`);
console.log(`creating an array in mutable way took ${mutableFunctionDuration} ms.`);
```

On my PC, the immutable execution with spread operator took *30 298* milliseconds
while the pushy old way took only *22* milliseconds.

What results do _you_ get?
