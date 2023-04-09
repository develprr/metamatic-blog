---
layout: post
title: Agile Data Management with MongoDB 
date:   2023-04-09 05.01.00 +0300
categories: Metamatic Systems
---

Hey visitor, welcome to the future! Do you come from the 
dark ages of the last decade where giant relational databases ruled?
Don't worry, it's year 2023 now and we have moved on: we have something 
better for you. Check out document databases! 

The best document databases that we have here in the future allow you to 
insert different kinds of complex objects into your ~~tables~~ collections 
without the headache of casting fixed schemas on your tables, and still have all the benefits
of an SQL database - indexing, sophisticated queries, joins and still supporting
the timeless relational model. Isn't it fantastic to live in the future?

Let's dive deeper!

But let me ask first, 

## Is the relational model going away?

Now, this is deep. Are the relational databases going
away? Are they some ancient artifacts of the past? I don't think so. Let me explain why!
A database that supports the relational database model is a database
that supports relational concepts like one-to-one, one-to-many and many-to-many.

Let's think about it. Isn't it a really natural way to see the world? Actually,
can you think about the world without relations at all?

For example, think about a school. A school can have many pupils. Typically, 
a pupil belongs to one school. That's a typical one-to-many relation. A course
can be attended by many pupils. Yet one pupil may be attending many courses. This 
is a many-to-many relation.

Relational databases organize object similarly
into object types that are stored in their own groups, or "tables". These
databases offer a simple and powerful concept to organize the relations 
between these objects in a way that is very natural, mimicking the way 
we see the world as well. Therefore, the relational databases will hardly go away at all!

## In a way, MongoDB is a relational database

It's quite common to think that a relational database equals an SQL database.
SQL is a query language used to make database queries to relational databases.
I challenge this view because MongoDB, not being an SQL database since it doesn't
support SQL queries, makes it possible to combine data in a way very
similar to typical relational databases. Therefore, I would still call MongoDB a relational
database - since it makes possible to form relations between objects similarly
to SQL-based relational databases. If you wish!

## Getting hands dirty

That's all about philosophy for now. I'll show next how to make the most of the 
relational databases with MongoDB, while still enjoying the fruit of flexibility of a document
based database. Let's get started and implement...

## One-to-many and many-to-many models with MongoDB!

Another practical example of a many-to-many scenario in the real world would
be a school that can have many courses and pupils. Yet each pupil can attend 
many courses and each course will be attended by many pupils.

To start with, let's describe the scenario first with TypeScript models. 
This makes sense, because we will be soon loading data from MongoDB into those
TypeScript objects:

```TypeScript
interface ISchool {
  _id: string;
  name: string;
  courses?: ICourse[];
  pupils?: IPupil[]
}

interface IPupil {
  _id: string;
  schoolId: string;
  name: string;
  school?: ISchool;
  courses?: ICourse[]
}

interface ICourse {
  _id: string;
  schoolId: string; 
  name: string;
  school?: ISchool;
  pupils?: ICourse[];
}
```

Note that I added a prefix "I" to each model here! Why did I do it?
I am just using here a convention that separates data objects from their
actual handlers. If I don't mix methods into the data objects but keep the
data objects separate and plain, it will be a lot easier to serialize them
directly into JSON objects. And JSON objects can be quite handily transferred
over REST (or other) APIs over the Internet. Not to forget how this approach can boost
your unit tests when you have a clear separation between data objects and their handlers.

To put it short, I added the "I" prefix to these data objects so I can
have separate handler objects such as *School*, *Pupil* and *Course*. 

Next time, let's check out how to build and keep relationships between these 
objects on database level when working with MongoDB.

## Finding schools, courses and students

Now, let's find some schools, pupils and courses. I implement a handler called "School"
that contains the functions to do that. TypeScript is a fantastic programming language
because it supports nicely both a more "functional" like approach, yet also providing
structures that allow a more traditional "object-oriented" approach, familiar to those
coming from planet Java. I'll play a bit here and mix in something from both worlds.

You could use class instances, static classes or even namespaces to implement the handlers 
to persist our objects into MongoDB. I will use namespaces here this time. 

Have a look at my *School* handler that finds a school by ID and merges school's pupils and courses
into the result with a lookup:

```TypeScript

namespace School {

  export const findByIdWithCoursesAndPupils = async (schoolId: string): 
	Promise<ISchool[]> => await MongoClient.aggregate("schools", [
	  {
		$match: {
		  _id: schoolId
		}
	  },
	  {
		$lookup: {
		  from: "courses,
		  localField: "_id",
		  foreignField: "schoolId",
		  as: "courses"
		}
	  },
	  {
		$lookup: {
		  from: "pupils",
		  localField: "_id",
		  foreignField: "schoolId",
	 	  as: "pupils"
	    }
	  }
  ]);
}

```

