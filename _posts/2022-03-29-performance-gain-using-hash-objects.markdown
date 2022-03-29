---
layout: post
title: Optimizing Peformance with Hash Objects
date:   2022-03-29 06.41.39 +0300
categories: metamatic
---

When did the humanity experience the golden age of software code performance
optimization? I would give me biased answer, it was in the 1980's. 
No, I don't have any hard data to support this claim. I just remember how
games kept evolving on some very limited hardware, back in those days 
of my childhood.

## The golden years of code optimization

That was the age of 8-bit personal computers with truly limited memory.
In Finland, the gaming computer of every school boy was the iconic
[Commodore 64](https://en.wikipedia.org/wiki/Commodore_64) with only 64 kilobytes of memory.
The Commodore era lasted about one decade before the market was finally
saturated with the next generation 16-bit computers.
 
What is truly remarkable, is just how much the software improved
during those long ten years. The games written for Commodore 64
toward the end of the era were true master pieces of software code
optimization. It's just mind blowing how complex games emerged 
for that whiny piece of hardware!

## Importance of code optimization today

In today's abundance of computer hardware and super efficient 
CPUs, it appears that software of our time is often quite 
lousily crafted, at least seen from the performance perspective.

There's no lack of articles on so many forums promoting
software "paradigms" that are quite disastrous for the CPU
performance. It may appear like vanity even to bother to look at performance,
but the reality will eventually hit hard when you have to
create software that has to process huge quantities of data.

Then it becomes very evident that quite many hyped coding techniques
just don't serve well anymore, and you will find yourself 
spending huge piles of money paying for CPU time to compensate for
lavish code.

## How to optimize?

It turns out, often the best approach to optimize code does not 
involve introducing another framework X to magically "fix everything".
Instead, the important thing to do is to have a look into the code at 
the fundamental level and ask the question, "what does this code really do". 
Although this might sound boring, I will say that going back to basics is powerful!

## Lists versus hashes

Now, time to compile this article into something practical and have a look 
at how lists are very commonly being accessed in almost any software and 
how some tightly-sitting coding habits can be dealing devastating blows to code perfomance! 

I'll use Ruby language for the example,
but the principle applies to every other modern programming language,
such as Python, JavaScript, Java etc. 

Let's find an entry from an array using the convenient array find method:

{% highlight ruby %}
fruit = ["apple", "orange", "banana", "kiwi"].find { |fruit|
  fruit.start_with? "k"
}
{% endhighlight %}

In the example above, we find the first fruit from the list of fruits
that starts with "k" (you'll get "kiwi").

That's very nice. But how does it perform when you have some more entries
in your list? Let's find out!

{% highlight ruby %}

class ArrayFindPerformanceTest

  def self.create_huge_array
      array = []
      1000000.times.each { |index|
        array.push("a-#{index}")
      }
      array
  end
  
  def self.test_find_method
      array = create_huge_array
      array_find_start = Time.now.to_f
      entry = array.find { |entry|
        entry == "a-999984"
      }
      array_find_finish = Time.now.to_f
      array_find_duration = array_find_finish - array_find_start
      puts array_find_duration
  end
end

ArrayFindPerformanceTest.test_find_method

{% endhighlight %}

On my machine, I am getting durations around 0.15 seconds to find
my entry from the list of one million strings. Let's stop here for a second and think about this a little. 
First of all, why on earth would you do this and filter arrays at all in your code?

This kind of a situation may occur, for example, when you execute a database query
and then further filter the data in your code. You might want
to do so because sometimes you need to apply conditions
that are too complex or impossible to express in the database query
only.

Okay, then the other question. To spend 0.15 seconds to comb through 
one million entries, is it good or bad? It certainly beats hands down any librarian,
at least a librarian without a PC, of course. But the truth is that if your code has to
spend those 0.15 seconds iterating lists all over again, that innocent tiny glitch 
will turn into a monster that slowly eats up your master piece 
and keeps you waking up sweaty in the middle of the night!

Let's imagine a situation that you have to make multiple searches
on the array in hand. So let's extend the test method a bit and wrap the array
into a Hash object before finding our entry:

{% highlight ruby %}

hash = array.reduce({}) { |hash, entry| 
  hash[entry] = entry
  hash
}

hash_access_start = Time.now.to_f
result = hash["a-999984"]
hash_access_finish = Time.now.to_f

hash_access_duration = hash_access_finish - hash_access_start
puts "hash access duration: #{hash_access_duration}"
puts "hash access is #{array_find_duration / hash_access_duration} times faster!"

{% endhighlight %}

I just found out that placing the list into a hash object was a really good idea.
My PC tells me that getting my object of interest from the hash was almost 30 000
times faster than finding it from the list!
