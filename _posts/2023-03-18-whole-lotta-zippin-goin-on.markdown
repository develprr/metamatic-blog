---
layout: post
title: There's a whole lotta zippin' goin' on
date:   2023-03-18 18.23.25 +0200
categories: Metamatic Systems
---

![pine forest]({{ site.baseurl }}/assets/pine-forest.png)

Once again I walked in a snowy pine forest somewhere in southern Finland and
looked at all those trees around me. I came to think just how much genetic data
must be stored inside those trunks - there's a whole lotta zippin' goin' on!

As we know, the annoying closing mechanism that too many jackets and
pants are equipped with is called a zipper. You almost never think about how exactly it works
until it breaks and makes you look like a moron.

In the nature, however, genetic material is zipped together all over again
when male and female gameophytes join and form the complete DNA of almost every tree.

Interestingly, there are also trees in the computer science data forest.
And it is even more astonishing that those trees - namely, decision trees - 
also zip data together at a very fundamental level when you look carefully enough.

It turns out that the decision tree, which is an essential algorithm to 
calculate statistical likelyhoods, relies on cosine similarity calculation
deep down in its core. And you can't get cosine similarity without dot product calculation. 
To calculate the dot product, the mighty zip function is a *must* for every self-respecting
data lumber man. Now we are slowly getting to the point!

## Putting stuff together with zip function

Before I show you how to use zip function to calculate dot products, 
I'll start with an example about using the zip function in Ruby language 
to put together simple lists.

Let's say that we have boys and girls and we want to zip them together into couples.

So we have two arrays:
```ruby
girls = ["Suzanne", "Maria", "Laura", Eva"]
boys  = ["Simon", "Andy", "Tom", "Tim"]
```

Zip them together!

```ruby
couples = girls.zip boys
```

And we get an array of couples happily zipped together now (and ever after?)
with the power of the mighty zip function!

```ruby
[
   ["Suzanne", "Simon"],
   ["Maria", "Andy"],
   ["Laura", "Tom"],
   ["Eva", "Tim"]
]
```

To further modernize this algorithm, consider applying the *shuffle* method to get
some follow-up randomization and vibes, one-to-many or many-to-many relationships and even more!

## Do the dot

Getting over this example, let's have a look how to seriously apply the zip function
to calculate the dot product:

```ruby
def calculate_dot_product(array1, array2)
   array1.zip(array2).map { |v1, v2|
      v1 * v2 
   } .inject(:+)
end
```

You can now feed any array pair to calculate their dot product. For example:

```ruby
answer_to_everything = calculate_dot_product [4,2,1], [8,4,2]
```

Psst! The answer is 42.

Moving on from here, the road is wide open to so many interesting things to come.

I'll be back zipping around all over the place, ready or not!