# Populating collections with data

For our example, let's add two imaginary schools, and populate the first one 
with courses and pupils. Finally, we add them all into MongoDB with handler utilities
that we just implemented:

```TypeScript

  // Defining the first school:
  const schoolMalmo: ISchool = {
	_id: "malmo",
	name: "Malmö Grundskula Svärje"
  };
 
  // Defining another school:
  const schoolHermosa: ISchool = {
	_id: "hermosa",
	name: "Escuela Hermosa Colombiana"
  };

  // Placing the chools into an array:
  const schools: ISchool[] = [schoolMalmo, schoolHermosa];

  // Adding a pupil to the first school:
  const pupilOne: IPupil = {
	_id: "elev1",
	schoolId: schoolMalmo._id,
	name: "Elva Tolva"
  };

  // Adding another pupil to the first school:
  const pupilTwo: IPupil = {
	_id: "elev2",
	schoolId: schoolMalmo._id,
	name: "Åckso Någonannan"
  };

  // Assinging the pupils into an array: 
  const pupils: IPupil[] = [pupilOne, pupilTwo];

  // Adding two courses to the first school:
  const courses: ICourse[] = [
	{
	  _id: "engelska",
	  schoolId: schoolMalmo._id,
	  name: "läser vi engelska mykke vidare"
	},
	{
	  _id: "matematiken",
	  schoolId: schoolMalmo._id,
	  name: "matematiken för alla"
	}
  ];
```

Next, let's implement DB injectors for all our entities that we created:

```TypeScript
namespace School {
 
  const collectionName: string = "schools";

  export const insertMany = (schools: ISchool[]) => MongoClient.insertMany(collectionName, schools);

}

```

## One-to-many with MongoDB

And write similar type-checking insert methods for other classes. If you are lazy you can also
just use the generic method ```MongoClient.insertMany(collectionName, entries)``` directly
but then you lose type-checking. With TypeScrpt, type-safe with boilerplate or relax-style
with compact code, the choice is yours!

Alright, I cut the crap now and add the items. Just do it!

```TypeScript
await School.insertMany(schools);
await Course.insertMany(courses);
await Pupil.insertMany(pupils);
````

And finally, get the results: 

```TypeScript
await School.findByIdWithCoursesAndPupils("malmo");
```

The database returns our school with all its courses and pupils
just the way we wanted:

```JSON
{
    "_id": "malmo",
    "name": "Malmö Grundskula Svärje",
    "courses": [{
        "_id": "engelska",
        "schoolId": "malmo",
        "name": "läser vi engelska mykke vidare"
    }, {
        "_id": "matematiken",
        "schoolId": "malmo",
        "name": "matematiken för alla"
    }],
    "pupils": [{
        "_id": "elev1",
        "schoolId": "malmo",
        "name": "Elva Tolva"
    }, {
        "_id": "elev2",
        "schoolId": "malmo",
        "name": "Åckso Någonannan"
    }]
}

```

## Many-to-many with MongoDB

In our example, a course should be able to have many pupils
and one pupil should be attending many courses. It can be done by adding 
an association object, a design pattern well known in relational databases:

```TypeScript
interface IPupilCourse {
  _id?: string;
  courseId: string;
  pupilId: string;
  courses?: ICourse[];
  pupils?: IPupil[];
}
```

Now, adding a pupil to a course: 

```TypeScript

const pupilCourse: IPupilCourse = {
  _id: `${courseMatematik._id}:${pupilOne._id}`,
  courseId: courseMatematik._id,
  pupilId: pupilOne._id,
}

await PupilCourse.insertMany([pupilCourse]);

```

And finally, let's implement a query to find all pupils by course ID:

```TypeScript

export namespace PupilCourse {

  const collectionName: string = "pupils-courses";

  export const insertMany = (pupilCourses: IPupilCourse[]) => MongoClient.insertMany(collectionName, pupilCourses);

  export const findPupilsByCourseId =  async (courseId: string) => await MongoClient.aggregate(collectionName, [
    {
      $match: {
        courseId
      },
    },
    {
      $lookup: {
        from: "pupils",
        localField: "pupilId",
        foreignField: "_id",
        as: "pupils"
      }
    }
  ]);

}
```

The ease of a schemaless document database peppered with agile 
queries comparable to SQL databases - that's MongoDB! 

I'll be back with more examples from the exciting world of managing data with MongoDB.

Cheers!

See you soon!
