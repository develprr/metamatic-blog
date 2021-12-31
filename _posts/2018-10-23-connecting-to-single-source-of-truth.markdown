---
layout: post
title:  "Connecting to Single Source of Truth"
date:   2018-10-23 23:53 +0300
categories: metamatic
---

## EDIT 2021-12-31: this article is deprecated!

```
Please do not try to install libraries described here, 
since they are currently fairly outdated and 
the support libraries have changed,
rendering the examples useless for now!
```


## The Metamatic State Container Model

The Metamatic™ framework introduces an embedded state container model. 

There are two major strategies to implement a state container, which are the **Two-Way-Events** strategy and the **One-Way-Events** strategy.
The *Two-Way-Events* strategy means that the central data container (MetaStore) communicates with the rest of the software only through events. 
In Two-Way-Events strategy, the state container uses events for both sending and receiving data. It listens for events for receiving data and
dispatches events for sending data. But in *One-Way-Events* strategy instead, events are used only for broadcasting. The State Container does not 
listen for events to receive data. The data is placed inside the container directly through setter or update method invocations from outside.

## Choosing the Invocation Strategy

In the Two-Way strategy, the events flow in two directions. They flow downstream, from the state container downward to the UI components.
And they flow upstream, from components upward back to the container. Downstream flow happens when the state container fires an event. 
The event is being handled down the stream in the UI components that listen for the events from the State
container. And when they flow upstream, from the UI components back to the state container, when a UI component dispatches an event back to the State Container.

With Metamatic, you can implement state container communication using both *Two-Way-Events* and *One-Way-Events* strategies. *One-Way-Events* strategy enables you to write more straightforward
code because it will be easier to track the program flow upstream back to container from the components, since you implement container updater functions that are directly
invoked by your components. It is recommendable to primarily update states through direct invocation from components. But if an event is to update 
at least two different states, then the component should rather dispatch an event than directly call the container updater function.

Example of dispatching an event upstream from a component:

{% highlight javascript %}

import {dispatch} from 'metamatic';
import {EVENT_SELECT_CAR} from '../config/events'

export SomeComponent extends Component {

  onClick = (event) => dispatch(EVENT_SELECT_CAR, event.target.value);

}
{% endhighlight %}

Example of directly invoking an car selection update method:

{% highlight javascript %}

import {dispatch} from 'metamatic';
import {selectCar} from '../states/CarState'

export SomeComponent extends Component {

  onClick = (event) => selectCar(event.target.value);

}
{% endhighlight %}
