---
layout: post
title: Landing on planet DynamoDB 
date:   2023-03-31 06.38.00 +0300
categories: Metamatic Systems
---

DynamoDB is an intriguing concept. It introduces a single-table design model.
This means that you could - and should - be stuffing all different kinds of objects
into one database table. This may sound crazy for anybody arriving from
the relational planet, on which you would place different types
objects into their own tables. 

## What would you do on the relational planet

For example, if you had a one-to-many relationship, something like an employee and organization,
clearly you would be placing the employees into their own table and organization
into their own table. You would implement a one-to-many relationship between
by adding a foreign key field into the employees table that points to the ID 
field of the organizations table. You would likely to have a similar approach
even with some document databases, such as MongoDB. With MongoDB,
you would join the tables at the query level by performing an aggregation
that joins the tables with a lookup operation.

## What you do on DynamoDB planet

However, when dealing with DynamoDB, you won't be doing anything like that.
In DynamoDB, you'll be happily injecting all objects that you think could
be related to each other into one big table. This is because there is 
buried-in secret: Deep down in its heart, DynamoDB is splitting your
multi-purpose giant table into many *partitions* which actually are all
their separate tables. But with DynamoDB, you don't care what those
partitions are - your attention is on how to define keys - partition keys and sort keys.
After all, those keys define how the database is split. 
Let's study what this really means!

## Implementing a many-to-many relationship on DynamoDB

Let's have a look how to practically implement a many-to-many relationship
with DynamoDB. Let me implement a DynamoDB model that has many schools
that can have many pupils. However one pupil can be a member of many schools.
And then there are also courses! Each pupil can attend many courses
and each course will have many pupils. A complete mess!

Can't wait to solve it!½

At work, I would do this with TypeScript programming language. 
However, since it's good to have hobbies that are not work-related, 
I'll use Ruby also in this example. Let's go!

# Defining the school

Since DynamoDB is *unstructed* from the schematic point of view,
the more important it is to define your "schemas" in the code
that manages the DynamoDB. 

First things first! Your DynamoDB handlers must import
AWS SDK's DynamoDB library in order to communicate with DynamoDB:

```ruby
require 'aws-sdk-dynamodb'
```

And of course you will need the actual client to communicate with your DynamoDB:

```ruby
$client = Aws::DynamoDB::Client.new(region: 'eu-north-1')
```

Psst! Be sure to sign up to AWS first and configure the AWS cli tool to make
this work. When you are done, 

we will be creating schools, courses and pupils. They
all will have unique IDs. So let's implement a helper method 
at this point to generate random IDs. You could also use a particular library for this, 
but for this example using a combination of current time rand method is just good enough.
With this, you'll get unique IDs. Well, you could theoretically get a duplicate ID but it's extremely unlikely.
Good enough for this demo!

```ruby
def generate_random_id
  "#{Time.now.ti}:#{rand(9999999999)}"
end
```

Now, we are good to go to define the School class.
The School class will be the utility to create School items
and attaching pupils and courses to them:

```
class School

  # All school related methods will be put inside this class
  
end
```

Now, we are going to fill the school with all the methods 
that it will need to become a happy school full of life and joy.

We partion the database by schools. This means that deep down in the
heart of DynamoDB, each school will form a separate internal table.
But for us, it will be all just one table. For us, it will 
be just one "schools" table, split with partition keys.


Now we add a method into the school class to define the syntax 
of our table's partition key. With this key, a particular school 
can be fetched from the database:

```ruby
def self.get_partition_key(school_id)
  "SCHOOL##{school_id}
end
```

And then we need to define the sort key. The sort key will be used
to fetch a particular school's metadata, meaning properties such as its address, desciption etc.:

```ruby
def self.get_sort_key(school_id)
  "#METADATA##{school_id}"
end
```

Having these fundaments in place, we can add the fundamental  
method to actually create school items into our database:

```ruby
  def self.create_one(school_name, school_id)
    school_id = school_id || generate_random_id
    school_item = {
      table_name: :schools,
      item: {
        PK: get_partition_key(school_id),
        SK: get_sort_key(scool_id),
        name: school_name,
        school_id: school_id
      }
    }
    
    $client.put_item school_item
    
  end
```

Gosh, time flies! I just realized it's already 7:00 AM and I need to take
one pupil to school right now. I'll be back soon!
