---
layout: post
title:  "Applying Immutability To Object Oriented Programming"
date:   2019-10-12 17:53:12 +0300
categories: metamatic
---

## About Programming Paradigms

In recent years there, has been a flood of publications about functional programming
paradigm. It can be seen as a challenger to more traditional "imperative programming" approach. It appears also that
sometimes "object oriented programming" is seen as the awkward drunk cousin 
of the entirely evil "imperative programming". You can't have one without the other at your party! 

Well, the case may actually not be that simple! It appears that there is often some confusion 
that "immutability principle" is a specific feature of the functional programming (**FP**) alone.
In the following I'll play around with objects and try to mix some immutability into object oriented programming approach.

Let's see if it works!

## Applying Immutability Principle in Object Oriented Programming

In the example coming soon I'll use TypeScript programming language to highlight a design
pattern that embeds some immutability into object oriented programming (**OOP**).

Let's implement an object oriented **LanguageCourse** object that allows to add language courses. The
*LanguageCourse* object must also be able to merge with other *LanguageCourse* objects.

This goal is best achieved by separating two aspects of an object into their own entities, namely, the data, and the object
itself, which will provide instance methods on top of the wrapped-in data object.

First, we decide that a LanguageCourse can have a number of students that will attend the course.

Let's create a student object. For simplicity, we'll define the student as a plain interface object at this point 
that won't have any instance methods:

{% highlight typescript %}
interface StudentData {
  name: string;
  age: number;
}
{% endhighlight %}

Now we define language course's data interface to describe what a language course is: 

{% highlight typescript %}
interface LanguageCourseData {
   name: string;
   description: string;
   attendees: StudentData[];
}
{% endhighlight %}

This means that a language course has a name, description and any number of attendees, which are students.
Now we are ready to go adding one student to a language course in a consistent OOP fashion. 

## The Immutable Way

If apply  to add one student to the course, 
we would write a standalone function for that:

{% highlight typescript %}
const addStudentToLanguageCourse = 
    (course: LanguageCourseData, student: StudentData): LanguageCourse => ({
    ...course,
    attendees: [
        ...course.attendees,
        student
    ]
});
{% endhighlight %}

The function above merges a student object into a language course object whereby the immutability 
principle is applied - the existing course object isn't changed at all, rather a new language course object
is created altogether by taking advantage of spread operator.
The resulting course object is like its predecessor except its attendees array will now contain the given student object.

## The OOP Style Immutable Objects

When creating immutability-loyal objects in the OOP way, the first thing to keep in mind is to separate data objects
into two distinct entities, which are the data - the real data that we care about - and the logic, which is the
OOP layer on top of the data. The OOP layer can be understood as a wrapper that provides methods to access, query and
modify those underlying data objects. By modifying I actually mean creating new versions of the existing ones following
the immutability principle. It's just that "modify" is easier to speak out than the latter long phrase to explain
that "modify" does not mean "mutate" here!

{% highlight typescript %}
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
{% endhighlight %}

I create now an object oriented language course that hasn't any attendees yet (empty array).

{% highlight typescript %}
const course: LanguageCourse = new LanguageCourse({
    name: "Learn ancient Alderaanian",
    description: "basic phrases",
    attendees: []
});
{% endhighlight %}

Now I have a course object. I add the first student:

{% highlight typescript %}
const firstStudent: StudentData = {
  name: "First-born Unicorn",
  age: 3121012
}

const courseWithStudent: LanguageCourse = course.addStudent(firstStudent);
{% endhighlight %}
Let's verify that there's now one student enrolled:

{% highlight typescript %}
const attendeesCount: number = courseWithStudent.getAttendeesCount();
console.log(atteendesCount);
// It's going to be 1 :)
{% endhighlight %}

## Evaluating the Results

These code samples prove that in deed it is possible to create object oriented
code that applies immutability principle to object-bound instance methods. Therefore,
it can hardly be claimed that mutability as is any argument to avoid object 
oriented programming. There is nothing to prevent you from placing
immutable functions into object instances.

Despite the fact that this technically works, it may not often 
be the most intuitive approach to take on. In practice,
Therefore I would be hesitant to largely take this kind of approach in a project, though.

How about using classes as mere namespaces for functions? 
I'll be soon back in the twighlight zone of mixing the deep waters of code!
