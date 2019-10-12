---
layout: post
title:  "Object Oriented Coding Is Improved Functional Coding"
date:   2019-10-12 17:53:12 +0300
categories: metamatic
---

## The Failure of Functional Programming

In recent years there has been a flood of publications and posts that promote the so called "functional programming"
paradigm. These advocates try to convince its virtues at the expense of the object oriented paradigm.

In reality, anybody who claims that "the functional programming paradigm" is superior to the object oriented approach,
has most likely been misinformed in programming matters.

## OOP Is FP On Steroids

Most publications that attack the object oriented programmiog (OOP) paradigm make the mistake that they confuse some
very basic principles. Most notably, advocates of functional programming (FP) commonly base their claims on assumption
that the "immutability principle" is a specific feature of the FP alone which - in their view - cannot be applied in OOP at all.

In the following I'll show this isn't true at all. In deed it's possible to apply the immutability principle in OOP with ease.

## Applying Immutability Principle In OOP

In the example coming soon I'll use TypeScript programming language to highlight a powerful, yet very easy design
pattern to apply the immutability principle in OOP.

Let's implement an object oriented **LanguageCourse** object that allows to add language courses. The
*LanguageCourse* object must also be able to merge with other *LanguageCourse* objects.

This goal is best achieved by separating two aspects of an object into their own entities, namely, the data, and the object
itself, which will provide instance methods on top of the wrapped-in data object.

First, we decide that a LanguageCourse can have a number of students that will attend the course.

Let's create a student object. For simplicity, we'll define the student as a plain interface object at this point 
that won't have any instance methods:

```TypeScript
interface StudentData {
  name: string;
  age: number;
}
```

Now we define language course's data interface to describe what a language course is: 

```TypeScript
interface LanguageCourseData {
   name: string;
   description: string;
   attendees: StudentData[];
}
```

This means that a language course has a name, description and any number of attendees, which are students.
Now we are ready to go adding one student to a language course in a consistent OOP fashion. 
But not so fast! Let's remind ourselves first how it would be done in the FP scheme... 

## The Crappy Functional Way

If we were to use the highly touted "functional" design pattern to add one student to the course, 
we would write a standalone function for that:

```TypeScript
const addStudentToLanguageCourse = 
    (course: LanguageCourseData, student: StudentData): LanguageCourse => ({
    ...course,
    attendees: [
        ...course.attendees,
        student
    ]
});
```

The "functional" function above merges a student object into a language course object whereby the immutability 
principle is applied - the existing course object isn't changed at all, rather a new language course object
is created altogether by taking advantage of TypeScript's (and yes, JavasSript's as well) fantastic spread operator.
The resulting course object is like its predecessor except its attendees array will now contain the given student object.

Now, the common mistake is to think that the immutability principle is a feature of functional programming.
This assumption is simply false. The immutability is by no means private property of FP. You can have it in OOP as well.
 
I'll show in the next example that it's in deed possible to incorporate the immutability principle with the good old OOP way.
As a result we'll get code that is way more consistent than that "functional" approach which violently 
haul functions out of their natural homes - namely, classes - and make them stand separately, brutally torn out
of their contexts. Such cruelty inevitably leads to ugly long function names, such as **addStudentToLanguageCourse** instead
of their neat OOP counterparts like **addStudent**. Moreover, functions grow parameter-hungry since they always need
their subject to be passed as an argument instead of having them cosily available in their context, namely, the object instances
that host them.

This sort of "functional programming" may be sufficient for academic projects but it should not be applied in real
business!

## The OOP Style Immutable Objects

When creating immutability-loyal objects in the OOP way, the first thing to keep in mind is to separate data objects
into two distinct entities, which are the data - the real data that we care about - and the logic, which is the
OOP layer on top of the data. The OOP layer can be understood as a wrapper that provides methods to access, query and
modify those underlying data objects. By modifying I actually mean creating new versions of the existing ones following
the immutability principle. It's just that "modify" is easier to speak out than the latter long phrase to explain
that "modify" does not mean "mutate" here!

Now let's say goodbye to the medieval heresies of FP and brace for the second coming of the mighty OOP.

It's time to add the OOP layer!

```TypeScript
class LanguageCourse {

    private data: LanguageCourseData;
    
    constructor(data: LanguageCourse) {
        this.data = data;
    }
    
    public addStudent = 
        (student: StudentData): LanguageCourse => new LanguageCourse({
        ...this.data,
        attendees: [
            ...this.data.attendees,
            student
        ]
    })
    
    public getAttendeesCount = (): number => this.data.attendees.length;
    
}
```

Don't you see, we are now safely back in the OOP land and still applying the immutability principle?

I create now an object oriented language course that hasn't any attendees yet (empty array).

```TypeScript
const course: LanguageCourse = new LanguageCourse({
    name: "Learn ancient Alderaanian",
    description: "basic phrases",
    attendees: []
});
```

Now I have a course object. I add the first student:

```TypeScript
const firstStudent: StudentData = {
  name: "First-born Unicorn",
  age: 3121012
}

const courseWithStudent: LanguageCourse = course.addStudent(firstStudent);
```
Let's verify that there's now one student enrolled:

```TypeScript
const attendeesCount: number = courseWithStudent.getAttendeesCount();
console.log(atteendesCount);
// It's going to be 1 :)
```

OOP is back! :)