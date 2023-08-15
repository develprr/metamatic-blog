---
layout: post
title: Tame Pandas With Pydantic to Be King of the Jungle
date:   2023-08-15 8.07 +0300
categories: Python Pydantic Pandas
---

![type-checking]({{ site.baseurl }}/assets/pydantic-panda.png)

Imagine that you are [Tarzan](https://en.wikipedia.org/wiki/Tarzan) 
and you fight the horrible and furious Jungle Pandas and finally beat them. 
Now after you have tamed them you want to open their locked cage to feed them.

When you insert the key into the lock, you certainly don't want to break
the lock if you accidentally insert the wrong key into the lock hole.
Ideally, the key should not fit in at all if it isn't the right one!

Amazingly, also people in the city face similar situations every time when 
they write some software code. Naturally, when you implement your functions
you want to be sure that it's not possible to pass wrong parameters to your functions.

This couldn't be more true when you write complex data conversions
in Python language and use scientific libraries to achieve your data
conversion and analysis goals.

Therefore, let's have a look how to tame [Pandas](https://pandas.pydata.org/) 
with [Pydantic](https://pydantic.dev/) data validation!
    
For example, if we want to use Pandas library to create a two dimensional data frame,
it can add an extra layer of readability and clarity to your code
to wrap the essential data structures into your own wrapper classes that both safeguard
the types of your objects at every step along the way and provide
some instantiation and processing methods that are specific to your 
use case.

Here's an example of a MSDataFrame class that only exists
to make sure that the Pandas data frame it contains was instantiated the expected
way, and more importantly, *cannot* be instantiated any other
than the right way!

```Python
import pandas
from pydantic import BaseModel, ConfigDict, validate_call
from d2floatarray import D2FloatArray
from stringlist import StringList

class MSDataFrame(BaseModel):

  model_config = ConfigDict(arbitrary_types_allowed=True)
  
  dataframe: pandas.core.frame.DataFrame
  
  @staticmethod
  @validate_call
  def new_from_d2_float_array(d2array: D2FloatArray, columns: StringList):
    return MSDataFrame(**{
      'dataframe': pandas.DataFrame(d2array.ndarray.T, columns.list)
    })
``` 
Now have a look at the custom constructor *new_from_d2_float_array*. With a little help from Pydantic, this
custom data frame class makes sure that it can only be instantiated
using a two dimensional array of float numbers and a list of 
columns that is a string list object.

So clearly we have written here also our own little wrappers for two-dimensional 
arrays and simple string lists.

The great news with this approach is that we can ensure that the 
code execution won't actually ever even reach the point at all where
this constructor is being invoked if you didn't come up with the right parameters.

To clarify this, consider the following code:

```Python
float_array = D2FloatArray.new([[161,47], [170.5, 72.4], [185.5, 91]])
columns = StringList.new(['Weight', 12])
ms_dataframe = MSDataFrame.new_from_d2_float_array(float_array, columns)
```
In the example above, we actually need to first instantiate a valid
float array object (of type *D2FloatArray*) and a *StringList* object. 
Now that even these basic types are encapsulated in their own wrappers
as well, the last code line, the (erratic) instantiation of the actual MSDataFrame will
never even be attempted since the second line of code is already wrong
and will cause an exception. For the complete example, check out the 
[Git repository](https://github.com/develprr/utility).

I hope this example hightlights how using Pydantic in combination of
type-checking wrapper classes can move the point of failure closer to 
program's starting point - which is really important because
the earlier you can detect the errors the easier and faster it will be 
to actually implement effective and robust code!

By the way, as Tarzan lives in Africa and Pandas are Asiatic, it's unlikely
they ever meet. But as we are already at the end of the story 
there is probably not much that can be done about it anymore at this stage. Cheers!
