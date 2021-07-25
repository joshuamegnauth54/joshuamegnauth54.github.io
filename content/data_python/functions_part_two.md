+++
title = "Functions in Python II: Evaluation strategy and mutability"
description = "How are arguments handled in Python?"
date = 2021-07-13
updated = 2021-07-25
[taxonomies]
tags = ["Python", "Tutorial", "Scraping", "Evaluation strategy"]
authors = ["Joshua Megnauth"]
+++

Arguments are inputs that are passed into functions. But how are arguments passed? Do functions receive copies of arguments? Does Python pass references everywhere? What _is_ a reference anyway?

Well, dear reader, let us delve into the crazy world of Python's evaluation strategy.

# What is an evaluation strategy?

An evaluation strategy outlines how arguments are passed into functions.

Programming languages such as Rust or C allow programmers to specifically label whether parameters are accepted by value or reference. Python, as well as other scripting languages, is opaque in terms of argument handling.

Python (and the rest) are designed around reducing the cognitive load on programmers. The tradeoff is that certain concepts are less immediate because scripting languages elide complexity. Python's evaluation strategy _feels_ a bit quirky even though it's a bit Spartan, like the language tends to be in general.

Argument semantics is fundamentally an issue of ownership and where data live in memory. Data may be created in one location but need to be accessed in another location. Those variables may need to be modified (mutated) as well.

