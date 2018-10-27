---
layout: page
title: News
permalink: /news/
---

### Version 1.7.0 Use initState function to initialize states

Metamatic version 1.7.0 introduces initState function that enables your app to robustly remember all its states even if the browser page is reloaded / refreshed! Set initial values safely to Metamatic states using initState function that only sets those values that don't already exist in the state. This is useful because using initState function protects values from being overwritten when the browser is refreshed: a page reload causes all initState calls to be re-executed. Luckily, initState won't touch any existing values at all and so the app will maintain exactly the same state it had before the page reload occurred!

### Version 1.6.9: calling updateState to a non-defined state now possible

Version 1.6.9 makes **updateState** function more usable. It can be now called also on a state that is not yet defined at all. In such case, it just initializes the state like *setState* function.

### Version 1.6.8: Fixed bug in retrieving items from session storage

A bug that prevented retrieving items from session storage has been fixed.

### Version 1.6.5: Functions renamed for more clarity

To improve code readability, *update* function has been renamed to **updateState**, *obtain* function to **getState**,  *store* function to **setState** and *clear*
function to **clearState**. Version 1.6.5 won't be backwards compatible with earlier versions! 

### Version 1.6.3: Support for external nested storages removed

Support for external nested containers has been at least temporarily removed since there are no obvious use cases for such scenarios. 
A critical bug that was present in version 1.6.2 has been fixed.

### Version 1.6.2: Support for localStorage and sessionStorage based persistency strategies added

Metamatic now supports *localStorage* and *sessionStorage* based container persistency strategies. That is particularly useful when creating an app
that must remember its states even after browser page reload. By default, Metamatic now uses localStorage as default persistency strategy. You can change
the persistency type by calling Metamatic's configuration functions **useLocalStorage**, **useSessionStorage** and **useMemoryStorage**.

### Version 1.5.7: Clear any Metamatic state with "clear" function

Metamatic now provides a practical **clear** function that allows clearing any state with leaner code than previously calling *store* with an empty parameter object.

### Version 1.5.5: Obtain safely a clone of any Metamatic state with "obtain" function

Metamatic now provides **obtain** function for safely retrieving any states from the Metamatic state container. This method provides an additional pathway
for implementing supernova-bright logic inside your web app.

### Version 1.4.6: Abstract away data containers with update and store functions 

Metamatic proudly introduces the groundbreaking data store functions **update** and **store**. With these functions, you can forget data stores alltogether
and concentrate on states only. A paradigm shift in deed! Also old *updateState* function has been renamed to **updateStore** and *observe* has been renamed to **observeStore**.

### Version 1.4.0: "observeStore" function allows to preconfigure listener states in advance

*connect* and *connectAll* functions now  set the listener's state retrospectively from the state container if such was defined. In a state container it is now possible to use ~~observe~~ **observeStore**  function to mark a state inside store for observation. 
When a state is under observation, it will be automatically fired every time when a listener signs up to listen for it. 

### Version 1.3.4: "updateStore" function for easily updating container states and broadcasting changes

Since version 1.3.4, you can update a state in the state container and dispatch that state with only one line of code. Write very efficient state-container
aware code with ridiculously few lines of code!
   
### Version 1.2.8: Better way to connect and disconnect objects 

Since version 1.2.8, you can register *any* component by passing *this* reference to **connect** function. The Metamatic Framework now internally injects
a unique ID to each registered component so the user doesn't need to know about IDs.
