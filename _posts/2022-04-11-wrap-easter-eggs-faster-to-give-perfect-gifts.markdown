---
layout: post
title: Wrap Easter Eggs Faster to Give Perfect Gifts 
date:   2022-04-11 06.19.00 +0300
categories: metamatic
---

## Starting with Legos 

Let's say that you have a basket full of Legos and you want to
build something. It may be really annoying to find the right pieces
from the basket. Ideally, you would buy a handy toolbox shelf
containing lots of small transparent boxes or pockets, 
so you could sort out your Legos and put them into their
dedicated pockets based on their type.

Now, if you have ever really tried it you will say it's just
an insane task to do that - you are not going to be doing
much else than sorting out Legos if you go for it. You'll be doing it
only to realize that they are all mixed up again at the next occasion.

So much about Legos. Let's talk about something merrier, 
it's already Easter for God's sake!

## About easter eggs, then

The same problematic applies to all aspects in life - 
you can achieve some reasonable cost saving and get things done
faster if you do some preparational organization first - but it is also very
important to take into account the cost of the preparation -
if the cost of pre-organization is high in relation to the expected gain
in the actual target activity, it may fundamentally affect the calculus
on how much sense it makes to optimize in the first place.

For example, if you want wrap Easter eggs into fancy paper and it is a
major hassle, you may want just to leave it altogether and focus on something that
is actually fun. So let's forget about easter eggs.

## About coding, then

Last time I wrote about the blessings of wrapping arrays into 
hash objects when trying to make your code execute faster. However,
I entirely skipped a very important aspect - the cost.

I wrote earlier that it can be up to 30 000 times faster to find entries
from an array if you wrap the array first into a hash object.

Well, if you spend a lot of time wrapping an array into a hash object
and then use it just once, that may not be very useful. But the situation
changes dramatically if the array is to be accessed multiple times.
 
Now the essential question is, how much does it actually cost to wrap an array into 
a hash object? Let's find out!

## Measuring duration

Now that time is money and CPU time is expensive, you will actually
be measuring time here. So let's make the fundamental element,
a method to measure duration of any given procedure. For the clarity
of expression, I use the Ruby language here again:

{% highlight ruby %}
  def measure_duration(proc)
    start_time = Time.now.to_f
    proc.call
    end_time = Time.now.to_f
    end_time - start_time
  end
{% endhighlight %}

The method takes in any procedure, executes it and returns the 
duration in seconds it took.

## Laying the Easter eggs

To measure the time it takes to wrap items, let's say Easter eggs,
into a hash object, we of course need the eggs first.
So let's lay them:

{% highlight ruby %}
  def lay_easter_eggs
     easter_eggs = []
     1000.times.each_with_index { |index|
      easter_eggs.push({
        :id => index,
        :name => "egg-#{index}",
        :weight => rand(100)
      })
     }
     easter_eggs
  end
{% endhighlight %}

So we got now one thousand easter egg objects to play with. Nice!

## Wrapping the Easter eggs

To measure how much time it takes to wrap an array of easter eggs
into a hash object, you need to actually decide how you wrap them.
There are many different approaches to wrap any gifts, 
so let's examine them in detail. 

You can use the each-loop:

{% highlight ruby %}
  def wrap_easter_eggs_to_hash_by_name_using_each_loop(eggs)
      hash = {}
      eggs.each { |egg|
        hash[egg[:name]] = egg
      }
  end
{% endhighlight %}

You can also use the traditional for-loop:

{% highlight ruby %}
  def wrap_easter_eggs_to_hash_by_name_using_for_loop(eggs)
    hash = {}
    last_index = people.length-1
    for i in 0..last_index do 
      egg = eggs[i]
      egg[egg[:name]] = egg
    end
    hash
end
{% endhighlight %}

The sleek but slow immutable reduce-loop:

{% highlight ruby %}

  def wrap_easter_eggs_to_hash_by_name_using_immutable_reduce_loop(eggs)
    eggs.reduce({}) { |hash,egg|
      hash.merge egg[:name] => egg
    }
  end
{% endhighlight %}

Not to forget the boosted-up variant of the latter, the mutable reduce-loop:

{% highlight ruby %}
  def self.wrap_eggs_to_hash_by_name_using_mutable_reduce_loop(eggs)
    eggs.reduce({}) { |hash,egg|
      hash[egg[:name]] = egg
      hash
    }
  end
{% endhighlight %}

## Let's measure them all for once!

And not only once, but some ten thousand times. Namely,
it turns out that if you measure something like this once or twice or thrice,
you will be getting wildly different outcomes. So to really
measure how long time it takes to measure a procedure, let's run the 
wrappers 10 000 times and take the average of them!

{% highlight ruby %}
def test_performance_of_hash_wrapping_methods
    eggs = lay_easter_eggs

    immutable_reduce_loop_time = 0
    mutable_reduce_loop_time = 0
    each_loop_time = 0
    for_loop_time = 0
    
    run_count = 10000
    run_count.times.each {
    
      immutable_reduce_loop_time += measure_duration proc {
        wrap_easter_eggs_to_hash_by_name_using_immutable_reduce_loop eggs
      }
      
      mutable_reduce_loop_time += measure_duration proc {
        wrap_easter_eggs_to_hash_by_name_using_mutable_reduce_loop people
      }
    
      each_loop_time += measure_duration proc {
        wrap_easter_eggs_array_to_hash_by_name_using_each_loop people
      }
      
      for_loop_time += measure_duration proc {
        wrap_easter_eggs_to_hash_by_name_using_for_loop people
      }
    }
    
    puts %{
      Wrapping array using immutable reduce loop took #{immutable_reduce_loop_time/run_count} ms.
      Wrapping array using mutable reduce loop took   #{mutable_reduce_loop_time/run_count} ms.
      Wrapping array using each loop took             #{each_loop_time/run_count} ms.
      Wrapping array using for loop took              #{for_loop_time/run_count} ms.
    }
end

{% endhighlight %}

## The results

I am getting the following results when running the test:
{% highlight ruby %}
  Wrapping array using immutable reduce loop took 0.0025952794075012205 ms.
  Wrapping array using mutable reduce loop took   0.0002493852853775024 ms.
  Wrapping array using each loop took             0.00020642895698547362 ms.
  Wrapping array using for loop took              0.00021767702102661133 ms.
{% endhighlight %}

## Conclusion

It appears that each-loop is definitely the fastest way to wrap an array of
easter egg objects into a hash object. Just in case if you ever need to!

Happy Easter!
