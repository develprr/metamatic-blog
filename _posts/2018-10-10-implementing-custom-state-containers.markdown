---
layout: post
title:  "Implementing Custom State Containers"
date:   2018-10-10 18:46:12 +0300
categories: metamatic
---
 
The Metamatic Framework provides functions **store** and **update** functions are most likely all what you need for state container management. 
Nevertheless, there may still be situations that user wants to do some old-school state container coding and define state containers by themselves. The good news is that Metamatic supports even that as well.
And even then, it still beats most established state container frameworks in the elegance it is done! This article explains how to create custom 
Metamatic state containers.

One wonderful thing about the Metamatic framework is that a state container as such is a plain object. Therefore implementing a Metamatic state container 
could not be easier. Another wonderful thing is that you modify the state container through direct method invocation, through very basic and plain setter methods.
This makes the code very readable. If any component wants to update a state in the state container, it should be done by
directly invoking a setter method. This approach is opposite to some major state container frameworks that require you to dispatch
events from components in order to update the state container. Such practice does not only turn the code unreadable and unmaintainable but is also
 completely unnecessary and serves no real purpose.

In Metamatic, the flow goes as follows: 

1. States are updated by directly invoking setter (updater) methods of a state container.
2. State container updater method automatically clones the new value object and updates the state container accordingly.
3. After setting a new value to the state container, the Metamatic framework broadcasts the changed state to everywhere in the app.
4. All components that are registered listeners of the dispatched Metamatic event, update their state.

## Important to Remember

Even though updating state container throug direct method invocation,

1. **YOU SHALL NEVER** directly refer to Metamatic state container from outside. You can call updater methods from outside but not directly the actual container.
2. Other components outside the state container shall **NEVER** use getters or direct references to get states from the state container. 
3. Components shall get events from the Metamatic state container only through **connect**, **connectAll** or **handle** functions provided by the framework.

The above mentioned three principles are important to keep in mind because directly referring to Metamatic container states would enable the referrer
components to manipulate the container states without the container being informed. While it's not the end of the world, it's a serious antipattern and strips off the
container's control over its states, which in turn can cause data integrity bugs that are difficult to track.

