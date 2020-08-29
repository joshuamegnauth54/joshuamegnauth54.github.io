Josh’s Brief R Guide: Basics
================
Joshua Megnauth

  - [Introduction](#introduction)
  - [Vectors](#vectors)
      - [Defining variables](#defining-variables)
      - [Vectors are special](#vectors-are-special)
  - [Functions](#functions)
      - [Reassignment (and why you should avoid
        it)](#reassignment-and-why-you-should-avoid-it)

# Introduction

# Vectors

I’d like to keep this short guide practical. So let’s start with
variables and vectors\!

## Defining variables

``` r
ages <- c(29, 24, 10, 42, 65, 8, 56, 22, 15, 8)
```

The `<-` operator *assigns* the right hand side to the left side. The
[c()](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/c)
function combines values into a *vector.* Thus, the above code
*combines* 29, 24, 10, 42, 65, 8, 56, 22, 15, and 8 into a *vector*
named *ages.* Let’s take a look at our new vector:

``` r
ages
```

    ##  [1] 29 24 10 42 65  8 56 22 15  8

The vector is the basic unit in R. Vectors hold an arbitrary number of a
**single data type**. Keep that fact in mind for the future even if you
don’t understand what that means as of yet.

R, like other languages, defines a set of basic types. The numbers above
default to *numeric* which are numbers as you’d expect them to be: real
numbers. Some of R’s other basic types include characters, integers,
factors (nominal), logical (booleans), and complex. We’ll discuss these
types later.

You may define **as many variables** as your little R programmer heart
desires. Likewise, R is fairly liberal with variable names.

``` r
meow <- "I love cats and video games!"
```

The restrictions on variable names include: \* Names are restricted to
letters, numbers, periods, and underscores \* Names can’t be restricted
R keywords, like TRUE or FALSE \* Names must start with a letter,
period, or underscore

We can use **meow** and ***meows\_and\_nyas*** as names but not
**99meows**. Likewise, R throws an error if we attempt to name our
variable after the keyword *function*.

With that said…you probably shouldn’t name your variables *meow.* One
ought to name their variables pragmatically and descriptively.

``` r
# Decent name - short and descriptive
vg.prices <- c(59, 20, 15, 15, 4.99)

# Bad name - what are somenums? SAS humans do this for some reason.
somenums <- c(295951568, 2, .000000001, 42)

# Bad name - too verbose
selected_pokemon_josh_and_his_best_friend_really_likes_version_two_for_Rlang <- c("Espeon", "Drampa")
```

Naming variables is difficult—shoddy examples aside. Following a
canonical method of naming variables will certainly save you headaches
later. Trust me.

## Vectors are special

Back to the fun stuff.

Vectors are special data types that are designed to facilitate linear
algebra (mostly). Wait\! Don’t run\! Sorry fellow mathemaphobes. I
should’ve warned you ahead of time. We’ll scarcely look at any equations
here; I only had to mention linear algrebra due to **vectorized
operations.**

Statistics and linear algebra are intricately connected. Have you ever
calculated a mean? Well, that’s linear algebra. Sensibly, the primary
data type in R is *totally awesome* for math.

Vectors may be added to, subtracted from, multiplied by, divided by (et
cetera) other vectors. **By the way, a single number, character, et
cetera is a vector of one in R.** I’ll prove that statement before we
continue by using the `==` operator. Two equal signs tests that two
statements are equal while `!=` tests for inequality.

``` r
14 == c(14)
```

    ## [1] TRUE

``` r
c('j') == 'j'
```

    ## [1] TRUE

Let’s say that the people in our *ages* vector were exposed to
radioactive waste and consequently de-aged by **five.** Let’s subtract
five from our vector.

``` r
ages # Take a look at ages again
```

    ##  [1] 29 24 10 42 65  8 56 22 15  8

``` r
deaged.ages <- ages - 5 # Subtract 5 and store the result in deaged.ages
deaged.ages # Check out our new vector
```

    ##  [1] 24 19  5 37 60  3 51 17 10  3

Wow\! **MAGIC.**

You may chain operations together as well. Pretend that Steam is holding
a sale where games are 50% off. How would you discount the games in our
*vg.prices* vector?

``` r
discount.vg.prices <- vg.prices - vg.prices * .5
discount.vg.prices
```

    ## [1] 29.500 10.000  7.500  7.500  2.495

What happens when an operation is performed on two vectors of unequal
size? The smaller vector is reused/recycled per each element of the
larger vector—useful and environmentally friendly\!

Let’s say the radioactive waste alternates between increasing a person’s
age by five and decreasing the second person’s age by five as well.

``` r
ages + c(5, -5)
```

    ##  [1] 34 19 15 37 70  3 61 17 20  3

Take a gander at how the second vector is reused in order to fit the
first vector.

# Functions

## Reassignment (and why you should avoid it)

R executes a script from top to bottom. Now, some of you may say “well
duh…that’s obvious\!” Yep\!

I’ll mention yet another “obvious” axiom to continue my tautology as
well as to be completely pedantic: lines of code depend on preceding
lines of code. In other words, code at the bottom of your scripts depend
or may be affected by the code above.

The logic above may seem extreme simple, but I’ve encountered situations
where my peers replaced variables they defined earlier then called
functions that expected the variable they replaced\!

``` r
name_and_cats <- c("Josh", 42)
print(name_and_cats)
```

\[1\] “Josh” “42”
