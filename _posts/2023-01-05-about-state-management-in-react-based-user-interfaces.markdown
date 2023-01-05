---
layout: post
title: About State Management Libraries in React-based User Interfaces
date:   2023-01-05 09.02.13 +0200
categories: Metamatic Systems
---

Anyone in user interface related software development may have noticed some intense 
discussions around the web about state management in web applications. 
There is also an ever-lasting debatte about what state management library you should be choosing
and whether you need one at all. There are multiple libraries to choose from,
such as Redux, Recoil, MobX, Flux et cetera. 

Believe me, once upon a time I even wrote my own state management library!

## Why do we need state management

Now let's dive in to the question - what are state management libraries,
and why would you need one! When creating modern user interfaces for a professional
portal, that has multiple pages, views or tabs and that needs to dynamically
interact with server side data, the challenges of keeping data in sync
between different components becomes obvious. Let's inspect this in detail! 

## An example use case

Let's think about an online store, where you have a list of items that
you have selected to your "shopping cart". Typically, 
these sites want to show the number of items in your cart at the top bar of the page 
or somewhere near the top right corner. They may want to show also the 
total price of your hand-picked items. At the same time,
there might be a side bar that lists the exact items currently on your 
cart. 

When you add an item to the cart, you expect that the number of total
items grows by one, yet the list of items should be also immediately updated.

So clearly we have here two components displaying two different data. Namely,
a summary box or icon showing the total number of your items,
and a list component showing the entire list of your items.

And obviously the summary data can be understood as a derivative of the list data - 
once you have the list data, you can derive also the total number of items out of that data. 

Therefore it is quite evident that these two data must be always in sync - 
when the list component is updated with the list data, there is no question that
the summary data showing the total number of items must be immediately updated as well.

Now, we are approaching the problem that explains why state management libraries
exist!

## Solving data syncing without state management 

The old-timer solution to keep components in sync, without state management,
would mean explicitly writing the code to update the components once data becomes available.

Let's express this with some pseudo code:

```
  itemList = loadDataFromServer()
  myItemListComponent.updateList(itemList)
  numberOfItems = itemList.length
  mySummaryComponent.updateTotal(numberOfItems)
  totalPrice = calculateTotalPrice(itemList)
  myTotalPriceIcon.updatePrice(totalPrice)
  
  // etc.. etc...
```

While this looks obvious, the task of updating components will become more cumbersome
when the application grows, and the risk increases that you forget to add the update invocation
for some component or that the code to sync between components isn't invoked at the right time,
or that removing some components breaks the application, or that slight changes in the data
breaks the chain, or these update methods exist in various places conflicting with each other:
The ways of breaking up the logic are many so this approach can turn the user interface
buggy, unreliable and hard to maintain over time.

Believe me, once upon a time I tried even this by myself!

## Solving data syncing with state management

To put it short, all state management libraries solve the above mentioned problems roughly
around the same principle. They provide a structure and tools to automate updating the UI
components upon changes in the data. They allow to *subsribe* components to *listen* for changes in 
the source data or its derivations, so once a component is coupled up with the state manager utitlity,
the state manager will ensure that once data is loaded from the server endpoint, 
all derivations (if needed) will be automatically calculated by the state management mechanism
and the components are updated.

## Which state management library to choose?

I will be back to this topic very soon and show you some practical examples 
on how different state management libraries can exactly be applied and how they compare.

Believe me even here! :)
