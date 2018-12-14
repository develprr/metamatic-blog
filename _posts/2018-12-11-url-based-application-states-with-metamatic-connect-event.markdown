---
layout: post
title:  "URL-based Application States with Metamatic's CONNECT Event"
date:   2018-12-11 18:28:12 +0300
categories: metamatic
---

## The Problem of State Defining Sub-URLs

A very typical situation in JavaScript based web apps is that they use client-side urls, 
where application's specific sub-urls are handled by the JavaScript client in the way
that ReactJS router does, among other similar frameworks.

For example there is a use case that your application provides the user with a possibility to 
select some option and when the user makes their choice, the JavaScript app should then load
the relevant data snippet into browser's memory over Ajax request.

That kind of use case would be for instance a situation when the user is asked to [select a car model](https://metamatic-car-app.herokuapp.com/cars)
from a list. In the example, the JavaScript application loads a details data snippet
for the selected car when users selects a car by clicking on it. When the data is loaded the app 
then redirects to [another view that renders the loaded data](https://metamatic-car-app.herokuapp.com/cars/3).

For the details view it is straightforward to render details data because this data already exists
in browsers's memory when the component, *CarDetails* in particular, is instantiated. The car selection event was bound to car details loading function!

Now, that solution has a grave shortcoming - if you forward that car details link to a friend and
they open it, the application won't show the right results because the function to load the needed car details
is only bound to a click listener that is in the different view: the app does not know how to load and show the right data
when the user enters the details view over a direct URL link.

Therefore the application must understand to load the relevant details data also based on the application's URL, not only
a click listener inside some other view.

## The Metamatic Solution

That's where Metamatic's CONNECT system event comes to help. Let's say that the user opens URL [https://metamatic-car-app.herokuapp.com/cars/4](https://metamatic-car-app.herokuapp.com/cars/4) 
that is mapped in the router to CarDetails component:

{% highlight javascript %}
<Route exact path='/cars/:carId' component={CarDetails}/>
{% endhighlight %}

Now this setting will cause that when the user opens a url with car's ID then **CarDetails** component will be rendered.
Therefore, the *CarDetails* view must have access to the corresponding car data that is being centrally maintained in the app's Metamatic store.

Therefore *CarDetails* component must be connected to the Metamatic store that holds car details. 
Using the Metamatic framework with ReactJS, the connection must be done in component's *componentDidMount* life cycle function:

{% highlight javascript %}
componentDidMount = () => connectToStore(this, STORE_CAR_MODEL_ITEM, (store) => this.setState(store));
{% endhighlight %}

In the example, when *CarDetails* component is instantiated, it's local state is entirely replaced with a copy of Metamatic's car model item store
of which name is *STORE_CAR_MODEL_ITEM*. 

Then *CarDetails* component just must trust that the store contains the needed data, *carModelDetails*. Now we must make sure that the store
will ensure the availability of that data. The store must load the relevant data not only when user selected the car model in another view
but also when *CarDetails* was invoked through /cars/**:carId** sub-URL.
 
## How to Implement CONNECT Event Handler

For this use case the Metamatic framework provides a very practical solution. In Metamatic, every time a component is connected to a store, this action
will automatically fire a corresponding system event with syntax **CONNECT**/*STORE_NAME_HERE* that is launched into the application-wide bit-space.

On the store side of a Metamatic app, the store can be initialized to listen for CONNECT event:

{% highlight javascript %}
import {handleEvent} from 'metamatic';

export const STORE_CAR_MODEL_ITEM = 'STORE_CAR_MODEL_ITEM';
export const CONNECT_CAR_MODEL_ITEM = `CONNECT/${STORE_CAR_MODEL_ITEM}`;

export const CarModelStore = () => handleEvent(CONNECT_CAR_MODEL_ITEM, () => loadCarModelDetailsByUrl());
{% endhighlight %}

In the example above we define *CarModelStore()* function that actually makes the Metamatic state manager listen for *CONNECT/STORE_CAR_MODEL_ITEM*
event. When such event occurs, car model details will be loaded based on the car model ID that is described in the URL path.

When *CarDetails* component is connected to the Metamatic store with name *STORE_CAR_MODEL_ITEM*, a system event *CONNECT/STORE_CAR_MODEL_ITEM* is fired
by the Metamatic framework. An example implementation of the actual **loadCarModelDetailsByUrl** function could look like this:

{% highlight javascript %}
const extractCarModelIdFromUrl = () => {
  const url = window.location.href;
  const id  = (url.split('/cars/')[1] || '').split('/order')[0];
  return id;
}

const loadCarModelDetailsByUrl = () => {
 const carModelId = extractCarModelIdFromUrl();
 loadCarModelDetails(carModelId);
}

const loadCarModelDetails = (carModelId) => {
  axios.get(`${CAR_DATA_URL}/${carModelId}`)
  .then((response) => setCarModelDetails(response.data));
};
{% endhighlight %}

Be free to use any else library than axios to load the details over Ajax. And be free to extract the car model ID in a more elegant way than my example :)
The point is however that when car model details are loaded then *STORE_CAR_MODEL_ITEM* must be updated to contain that data:

{% highlight javascript %}
const setCarModelDetails = (carModelDetails) => updateStore(STORE_CAR_MODEL_ITEM, {
  carModelDetails
});
{% endhighlight %}

Updating the store will fire a Metamatic STORE_CAR_MODEL_ITEM event will dispatch the actual store STORE_CAR_MODEL_ITEM to all listeners. Then *CarDetails* 
component will receive that store and have access to the desired *carModelDetails* state inside that store!

## A Generic Solution

A great benefit of this way of binding URL patterns to stores is that every time any component is bound to STORE_CAR_MODEL_ITEM store it will make the store to 
act, namely, optionally loading car model details. For the Metamatic Car App, there are actually several URLs that will require the application to 
know car details data: 

* Car details [https://metamatic-car-app.herokuapp.com/cars/4](https://metamatic-car-app.herokuapp.com/cars/4)
* Car order form [https://metamatic-car-app.herokuapp.com/cars/4/order](https://metamatic-car-app.herokuapp.com/cars/4/order)
* Order confirmation page [https://metamatic-car-app.herokuapp.com/cars/4/confirmation](https://metamatic-car-app.herokuapp.com/cars/4/confirmation)

Then you must edit the Router component to define which view components actually respond to the URL patterns described above:

{% highlight javascript %}
      <Route exact path='/cars/:carId' component={CarDetails}/>
      <Route exact path='/cars/:carId/order' component={OrderView}/>
      <Route exact path='/cars/:carId/confirmation' component={OrderConfirmationView}/>
{% endhighlight %}

Since all those components need the car details data they connect to STORE_CAR_MODEL_ITEM in their constructor. And STORE_CAR_MODEL_ITEM, in turn, 
then loads the car details when needed. Putting all this together, to create views that can be accessed through a distinct URL, you need to do following things: 

1. Use Metamatic's CONNECT event to make a store to optionally load data when a component connects.
2. Connect the component to store in the constructor.
3. Define state-defining URLs for renderer components in React router.

## Complete Example

Find a complete example application to showcase the Metamatic CONNECT event [here](https://github.com/develprr/metamatic-car-app).
