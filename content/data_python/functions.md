+++
title = "Functions in Python I: Simple functions and argument handling"
description = "Why and how do we write functions in Python?"
date = 2021-07-02
updated = 2021-07-13
[taxonomies]
tags = ["Python", "Tutorial"]
authors = ["Joshua Megnauth"]
+++

# What are functions?

Whenever I learn a new programming language my first steps are as follows:
* Print "I like cats" to [stdout](https://en.wikipedia.org/wiki/Standard_streams).
* Write a function.

I've found that newbie programmers tend to be scared of writing functions despite calling functions constantly. Data analysts are especially wary of writing functions which, I wager, isn't helped by the languages we usually use: [Python](https://www.python.org/) and [R](https://www.r-project.org/). Data analysts are often taught to write code as a means to an end which often precludes writing our own functions. Thus, many data scripts are unwieldly, throwaway megaliths of code held together by tape and old glue. Good scripting languages like Python are ergonomic enough that newbie programmers could do a lot even if they forgo functions. However, functions are one of those tools that you can't go without.

Like loops, another bugbear, functions are initially difficult to write but become second nature after some practice. They're so useful that I'd feel naked without the ability to write functions. But what are functions anyway?

**Functions are reusable blocks of code that may accept zero or more parameters and return `None` or one or more values or objects.** Admittedly, defining functions didn't seem to make them less scary. Let's go deeper by looking at a basic, built in Python function to understand function design as well as gain an intuition as to why you'd want to write your own. Many of you may find the section below tedious. Feel free to skip it. I won't be mad...maybe.

## Handy terminology

* Using a function is often referred to as **calling or invoking a function/**
* Function inputs are also known as **parameters** or **arguments.**
* Outputs are also called **return values.**
* **Declarations** or **signatures** are the headers that outline a function. A function's name and parameters are part of the declaration.
* A function's **definition** is the body of code that is executed when invoked.
* Functions associated with a class, such as [str.format](https://docs.python.org/3/library/stdtypes.html#str.format), are called **methods**.
* **Instance methods** are called on a constructed object (an instance). **Class methods** don't require an instance but are associated with the type in some way.

Parameters and arguments are [technically different terms](https://stackoverflow.com/questions/1788923/parameter-vs-argument), but they're [used interchangeably](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html). Parameters are the variables that receive an argument. Arguments are the values passed into parameters.

Imagine we had a function, `mean()`, that takes in a list of floats and outputs a single float. We can say that `mean()` has one parameter, a list of floats, and returns a float. We can call `mean()` with `[14, 28, 42]`, an argument.

## Reusability

Let's say we want to write "I like cats" to _stdout_. To do so we'd obviously use Python's built in [print()](https://docs.python.org/3/library/functions.html#print) function. Python's documentation states that `print()` writes strings (or objects that can be represented as a string) to a stream where the stream is _stdout_ by default. So, all we really need to do is call `print()` with "I like cats."

```python
# 👇 Function call
print("I like cats.")
```

```
I like cats.
```

We know that we can use `print()` with different strings and objects. In other words, we're clearly not limited to printing "I like cats" and can also print "I like giraffes" as well. The function is general enough that we can and do use it constantly. Imagine how limited Python would be if `print()` were actually `print_i_like_cats()` instead. Everyone likes cats, but programmers likely have more to print than just that fact of life. Furthermore, programmers would have to write hundreds of functions for every little statement they'd wish to print and thus duplicate code and effort.

## Encapsulation

We've all used `print()` before but likely without considering the amount of work that goes into simply printing characters to a console. This is essentially the reusability of functions in a nutshell. Writing characters to a stream involves ensuring the input is valid as well as copying the input to the stream. The stream may be a file, special file (_stdout_), socket, et cetera. A buffer may be managed somewhere along the line. As a programmer, you (most likely) don't want to go through the hassle of manually printing characters.

The beauty of functions is clearly represented by `print()` which wraps layers of actions that are invisible to the user that we don't even have to understand. Do we need to understand how "I like cats" is eventually output to our terminal? For the most part, no. The `print()` function does **one action well** by virtue of its own lines of code as well as the other functions called along the chain.

Notice that we aren't calling a behemoth that writes to a stream, reads from a stream, parses data, manages memory, and more in a single function. The function is simply designed to ease writing to a stream. Invariably, `print()` calls other functions to delegate tasks such as actually writing to the stream, but those details are hidden from our view as callers of the function. Instead, we only have to worry about how we consume the function. The function's inner workings may change throughout Python versions but that doesn't usually affect how we use `print()`.

I'm belaboring my point here, but hopefully you can gain an intuition about functions in general from Python's intricate but humble `print()`.

## Parameters

Python's `print()` function also accepts zero or more parameters as our definition above states. We can pass multiple printable objects into the function, specify a string to separate each object, and also write to a different stream according to the docs.

```python
import sys
import time

# Printing a sequence of strings to standard error
print(*"your cat is hungry".title().split(),
      sep="! ",
      end="!\n",
      file=sys.stderr)
```

```
Your! Cat! Is! Hungry!
```

The code above prints a sequence of strings to standard error that are separated by the string `"! "`. `*"your cat is hungry".title().split()` looks complicated but is actually one of those nice, space efficient Python expressions. You don't have to understand it for this post but I'll explain anyway. The line of code calls a string method to set the string to title case followed by splitting that return value by whitespace and returning a list. That list is finally unpacked into separate strings. Thus, the `print()` function is effectively called with:

```python
print("Your", "Cat", "Is", "Hungry",
      sep="! ",
      end="!\n",
      file=sys.stderr)
```

We'll talk about this in a second after another related example.

```python
# Printing four different objects
awesome_animals = set(["cat", "giraffe", "duck", "bunny"])
favorite_pokemon = "Espeon"
bands_genres = {"Sonic Youth": "noise rock",
                "King Crimson": "progressive rock",
                "Godspeed! You Black Emperor": "post rock"}

print(awesome_animals, bands_genres, favorite_pokemon, time.localtime(),
      sep="\n\n")
```

```
{'bunny', 'cat', 'duck', 'giraffe'}

{'Sonic Youth': 'noise rock', 'King Crimson': 'progressive rock', 'Godspeed! You Black Emperor': 'post rock'}

Espeon

time.struct_time(tm_year=2021, tm_mon=6, tm_mday=24, tm_hour=4, tm_min=31, tm_sec=21, tm_wday=3, tm_yday=175, tm_isdst=1)
```

`print()` takes parameters that—surprise!—are all related to printing objects to a stream. `print()` conveniently abstracts away busy work which is evident from its parameters. `print()` is designed to ease writing different objects, so the parameters focus on allowing the programmer to pass as many different objects as they wish as well as control the output in some way such as by adding separators between the objects.

Notice that my first invocation in this section passes four strings as input (`"Your"`, `"Cat"`, `"Is"`, and `"Hungry"`). However, the second invocation passes four different objects: a set, a string, a dictionary, and a [struct_time](https://docs.python.org/3/library/time.html#time.struct_time). In both cases the function prints out what we expect without any additional processing. Lower level functions may expect a string which means we would need to manually call another function to get a string representation of those objects. `print()` handles that for us. This is an important point which I've repeated enough that you're likely bored of hearing it.

## Summary
Okay! That was a longer digression than I intended. The point of annoyingly looking at `print()` in detail was to provide an intuition about functions so that you start to think about writing your own.

`print()`...

* Is convenient
* Is easy to call (_id est_ the parameters aren't difficult to understand)
* Abstracts away boilerplate (that is, it saves you from manually writing each object to a stream)
* Generalizes an action so that it is reusable

In fact, programmers usually generalize these idioms (more or less) into **D.R.Y.: Don't Repeat Yourself.** Your own functions don't have to be as integral, elegant, and even useful as `print()` to benefit from D.R.Y. You may find yourself writing lines of code that look similar to lines of code you've written previously in the same project; these are awesome candidates for functions.

As an exercise, try thinking about other functions you've encountered in Python, libraries, or other programming languages. How are they convenient? Imagine writing code without these functions to test the points above.

# Writing functions - a basic and boring example
Let's jump into writing functions via a very boring, canonical example before moving onto something fun.

```python
def say_hi(name):
      print(f"Hi {name}! 😺😺😺")

# And calling say_hello:
say_hi("Josh")
```

```
Hi Josh! 😺😺😺
```
First, let's go over the syntax of writing a basic, one line function.

Python's keyword `def` indicates a function definition (see? **def**inition!). The next element is my function's name: `say_hi`. The list of parameters follows the name. `say_hi` only takes in one argument, `name`, which is the name we're greeting with `print()`.

The colon, `:`, separates the function declaration from the indented block of code of the function itself (the definition). Calling a function "jumps" to the code within the indented block from wherever the function was called. As you can see, the function's code is simply more Python rather than any special syntax.

Finally, the parameter, `name`, is a variable in scope for the function which means we can use `name` just like any other variable within the indented block. The print statement demonstrates this as `name` is used within `print()`.

Let's work through writing a few more functions because I don't really want to talk about `say_hi` anymore.

# Mean, variance, and standard deviation
Mean, variance, and standard deviation are simple formulas that are perfect for demonstrating writing functions. The functions are **composable** because variance uses the mean and standard deviation is the square root of variance. While means are simple to calculate, writing out the formula in line each time an average is needed is tedious and possibly error prone. We may want to add extra features to the mean calculation as well which would be difficult if we copy and pasted the line(s) of code throughout our program. What if we needed to implement one of the 9001 formula that use the mean in some way? Would we copy and paste the mean code each time we need, say, root mean squared error? Don't forget **D.R.Y.**!

(With that said, you totally shouldn't roll your own mean function in normal practice in lieu of using the `mean()` available from libraries like [NumPy](https://numpy.org/). These are just examples, okay?)

## Mean
```python
from collections.abc import Iterable

def mean(array):
      # First check if array is an Iterable since we need a sequence of numbers.
      if not isinstance(array, Iterable):
            raise TypeError("Array should be a sequence of numbers.")

      # Fail on empty Iterables
      if len(array) == 0:
            raise ValueError("You can't call mean() with an empty array, bud.")

      # And finally check if every value in array is either an int or a float.
      if not all(map(lambda x: isinstance(x, (float, int)), array)):
            raise TypeError("You have to pass in an array of numbers.")

      # After all of that we can finally return the mean of array.
      return sum(array)/len(array)
```

`mean()` ramps up the difficulty a bit! Python is dynamically typed, so I wrote a few (arguably superfluous) checks to constrain `array`.

`isinstance()` is a function that returns `True` if the first argument (an object) is one of the types in the second argument. Thus, `isinstance(array, Iterable)` is testing if the argument, `array`, is an `Iterable`. The next test checks if the array has at least one element. You can't take the mean of an empty object since `sum([])` is 0 and `len([])` is 0.

The last check is more complex due to the lambda, so I'll cover it when I discuss anonymous functions. Essentially, I'm testing if each element is a number.

Python would throw an exception even if we didn't have these checks. For example:

```python
def one_liner_mean(a):
      return sum(a)/len(a)

one_liner_mean([14, 28, "I broke", "your function", 25 ** 500, "YOU NERD.", 42, 56])
```
To which Python complains:
```
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

However, ensuring your functions return proper error messages when appropriate is important for debugging and general use. `say_hi` from earlier doesn't perform any error checking at all. Callers can provide nonsensical arguments that execute while being incorrect. While this doesn't matter for a function as trivial and useless and `say_hi`, a simple check can be lifesaving for other code!

I'm not claiming that my checks above are the ideal, idiomatic way of writing `mean()`. I'm not using custom exceptions, and the final check seems gratuitous. With that said, they're useful to demonstrate error checks and the messages are more helpful than the defaults.

The final line of code is a **return statement** that returns the result of calling `sum()` on `array` divided by the length of the array (which is the mean of course).

## Return statements
As mentioned earlier, functions can return `None`, one, or more values. Values can be anything including objects, such as `List`s or even other functions; and primitive values such as `int` and `float`. Returning a value is as simple as `return value` which you can see from `mean()`. Returning multiple values is as simple too: `return value1, value2, valueN`.

A function's documentation will list possible return values as well as their types. For example, take a look at the documentation for Python's [built in mean() function](https://docs.python.org/3/library/statistics.html#statistics.mean) in the statistics library. The documentation says that the function "Return\[s\] the sample arithmetic mean of `data` which can be a sequence or iterable" (`data` is the function's argument). As a side note, exceptions are not return values.

Functions are allowed to have multiple return statements depending on the the flow of execution. Let's say, for whatever reason, we wanted a function that calculates the mean but also returns the intermediate work if necessary. The function should also return `numpy.nan` if the caller goofed and passed in an empty array.

```python
import numpy as np

# Different name and a new argument 👇
def mean_intermediate(array, show_work):
      # First check if array is an Iterable since we need a sequence of numbers.
      if not isinstance(array, Iterable):
            raise TypeError("Array should be a sequence of numbers.")

      # Return nan on empty arrays
      if len(array) == 0:
            # 😮 New return statement!
            return np.nan

      # And finally check if every value in array is either an int or a float.
      # Added more type checks to isinstance 👇
      if not all(map(lambda x: isinstance(x, (float, int, np.float64, np.integer)), array)):
            raise TypeError("You have to pass in an array of numbers.")

      # 👀 NEW 👀 🙀
      # After all of that we can finally calculate the mean of array
      # We'll first store the intermediate work in case we need it
      array_sum = sum(array)
      array_len = len(array)
      array_mean = array_sum/array_len

      if show_work:
            return array_sum, array_len, array_mean
      else:
            return array_mean
```

Now we have three return statements! The first returns a "not a number" sentinel because a zero length array would lead to a division by zero. The two return statements at the end of the function branch depending on the argument `show_work`, which should be a `bool`. If the caller wants to see the "work" then the function returns the summation of `array`, the length of `array`, and finally the mean. The return value is implictly a tuple. Callers can receive the return value as a tuple or unpack (destructure) the tuple into multiple parts.

```python
thread_rng = np.random.default_rng(42)

# Fake data
fake_ages = thread_rng.integers(6, 21, size=20)

print(f"Mean: {mean_intermediate(fake_ages, False)}")
print(f"Mean with work as a tuple: {mean_intermediate(fake_ages, True)}")
```

```
Mean: 13.5
Mean with work as a tuple: (270, 20, 13.5)
```

The first call to `mean_intermediate` only returns the mean as is clear from the function implementation. The second call returns the tuple of the intermediate work as well as the mean. Callers can store that tuple or unpack it as mentioned above. Let's take a look at that now.

```python
# Unpacking a tuple into individual variables
array_sum, array_len, array_mean = mean_intermediate(fake_ages, True)
print(f"{array_sum}/{array_len} = {array_mean}")

# Ignoring individual return values
array_sum, _, _ = mean_intermediate(fake_ages, True)
```
```
270/20 = 13.5
```

The first line of code unpacks the single tuple into three separate variables which is clearer and easier to use. The second `mean_intermediate` call also unpacks the tuple but ignores some of the return values by assigning them to `_`, a special Python variable that typically stores the last result of a function call.

To be even more explicit we can return a named tuple instead. Named tuples are just tuples with field names for explicitness.

```python
from collections import namedtuple

# Declare a named tuple called MeanCalc
MeanCalc = namedtuple("MeanCalc", ["summation", "length", "mean"])

def mean_intermediate(array, show_work):
      # Error checking left out for brevity.
      array_sum = sum(array)
      array_len = len(array)
      array_mean = array_sum/array_len

      if show_work:
            # Returning a named tuple instead 😺
            return MeanCalc(array_sum, array_len, array_mean)
      else:
            return array_mean

print(f"As a named tuple: {mean_intermediate(fake_ages, True)}")

# Unpacking works the same 😺
array_sum, array_len, array_mean = mean_intermediate(fake_ages, True)

# We can also store the tuple then access each field in a nicer way.
mean_work_ages = mean_intermediate(fake_ages, True)
print(f"Mean: {mean_work_ages.mean}")
```

```
As a named tuple: MeanCalc(summation=270, length=20, mean=13.5)
Mean: 13.5
```

Returning named tuples certainly looks nicer!

## Default arguments

Let's say that instead of `mean_intermediate()` we simply had a single `mean()` that encapsulated a lot of functionality. Besides returning the intermediate work, our new `mean()` could calculate the average by precluding `nan`s or calculate the mean across a dimension. The declaration could look something like this.

```python
def mean(array, by, show_work, remove_na):
      pass
```

Callers would have to pass in an array, the axis to calculate across, and two bools to indicate whether the intermediate work should be returned as well as what to do about `nan`s. For example:

```python
fake_ages_mean = mean(fake_ages, "flat", False, True)
```

Our new mean would take only four arguments, but what if we expanded the function further? Calculating a mean via our function (or other functions) has reasonable defaults. We can assume that most callers would expect to receive the mean without intermediate work. In other words, most callers would use `mean()` by `show_work` set to `False`. We can also assume callers would usually want the flattened mean of an n-dimensional array. Filling in the last three arguments doesn't seem like a lot of work, but what if you were deep within a project where you had to exhaustively type out or copy and paste the functional call only to change the first argument?

You'd probably eventually write your own convenience function.

```python
def meen(a):
      """COME ON MAN!!!"""
      return mean(a, "flat", False, True)
```

Python has a nifty feature to absolve this tedium. **Default arguments** are preset parameters that reduce function call complexity. Parameters with default arguments are referred to as **keyword arguments** or **named parameters**. Parameters without defaults are known as **positional arguments** and must be provided when the function is called. Some Python libraries expose truly gnarly public functions (neither hyperbole nor an insult). For example, take a look at [Seaborn](https://seaborn.pydata.org/)'s [pairplot](https://seaborn.pydata.org/generated/seaborn.pairplot.html) function. Would you really want to fill in each of those parameters by hand for each call?

I am not a masochist—except concerning certain video games—so I'm in favor of reasonable default argument use. Default arguments are great for providing a flexible public API while delegating work to private functions which is likely how some of the longer functions handle default arguments.

Anyway, adding default arguments to a function is as simple as setting `argument=default_value` in the declaration.

```python
# Default arguments are set with an equals.
def mean(array, by="flat", show_work=False, remove_na=True):
      pass

# Now mean is easier to call! 👍
_ = mean(fake_ages)
```

The new declaration above adds default arguments for `by`, `show_work`, and `remove_na`. Calling `mean()` by simply passing in an array defaults to taking the mean of a flattened array without showing work but _with_ removing `nan`s. Notice that I didn't provide a default argument for `array`. `array` is a positional argument that callers must always provide. Logically, providing a default for `array` doesn't make sense because the entire purpose of the function is to calculate a mean for `array`.

Add a default argument for `show_work` to `mean_intermediate` as an exercise.

### Positional, keyword, and default arguments

Arguments match up by position for function calls which you already know even if you haven't heard it formally. In other words, calling a function like so:

```python
def some_cool_function(first_arg,
                       second_arg,
                       third_arg,
                       fourth_arg,
                       fifth_arg):
      pass

# Arguments are passed in by position and thus match up to each arg
some_cool_function(1, 2, 3, 4, 5)
```

...passes in 1, 2, 3, 4, and 5 to the `first_arg`, `second_arg`, `third_arg`, `fourth_arg`, and `fifth_arg` parameters by position.

I can also call `some_cool_function` by manually labeling each argument. I can also refer to them out of order.

```python
# In order
some_cool_function(first_arg=1,
                   second_arg=2,
                   third_arg=3,
                   fourth_arg=4,
                   fifth_arg=5)

# Out of order! 🤯🤯
some_cool_function(second_arg=2,
                   first_arg=1,
                   fourth_arg=4,
                   third_arg=3,
                   fifth_arg=5)

# Mixed positional and keywords 🤯🤯🤯🤯
some_cool_function(1,
                   2,
                   third_arg=3,
                   fourth_arg=4,
                   fifth_arg=5)
```

The three code snippets above show different ways of calling `some_cool_function`. You can explicitly refer to each argument by name as shown in the first and second calls. This is known as keyword arguments or named parameters as mentioned above.

The second call refers to the arguments out of order. The third call mixes positional as well as keyword arguments. Notice that I supplied the positional arguments first in the mixed call. Keyword arguments **always appear after positional arguments.**

```python
# This is ILLEGAL and you're going to be arrested by the Python police.
some_cool_function(1,
                   2,
                   third_arg=3,
                   4,
                   5)
```

Allowing mixing positional and keyword arguments without mandating that positional arguments come first would lead to ambiguous calls. There is no "right" way to handle mixing the order of positional and keyword arguments. Likewise, you can't provide a positional argument and a keyword argument that refers to the same parameter. This is easier to understand with an example.

```python
# Do you hear that?
# It's the hiss of the Python police's blue-yellow siren.
some_cool_function(1,
                   first_arg=1,
                   second_arg=2,
                   third_arg=3,
                   fourth_arg=4,
                   fifth_arg=5)
```

`first_arg` is provided twice which is also illegal. I can't think of a reason why you'd supply an argument twice in the same call so it's for the best that Python throws an error here.

**Do these rules seem confusing or verbose?** Don't worry! After coding in Python for a few days you'll likely "get" these rules without having to memorize them. I personally haven't really thought about them till writing this tutorial. The main takeaways are:

* Callers can specifically supply arguments by name
* Positional arguments always come before keyword arguments

Default arguments can be overridden as both positional or keyword arguments.

```python
# Override show_work only
_ = mean(fake_ages, show_work=True)

# Override by and show_work by position
_ = mean(thread_rng.random((5, 3)), "row", True)
```

## Variance and standard deviation via composition

We're at the end of the first part of my brief tutorial on writing functions in Python. For our last steps, we'll implement variance and standard deviation while demonstrating composability. I'll use a slightly modified version of the first mean function from earlier on to limit some of the busy work since we're crafting an A.P.I.

Recall that the original mean function looked more or less like so:

```python
def mean(array):
      # First check if array is an Iterable since we need a sequence of numbers.
      if not isinstance(array, Iterable):
            raise TypeError("Array should be a sequence of numbers.")

      # Return nan on empty arrays
      if len(array) == 0:
            return np.nan

      # And finally check if every value in array is either an int or a float.
      if not all(map(lambda x: isinstance(x, (float, int, np.float64, np.integer)), array)):
            raise TypeError("You have to pass in an array of numbers.")

      # After all of that we can finally return the mean of array.
      return sum(array)/len(array)
```

Variance relies on the mean so we can call `mean` directly in our new function.

```python
# Finally! A different topic.
def variance(array):
     array_mean = mean(array)
     return sum([(xi - array_mean)**2 for xi in array])/len(array)
```

Standard deviation is the square root of variance thus we can call `variance()`.

```python
# Reusing variance() is eco friendly.
def std(array):
     return variance(array)**(1/2)
```

Since `array` is passed down to `mean()`, any errors would also be raised in `variance()` as well as `std()`. However, what if we were implementing another formula, such as residual sum of squares, where we'd want `ŷ`and `y` to both be arrays of numbers as well? Simply copying and pasting the code is unacceptable for reasons discussed earlier. We'd increase code bloat as well as reduce maintainability.

An easy solution is to write another function for the error handling and dispatching the public facing (A.P.I.) functions to internal implementations. This is another set of ideas that sounds more difficult in words than in code.

```python
# Notice the underline before the names of the functions below! 👍
def _is_array_of_nums(array):
    # First check if array is an Iterable since we need a sequence of numbers.
    if not isinstance(array, Iterable):
        raise TypeError("Array should be a sequence of numbers.")

    # Return nan on empty arrays
    if len(array) == 0:
        return np.nan

    # And finally check if every value in array is either an int or a float.
    # This check may be gratuitous.
    if not all(map(lambda x: isinstance(x, (float,
                                            int,
                                            np.float64,
                                            np.integer)),
                   array)):
        raise TypeError("You have to pass in an array of numbers.")

def _mean(array):
    # Return the mean of array (unchecked)
    return sum(array)/len(array)


def _variance(array):
    # Unchecked variance
    array_mean = _mean(array)
    return sum([(xi - array_mean)**2 for xi in array])/len(array)


def _std(array):
    # Unchecked standard deviation
    return _variance(array)**(1/2)
```
Python lacks language features for _private_ or _protected_ functions and methods. Conventionally, functions with a preceding underscore, such as `_mean()`, are taken to be implementation details that normal users shouldn't access.

I defined four private functions. I moved the error checking logic into the first, `_is_array_of_nums()`. The other three functions perform the calculations at hand without error checking.

```python
def mean(array):
    _is_array_of_nums(array)
    return _mean(array)


def variance(array):
    _is_array_of_nums(array)
    return _variance(array)


def std(array):
    _is_array_of_nums(array)
    return _std(array)
```

The final set of functions dispatches each calculation to the private function. Error checking is only performed once.
