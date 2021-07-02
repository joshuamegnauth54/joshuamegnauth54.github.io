+++
title = "Writing functions in Python (DRAFT)"
description = "An explanation of writing functions in Python that covers the basics as well as intermediate issues."
date = 2021-07-02
updated = 2021-07-02
[taxonomies]
tags = ["Python", "Tutorial"]
authors = ["Joshua Megnauth"]
+++

# What are functions?

Whenever I learn a new programming language my first steps are as follows:
* Print "I like cats" to _stdout_.
* Write a function.

I've found that newbie programmers tend to be scared of writing functions despite calling functions constantly. Data analysts are especially wary of writing functions which, I wager, isn't helped by the languages we usually use: [Python](https://www.python.org/) and [R](https://www.r-project.org/). Data analysts are often taught to write code as a means to an end which often precludes writing our own functions. Thus, many data scripts are unwieldly, throwaway megaliths of code held together by tape and old glue. Good scripting languages like Python are ergonomic enough that newbie programmers could do a lot even if they forgo functions. However, functions are one of those tools that you can't go without.

Like loops, another bugbear, functions are initially difficult to write but become second nature after some practice. They're so useful that I'd feel naked without the ability to write functions. But what are functions anyway?

**Functions are reusable blocks of code that may accept zero or more parameters and return `None` or one or more values or objects.** Admittedly, defining functions didn't seem to make them less scary. Let's go deeper by looking at a basic, built in Python function to understand function design as well as gain an intuition as to why you'd want to write your own. Many of you may find the section below tedious. Feel free to skip it. I won't be mad...maybe.

## Bits of terminology

Here are a few basic terms just to make sure we're on the same page.

* Using a function is often referred to as **calling a function**
* Function inputs are also known as **parameters** or **arguments.**
* Outputs are also called **return values.**
* Functions associated with a class, such as [str.format](https://docs.python.org/3/library/stdtypes.html#str.format), are called methods.

Imagine we had a function, `mean()`, that takes in a list of floats and outputs a single float. We can say that `mean()` has one parameter, a list of floats, and returns a float.

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

# Writing functions
## A basic and boring example
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

Python's keyword `def` indicates a function definition (see? **def**inition!). The next element is my function's name: `say_hi`. Next up is the list of arguments. `say_hi` only takes in one argument, `name`, which is the name we're greeting with `print()`.

The colon, `:`, separates the function definition from the indented block of code of the function itself. Calling a function "jumps" to the code within the indented block from wherever the function was called. As you can see, the function's code is simply more Python rather than being anything special.

Finally, the argument, `name`, is a variable in scope for the function which means we can use `name` just like any other variable within the indented block. The print statement demonstrates this as `name` is used within `print()`.

Let's work through writing a few more functions because I don't really want to talk about `say_hi` anymore.

## Mean, variance, and standard deviation
Mean, variance, and standard deviation are simple formulas that are perfect for demonstrating writing functions. The functions are **composable** because variance uses the mean and standard deviation is the square root of variance. While means are simple to calculate, writing out the formula in line each time an average is needed is tedious and possibly error prone. We may want to add extra features to the mean calculation as well which would be difficult if we copy and pasted the line(s) of code throughout our program. What if we needed to implement one of the 9001 formula that use the mean in some way? Would we copy and paste the mean code each time we need, say, root mean squared error? Don't forget **D.R.Y.**!

(With that said, you totally shouldn't roll your own mean function in normal practice in lieu of using the `mean()` available from libraries like [NumPy](https://numpy.org/). These are just examples, okay?)

### Mean
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

`mean()` ramps up the difficulty a bit! `isinstance()` is a function that returns `True` if the first argument (an object) is one of the types in the second argument. Thus, `isinstance(array, Iterable)` is testing if the argument, `array`, is an `Iterable`. The next test checks if the array has at least one element. You can't take the mean of an empty object since `sum([])` is 0 and `len([])` is 0.

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

However, ensuring your functions return proper error messages when appropriate is important for debugging and general use. I'm not claiming that my checks above are the ideal, idiomatic way of writing `mean()`. They're useful to demonstrate error checks and the messages are more helpful than the defaults.

## What are parameters anyway?

We know that parameters are inputs that we pass into functions. The argument, `name`, is given the **object reference** of whatever we pass into the function.

## Checking parameters