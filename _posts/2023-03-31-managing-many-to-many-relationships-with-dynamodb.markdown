---
layout: post
title: Landing on planet DynamoDB 
date:   2023-03-31 06.38.00 +0300
categories: Metamatic Systems
---

[DynamoDB](https://docs.aws.amazon.com/dynamodb/index.html) is an intriguing concept. 
It introduces a single-table design model. This means that you could - and should - be stuffing all different kinds of objects
into one database table. This may sound crazy for anybody arriving from
the relational planet, where you would be placing different types
objects into their own tables. 

## What would you do on the relational planet

If you were to use a relational database, such as [PostGRE](https://www.postgresql.org/) 
or [MySQL](https://www.mysql.com/), and you had to implement a one-to-many relationship, 
something like an employee and organization, you would be 
placing employees and organizations into their own separate tables. 
You would establish a one-to-many relationship between them
by adding a foreign key field into the employees table that points to the ID 
field of the organizations table. You would likely to have a similar approach
even with some document databases, such as MongoDB. With [MongoDB](https://www.mongodb.com/),
you would join the tables at the query level by performing an aggregation
that joins the tables with a lookup operation.

## What you do on DynamoDB planet

However, when dealing with DynamoDB, you won't be doing anything like that.
In DynamoDB, you'll be happily injecting all objects that you think could
be related to each other into one big table. This is because there is a 
buried-in secret: deep down in its heart, DynamoDB is splitting your
multi-purpose giant table into many *partitions* which actually are all
their separate tables. But with DynamoDB, you don't care about what those
partitions are - your attention is on how to define keys - partition keys and sort keys.
After all, those keys define how the database is split. 
Let's study what this really means!

## Implementing a many-to-many relationship on DynamoDB

Let's have a look how to practically implement a many-to-many relationship
with DynamoDB. Let me implement a DynamoDB model that has many schools
that can have many pupils. However one pupil can be a member of many schools.
And then there are also courses! Each pupil can attend many courses
and each course will have many pupils. A complete mess...

Can't wait to solve it!

At work, I would do this with TypeScript programming language. 
However, since it's good to have hobbies that are not work-related, 
I'll use Ruby also in this example. Let's go!

# Preparations for the "education business"

Since DynamoDB is *unstructured* from the schematic point of view,
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

Psst! Be sure to sign up to AWS first and configure the [AWS CLI](https://aws.amazon.com/cli/) tool to make
this work. 

When you are done, we will be creating schools, courses and pupils. They
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

# Creating and finding schools

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

Having these fundaments in place, we can add the base method 
to actually create school items into our database:

```ruby
def self.create_one(school_name, school_id)
  school_id = school_id || generate_random_id
  school_item = {
    table_name: :schools,
    item: {
      PK: get_partition_key(school_id),
      SK: get_sort_key(school_id),
      name: school_name,
      school_id: school_id
    }
  }
  $client.put_item school_item    
end
```

If you insert a school into a database, you'd better know also
how to get it back!

```ruby
def self.find_by_id(school_id)
  $client.get_item({
    table_name: :schools,
    key: {
      PK: get_partition_key(school_id),
      SK: get_sort_key(shool_id)
    }
  })
end
```

# Adding courses to schools

No school is a real school if it doesn't offer any courses! We create
another class to handle courses:

```ruby
class Course

  # add all course-related methods inside this class

end
```

Now that our database is already designed to be partitioned by the schools,
the courses won't need their own partition keys. The courses will be
inserted into schools, that already partition the main table. So let's jump right away
into defining the sort key structure for the course object:

```ruby
def self.get_sort_key(course_id)
  "COURSE##{course_id}"
end

```

Since every course has a one-to-many relationship with the school that it
belongs to, so clearly the school ID is needed when creating a course - 
so we can tell the database to which school the new course belongs to:

```ruby
def self.create_one(school_id, course_id, name)
  $client.put_item({
    table_name: :schools,
    item: {
      PK: School.get_partition_key(school_id),
      SK: get_sort_key(course_id),
      name: name
    }
  })
end
```

Similarly, you will need to pass also the school_id when you want to find
a course by ID - the school ID specifies from which partition the course
should be looked for:

```ruby
def self.find_by_id(school_id,course_id)
  $client.get_item({
    table_name: :schools,
    key: {
      PK: School.get_partition_key(school_id),
      SK: get_sert_key(course_id)
    }
  }).item
end

```

Next time, we will be going big and see how to find all courses of a school
with DynamoDB. Take care!