Read more about these principles in a [blog article on state container strategies](http://www.oppikone.fi/blog/implementing-metamatic-state-container.html).

## Create a State Container

In the Metamatic framework, creating a state container is very easy. The state container is essentially only a very simple plain object. You can name it
as you wish, for instance AppState, AppContainer, MainStore etc. In this example, I'll name the state container as *MetaStore*. To create a MetaStore, create 
*MetaStore.js* file  Of course the file can have any name you wish. In that file, define the state container object as a plain object:

{% highlight javascript %}
const MetaStore = {};
{% endhighlight %}

As already mentioned, you really can name it as you wish, I just call it *MetaStore* for convenience!

## Enabling MetaStore to Receive And Broadcast Changes 

Let's say that you want MetaStore to centrally hold some piece of data, let's say email address, and when the email address changes, to
broadcast the change:

{% highlight javascript %}

export const STATE_EMAIL_ADDRESS = 'MetaStore:user.emailAddress';
export const setEmailAddress = (emailAddress) => 
  updateStore(MetaStore, STATE_EMAIL_ADDRESS, emailAddress);
{% endhighlight %}

And register any React component to listen for email address change:

{% highlight javascript %}
componentDidMount = () => connect(this, STATE_EMAIL_ADDRESS, (emailAddress) => this.setState({emailAddress}));
{% endhighlight %}

For most cases, this is all what you need! In most cases, you only need MetaStore to replicate the data, store it, and broadcast the change
to all parts of the app where that data is being displayed. But if you want to create a custom setter function that does some custom modification to the
objects other than just storing them, you can also write an entirely customized setter function and then exclusively dispatch whatever you wish:

{% highlight javascript %}
export const EMAIL_ADDRESS_CHANGE = 'EMAIL_ADDRESS_CHANGE';

export const setEmailAddress = (emailAddress) => {
  MetaStore.modifiedEmailAddress = doSomeModifications(emailAddress);
  dispatch(EMAIL_ADDRESS_CHANGE, modifiedEmailAddress);
}
{% endhighlight %}

## Use updateStore Function for Efficient State Manipulation

It's very common that you want MetaStore to only do the basic thing: store and dispatch. Therefore Metamatic provides **updateStore** function 
to do this on a whim. The wonderful thing is that you can achieve the essential store-and-broadcast incidence with one line of code:

{% highlight javascript %}
export const setEmailAddress = (emailAddress) => 
  updateStore(MetaStore, 'MetaStore:user.emailAddress', emailAddress);
{% endhighlight %}

What *updateStore* does is that it clones the value object, in this case the *emailAddress* (no matter whether it's a primitive type or a complex object),
updates the "emailAddress" property inside *MetaStore* and then dispatches this changed value to all over the app, updating all components that use this property.

The first parameter for *updateStore* function is the actual store that you created earlier. The second parameter is the property locator.
It specifies the target property inside the state container that will be updated. In this case, there is *user* object inside MetaStore, 
having a property *emailAddress* to be updated. 

If that structure does not exist inside the container, no worries, it will be created by *updateStore* on the fly!
After the state update described in the example above, *MetaStore* would contain a structure as follows: 

{% highlight javascript %}
MetaStore = {
  user: {
    emailAddress: 'somebody@somewhere.com'
  }
}
{% endhighlight %}

After the property was updated inside the container, it will then be broadcasted to all over the app as a passenger for an event, whose name is, 
perhaps not surprisingly, exactly the same as the property locator, which is in this example 'MetaStore:user.emailAddress'.

It is highly recommended that you parametrize the property locator, for example as follows: 
 

{% highlight javascript %}

export const STATE_EMAIL_ADDRESS = 'MetaStore:user.emailAddress';
export const setEmailAddress = (emailAddress) => updateStore(MetaStore, STATE_EMAIL_ADDRESS, emailAddress);

{% endhighlight %}

Parametrizing the event is very practical because you can then more easily implement a state change listener in receiving React components that will update
themselves when the state was changed:

{% highlight javascript %}
componentDidMount = () => connect(this, STATE_EMAIL_ADDRESS, (emailAddress) => this.setState({emailAddress}));
}
{% endhighlight %}

## Preconfigure Listener Components With observeStore Function

You may want components to receive their states or part of their states from outside already upon mounting. Let's assume that your state container
holds a list of cars already in a very early phase of the application initialization. Then later you want to create a car selector component that should 
have the car data available already when it's created - not only when it's changed next time. Why not? It makes sense that we should preconfigure
a component's state with right data already early on when the component is being created in the first place - given that the data is already available
in some part of the application by that time, right?

{% highlight javascript %}

// MetaStore with preconfigured values available:
import {observeStore} from 'metamatic';

const MetaStore = {
  currentUser: {
    carsAvailable: [
        {
          value: 'tesla-model-3',
          label: 'Tesla Model 3'
        },
        {
          value: 'kia-niro-ev',
          label: '2019 Kia Niro EV'
        }
    ]
  }
}

// make a data event to point to values in store:
export const STATE_CARS_AVAILABLE = 'MetaStore:currentUser.carsAvailable';

// make the data observable:
observeStore(MetaStore, STATE_CARS_AVAILABLE);
{% endhighlight %}

Now, let's make CarSelector receive the available cars preconfigured already when the component is being mounted:

{% highlight javascript %}
...
import {connect} from 'metamatic';
import {STATE_CARS_AVAILABLE} from '../store/MetaStore';

class CarSelector extends  Component {
                          
  componentDidMount => connect(this, STATE_CARS_AVAILABLE, (carsAvailable) => this.setState({carsAvailable}));
  ...
}
{% endhighlight %}

What happens here is that since the available cars were defined already earlier in the MetaStore, now when a new CarSelector component instance is created,
the available cars will be copied into its state from the MetaStore already upon mounting. A very convenient way to preconfigure React object's states
upon initialization! Of course, in real world, the available cars list won't be hard-coded inside MetaStore. They will rather be loaded over REST API and then
placed into MetaStore by preferrably using **updateStore** function. Either way, **connect** function will clone that car data into CarSelector's state
immediately when it's available!


