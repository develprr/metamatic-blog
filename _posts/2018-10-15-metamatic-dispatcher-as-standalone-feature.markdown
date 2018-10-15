---
layout: post
title:  "Using Metatic Dispatcher as Standalone Feature"
date:   2018-10-15 23:53 +0300
categories: metamatic
---

The (Metamatic state container framework)[https://www.npmjs.com/package/metamatic] wonderfully abstracts away the burden of writing and managing containers by yourself. With Metamatic, chances are
that you never really need to think about how to actually "broadcast", "radiate" or "dispatch" events. That is, when you launch an even somewhere inside
your app then all parts that listen for that specific event would receive that event and handle it the way they wish. But inside the hood Metamatic indeed
relies on such event dispatching mechanism. 

What is exciting that you can actually use those Metamatic's event features directly byself, bypassing the Metamatic's embeddded state container model.

## Dispatching and Handling Events

When you want to **dispatch** an event somewhere in your app:

{% highlight javascript %}
import { dispatch } from 'metamatic';

dispatch('MY-EVENT', someObject);
{% endhighlight %}

Define a listener for your event using **handle** function:

{% highlight javascript %}
import { handle } from 'metamatic';

handle('SOME-EVENT', (value) => {
console.log(value);
...
})
{% endhighlight %}

## Dispatcher Clones Objects

You may have seen in some other state container frameworks that you must clone the object using a spread operator ( {...someObject} ) always when it is received by the 'reducer' function. In Metamatic, this is not the case. When you dispatch an object with **dispatch** function, the object
is always being automatically cloned. It is a very useful and important feature that objects are cloned when they are dispatched.  If the objects were not
cloned when dispatched into the bit space, that cause the listeners to receive a reference to the original object instead of a clone. That would be
very bad because modifiying the received object inside a listener component would secretly change the original object in the state container, thus making
the state container essentially useless. The very idea of a central state container is that
its objects can't be changed from outside. If it was possible to uncontrollably modify them from outside then the state container would not be able to detect a change and broadcast the change event across the application!