For example, take a look at this small script displays an [xkcd](https://xkcd.com) comic using [Pillow](https://pillow.readthedocs.io/en/stable/).

```python
from requests import session
from PIL import Image
import io

XKCD_URL = "https://xkcd.com/{}/info.0.json"


def pull_xkcd_comic(comic_id):
    # Ignoring error handling for brevity

    with session() as sess:
        xkcd_json = sess.get(XKCD_URL.format(comic_id)).json()
        print_xkcd_metadata(xkcd_json)

        # Retrieve and return image data as bytes
        image_bytes = sess.get(xkcd_json["img"]).content
        return image_bytes


def print_xkcd_metadata(xkcd_json):
    num = xkcd_json["num"]
    month = xkcd_json["month"]
    year = xkcd_json["year"]
    title = xkcd_json["title"]
    transcript = xkcd_json["transcript"]

    print(f"XKCD comic #{num} was released on {year}-{month}\n\n"
          f"{title}: {transcript}")


def display_xkcd_comic(image_bytes):
    # The image is already encoded so I have to wrap it
    # in a BytesIO object.
    with Image.open(io.BytesIO(image_bytes)) as im:
        im.show()


if __name__ == "__main__":
    # Little Bobby Tables
    image_bytes = pull_xkcd_comic(327)
    display_xkcd_comic(image_bytes)
```

`pull_xkcd_comic()` accepts an integer that refers to an xkcd comic. 327 is one of my favorite comics on SQL injections. Anywho, within that function I download metadata on the comic as JSON which is then stored into a dictionary (`xkcd_json`).

That dictionary is passed to another function, `print_xkcd_metadata()`, which prints the metadata. Control is returned to `pull_xkcd_comic()` after `print_xkcd_metadata()`. The actual image for the comic is downloaded using a link from the dictionary from earlier.

Next, the image data as a PNG which is stored as an bytes buffer is passed to `display_xkcd_comic()`. The bytes array is then passed to the constructor (another function) of a class, [BytesIO](https://docs.python.org/3/library/io.html#io.BytesIO) which wraps the buffer into a stream.

Finally, the stream is parsed by Pillow.

Our data are passed around across multiple functions. Incredibly, the process outlined above elides all of the functions internally called by the libraries.

# References and values

Python's evaluation strategy is to call by object reference. I'll first discuss calling by value and by reference before discussing how Python handles parameters. Like other programming blogs, I'll take a polyglot approach to demonstrate both conventions due to Python's differences.

The language I'll use for demonstration, [Rust](https://www.rust-lang.org/), should look somewhat like Python for these simple examples but you may skip them if they're difficult to understand. I'd rather write code in a real language than construct my own pseudo-code or write invalid Python. Both would be more confusing than helpful, in my opinion.

Parameters that are passed by value are copied or moved into the function. Data that are copied exists at the original location in memory as well as within the function (barring optimizations and whatnot). Modifying the data in one location doesn't change the data in the other because the values are duplicated.

```rust
fn modify_i32(mut number: i32) {
    number += 42;
}

fn main() {
    let number = 14;
    modify_i32(number);
    assert_eq!(number, 14);
}
```

The value of `number` (a 32 bit signed integer) is copied into `modify_i32()`. The original hasn't changed at all! Also notice that `number` is still valid after calling `modify_i32()` because the value wasn't moved into the function.

Arguments that are moved are only valid within the function (that is, the function "owns" the value of the data). In other words, the data are moved into the function as well as likely to a new location in RAM, such as a new stack.

```rust
pub struct Person {
    name: String,
    // Because everyone wants lots of cats.
    number_of_cats: u128,
}

fn print_human(person: Person) {
    println!(
        "Random human {} has {} cat companions!",
        person.name, person.number_of_cats
    );
}

fn main() {
    let josh = Person {
        name: "Josh".to_owned(),
        number_of_cats: 14725251969,
    };

    // The value of "josh" is moved into print_human because Person cannot be copied.
    print_human(josh);
    // This doesn't work:
    // println!("Hello {}!", josh.name);
}
```

`Person` is a type that can't be trivially copied. Trivially copyable types don't own resources, such as pointers to memory or file handles.

We can take references to a `Person` or move the value to a new memory location. The function `print_human()` accepts a `Person` by value which in this case is a move.

Parameters that are passed by reference exist at the original location in memory but the function or struct borrows access to the value.

```rust
fn print_human_ref(person: &Person) {
    println!(
        "Random human {} has {} cat companions!",
        person.name, person.number_of_cats
    );
}

fn main() {
    let josh = Person {
        name: "Josh".to_owned(),
        number_of_cats: 14725251969,
    };

    // The value of "josh" is borrowed by print_human_ref instead this time.
    print_human_ref(&josh);
    // This WORKS now:
    println!("Hello {}!", josh.name);
}
```
Okay, this should be the last of the Rust code. _Maybe._ No promises.

The ampersand indicates that `print_human_ref()` takes a reference to a value rather than moving the data. The `person` argument in the function refers to the memory location containing the value of a Person struct.

The function borrows access to the data which means that the value is still valid once `print_human_ref()` ends. The value exists at the original location in memory as well.

## Call by object reference and Python

Python's evaluation strategy passes objects by the value of the reference. Thus, as you can tell from the Python example in the first section, the objects are still valid across function calls rather than being moved or copied.

With that said, "call by object reference" is different than _call by reference_. Essentially, Python copies the value of the reference to an object into a function. The value of a reference is a number that represents a location in memory. You can check this out yourself using [id()](https://docs.python.org/3/library/functions.html#id).

So, let's untangle that a bit. My Rust examples were more complicated than Python because Rust has different goals (systems programming). Programmers specify exactly how arguments are passed in as well as are able to create types that are trivially copyable.

As a result, Rust programmers often consider the lifetimes of their variables as well as the appropriate scheme for their parameters. For example, moving resources can constrain lifetimes and reduce indirection.

Python elides all of that complexity (again, different goals) by copying the reference for values of objects, always. This means that:

* Everything in Python is an object, including primitive types such as integers
* Python's evaluation strategy is harder to explain than I thought
* Python's evaluation strategy is easier to understand in practice than explain

# Immutable types

At this point, my dear readers may wonder why I focussed on another programming language and explained concepts that are ostensibly useless for Python. Trivially copyable values, such as integers and floats, save memory and can be optimized easily especially if they're immutable.

An integer "object" seems counterintuitive. A class would introduce indirection for instances stored on the heap as well as a penalty for dynamic dispatch.

```python
# Returns True?!
isinstance(int, object)
```

However, Python handles this gracefully by treating primitive types as well as strings as immutable. The Python interpreter, such as CPython, can cache instances of these immutable types or optimize them in other ways.

```python
# Example of caching
a = 14
b = 14
assert(id(a) == id(b))
```

Let's first take a look at immutable primitives.

```python
def modify_number(i):
    # Doesn't modify the ORIGINAL i
    i += 42

def append_meow(s):
    # Doesn't modify the ORIGINAL s
    s += "meow"

i = 42
modify_number(i)

# AHHH.
assert(i == 84)

s = "Cat!!! "
append_meow(s)

# OH NO.
assert(s == "Cat!!! meow")
```

Neither of the two functions above modify the _original_ values. `modify_number()` accepts a number and adds 42 to that number. The value of `i`'s reference is copied to the function as usual. However, numbers are immutable so the interpreter **does not** modify the value referenced by `i`. `append_meow()` is a similar case.

Python strings are immutable. The line:
```python
s += "meow"
```
...returns a new string with "meow" appended whose reference is then assigned to `s`.

Likewise, `modify_number()` returns a new `int` instance.

Is this confusing? Of course it is! Let's take a look at how this works step by step (more or less).

```python
def maybe_mutate(i):
    print(f"Address of i pre-mutation [in function]: {id(i)}")
    i += 42
    print(f"Address of i post-mutation [in function: {id(i)}")
    return i

i = 14
print(f"Address of i pre-mutation [outside function]: {id(i)}")
j = maybe_mutate(i)
print(f"Address of i post-mutation [outside function: {id(i)}")
print(f"Address of j (return val) post-mutation [outside function: {id(j)}")
```

```
Address of i pre-mutation [outside function]: 94853837223264
Address of i pre-mutation [in function]: 94853837223264
Address of i post-mutation [in function: 94853837224608
Address of i post-mutation [outside function: 94853837223264
Address of j (return val) post-mutation [outside function: 94853837224608
```

Pay attention to the numbers in each line as well as the code. You'll notice that `i` **is the same**:

* pre-mutation outside of the function
* pre-mutation inside of the function
* post-mutation **outside** of the the function

`i` refers to the same object outside of the function before the call as when it is passed into the function **before** mutation. `i` refers to the same object **after** the function call returns and **after mutation.**

`i` changes within the function to a new object when I attempted to mutate `i`. `j`, the value that is returned, is that same object. In other words, a new object was created when I added 42 to `i` and the reference to that object was copied into `i`. The original `i` still exists as you can see when the function returns.

As a side note, Python throws an error if you try to modify an immutable variable.

```python
# OH NO. A typo! 😱
name = "Josg"

# Eh, I'll just fix it the cool way with indexing! 😺
name[3] = 'h'
```

```
TypeError: 'str' object does not support item assignment
```

# Mutable types
Mutable types are modifiable in some way such as collections or instances of classes that hold some kind of state.

Like references to immutable values, mutable types are passed by copying the reference into the function. Modifying mutable types changes aspects of the value rather than producing a new value.

```python
def print_favorites(favorite_pokemon):
    print("Memory location in function: {}".format(id(favorite_pokemon)))
    for person, pokemon in favorite_pokemon.items():
        print(f"{person}'s favorite Pokémon is {pokemon}.")

favorite_pokemon = {"Josh": "Espeon"}
print("Memory location pre-mutation: {}".format(id(favorite_pokemon)))
favorite_pokemon["Jaqueline"] = "Drampa"
print("Memory location post-mutation: {}".format(id(favorite_pokemon)))
print_favorites(favorite_pokemon)
```

```
Memory location pre-mutation: 139810692989408
Memory location post-mutation: 139810692989408
Memory location in function: 139810692989408
Josh's favorite Pokémon is Espeon.
Jaqueline's favorite Pokémon is Drampa.
```

`favorite_pokemon` is a dictionary which is a mutable type. Adding Jaqueline's favorite Pokémon to the dictionary mutates the dictionary without returning a new object. The memory location of `favorite_pokemon` remains the same throughout the script.

Logically, the reference would change if we replaced `favorite_pokemon` with a new object.

```python
# Tiffany gets her own dictionary. 🙀
favorite_pokemon = {"Tiffany": "Psyduck and Snorlax"}
```

## What if I want to change a number/immutable value in a function?
The simple answer is that you don't. If you're passing in a number, `i`, you should return a new number than try to modify that single `i`.

Essentially, for all immutable types you should return a new instance of that type. I'm going to speak a bit broadly by saying that this pattern holds across other programming languages as well.

In Rust (okay I lied earlier), we could mutate an integer in a function if we really wanted to like so:

```rust
fn add_answer_to_i(i: &mut i32) {
    *i += 42;
}

fn main() {
    let mut i = 14;
    add_answer_to_i(&mut i);
}
```

This is a bit silly. On a 64 bit system, `add_answer_to_i()` takes a 64 bit pointer to a 32 bit number. The pointer required to modify `i` is _larger_ than `i` itself (barring optimizations)!

Idiomatic Rust like idiomatic C as well as idiomatic Python calls for simply returning a trivial, copyable value.

```rust
fn add_answer_to_i(i: i32) -> i32 {
    i + 42
}
```
And Python:
```python
def add_answer_to_i(i):
    return i + 42
```

An easy but ugly workaround is to wrap an immutable type in a mutable type, [such as a list](https://stackoverflow.com/q/15148496).

```python
def mut_add_bad(mut_i, j):
    mut_i[0] += j
```

Don't do that.

I actually wrote a wrapper around a number before stumbling upon the question above. Don't do that either.

```python
class OwnedNumber:
    __slots__ = "_number"

    def __init__(self, number):
        self._number = number

    def __int__(other):
        # Type cast to ease adding two OwnedNumbers as well as adding other
        # types to OwnedNumber
        if isinstance(other, OwnedNumber):
            return other._number
        else:
            return int(other)

    def __repr__(self):
        return "OwnedNumber({})".format(self._number.__repr__())

    def __str__(self):
        return str(self._number)

    def __add__(self, other):
        # Return new instance on add
        return OwnedNumber(self._number + int(other))

    def __iadd__(self, other):
        self._number += int(other)
        return self

    # Et cetera...
```
`OwnedNumber` is a mutable type that wraps around a number (or, really, anything that implements `add()` and `iadd()`). Try testing out `OwnedNumber` by adding integers or other `OwnedNumber`s to an instance.

Obviously, the "strategies" above are not great solutions at all. Python's immutable types are immutable for good reasons.

## What if I don't want a function to sully my mutable objects?
Languages with greater control over parameters allow programmers to pass types immutably. As we saw with Python, immutability is largely defined on the type level rather than determined when objects are created or passed as arguments.

Let's say we had a function to add my favorite Pokémon to a dictionary:
```python
# Yet another contrived function
def add_pokemanz(dict_to_sully):
    if not len(dict_to_sully.keys()):
        raise ValueError("Hey! You passed an empty dict. Rude.")
    for person, pokemon in zip(["Josh", "Jaqueline", "Tiffany"],
                               ["Espeon", "Drampa", "Psyduck and Snorlax"]):
        dict_to_sully[person] = pokemon
```
You see, I **really** want to add my (and Jaqueline and Tiffany's) favorite Pokémon to your precious dictionary. Okay, my function is contrived because I had trouble figuring out a simple, pithy example. Bare with me here!

You may work with an API that modifies a mutable variable. For example, a function may take an image and destructively transform the buffer. You may have a set of cleaning functions that alter a [pandas.DataFrame](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html). You may encounter a jerk who intends to sully your `favorite_pokemon` dictionary.

Cloning solves this problem with some caveats.

```python
my_favorites = {
    # Pokemon here
}

# This is now a new object via a shallow copy.
my_favorites_cpy = my_favorites.copy()

# The original dictionary is left untouched.
add_pokemanz(my_favorites_cpy)
```

# Shallow and deep copies

Copying or cloning the mutable `my_favorites` dictionary produces a new dictionary with the same references. Remember, Python stores references to values in variables. The keys and values of a dictionary are objects just like everything else in Python. The keys must be hashable which usually implies immutability as well.

Each dictionary value is copied into the new dictionary as well. If the type of the value is immutable then the new dictionary is functionally equivalent to the old dictionary. But what if the value is a mutable type, such as a list?

```python
from collections import defaultdict

def update_fav_pokemon(fav_pokemon, name, pokemon):
    # Mimic defaultdict if fav_pokemon is a dict but not a defaultdict
    if isinstance(fav_pokemon, dict) and not isinstance(fav_pokemon,
                                                        defaultdict):
        # Add an empty list with name as the key if name doesn't exist in
        # fav_pokemon. I.e., mimic defaultdict.
        if not fav_pokemon.get(name):
            fav_pokemon[name] = []
    elif not isinstance(fav_pokemon, defaultdict):
        raise ValueError("fav_pokemon should be a dictionary.")

    fav_pokemon[name].append(pokemon)

fav_pokemon = defaultdict(list)
update_fav_pokemon(fav_pokemon, "Jaqueline", ["Drampa", "Psyduck"])
update_fav_pokemon(fav_pokemon, "Tiffany", ["Espeon", "Dragonite"])
```

# I really want to mutate an immutable variable and...
Okay! _Okay._

Mutating an immutable variable is wildly unsafe and will likely cause [undefined behavior](https://en.wikipedia.org/wiki/Undefined_behavior).

Immutability isn't magic. Implementations 