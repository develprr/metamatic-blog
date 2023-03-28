---
layout: post
title: Time to Talk about Values
date:   2023-03-28 09.29.13 +0300
categories: Metamatic Systems
---

Let's get serious now. It's time to talk about values. 
Chances are high that you have heard this many times over. 
Corporations want to hire people who share their common values. 
Even sport clubs prefer to work with people who share their values.
Et cetera. Obviously, the values matter. A lot! 

The real question is, how do you know whether someone shares your values? Let's find out!

## A Practical Real-World Example

Let's say that an interstellar insurgency group called the Jedi Order 
in a far-away galaxy wants to hire another Jedi knight to turn the tide in their hopeless
fight against the Evil Empire. Now, there are many warriors in the galaxy 
who say they are a true Jedi while they are not. 

First of all, how do we know how closely the applicant's values are aligned with those of
the Jedi Order? Well, we can use the *force* to find it out, or we can use the...

## Cosine Similarity!

Looking at the Jedi knights that we already have (or had), we know
the average values of a typical Jedi knight:

```ruby
number of light sabers:       8
number of robotic pets:       2
opinion about empire (0-100): 0
opinion about rebels (0-100): 100
```

Placing the values into a vector, we get:

```
  jedi_order_values = [8,2,0,100]
```

And looking at an imaginary applicant 1's values, we get:

```ruby
  applicant1 = [3,1,9,82]
```
And the values for the other applicant:

```
  applicant2 = [4,0,13,92]
```

Which one of these two candidates is the better match?

Calculating the cosine similarity with the Jedi Order for both candidates:

```ruby
  similarity1 = CosineSimilarity.calculate jedi_order_values, applicant1 
  similarity2 = CosineSimilarity.calculate jedi_order_values, applicant2
```

We get result 0.993 for the first applicant and result 0.989 for the second candidate.
Clearly, we'll hire the applicant number 2.

I know that every reader is deeply impressed by this magic bullet at this point, 
so let's have a look under the hood and see how the engine ticks.

## Understanding the Cosine Similarity

To calculate the cosine similarity, just take the [dot product](https://www.metamatic.net/metamatic/systems/2023/03/18/whole-lotta-zippin-goin-on.html)
of the two vectors representing each applicant to be compared and divide it with the product of their norms. 
Actually, these kinds of things are so much easier to express in a programming languge:

```ruby
class CosineSimilarity
  
  def self.calculate(vector1, vector2)
    dot_product = DotProduct.calculate vector1, vector2
    norm1 = Norm.calculate vector1 
    norm2 = Norm.calculate vector2 
    dot_product / (norm1 * norm2)
  end

end

```
To further improve the process, you might want to apply coefficents to each
property depending on how much each value matters to *you* in this game of Jedi.

Next time, let's dvelve into the topic who is normal, who is not, 
why it matters and how to find out!

Cheers!
