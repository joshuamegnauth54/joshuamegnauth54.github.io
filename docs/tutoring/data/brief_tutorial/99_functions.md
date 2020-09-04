Functions and loading data?
================
Joshua Megnauth

  - [What are functions?](#what-are-functions)
      - [We have to talk about
        parameters\!](#we-have-to-talk-about-parameters)
  - [Finally, let’s load some data](#finally-lets-load-some-data)
  - [Advanced users](#advanced-users)

# What are functions?

Functions are reusable blocks of code that perform some action. For
example, R implements functions for measures of central tendency by
default. You can simply call a function rather than calculating the mean
or median by verbosely writing code each time. Let’s look at how to call
a function:

``` r
mean(airquality$Wind)
```

    ## [1] 9.957516

The syntax for calling a function is basically like functions in math as
well as other programming languages: `name(parameters)`

Functions may take zero or more *parameters* (also known as
*arguments*). Parameters provide input or options to the function. I
call the mean function above by passing in the Wind column from the
*airquality* data set.

## We have to talk about parameters\!

\*\*No programmer memorizes functions.\*

As a programmer you’ll likely have documentation open constantly while
coding. Let’s work through an example of using the documentation and
function parameters.

[https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/mean](Open%20this%20link%20please.)
Or, alternatively, type `?mean` in your R session (the **Console** tab).

Mean’s description states that the function calculates the “(trimmed)
arithmetic mean.” What does the function mean by “trimmed”? We can see
under the *arguments* section that the function takes a *trim* parameter
which is a “fraction” of “observations to be trimmed from each end of x
before the mean is computed.” Trimming removes a fraction (such as 0.1
or 10%) of observations before calculating the mean.

Next, let’s look at the **na.rm** argument. The parameter **na.rm**
“indicat\[es\] whether NA values should be stripped before the
computation proceeds.” We can’t perform math on `NA` (missing) values.

``` r
14 + NA
```

    ## [1] NA

Thus, the mean function has a parameter to ignore `NA` values to ensure
computation actually succeeds.

``` r
mean(airquality$Ozone)
```

    ## [1] NA

The mean function explicitly returns `NA` because the Ozone column is
missing some observations.

``` r
mean(airquality$Ozone, na.rm = TRUE)
```

    ## [1] 42.12931

Notice that I called the function with `na.rm = TRUE`. Functions may be
called by passing in arguments by position, by name, or mixing the two.
The position of each arguments is shown in the documents. Looking back
at mean we can see that `x`, our data, is the first parameter. The
arguments `trim`, `na.rm`, and variadic follow. I pass
`airquality$Ozone` by position which matches the argument `x`. I
**have** to use `na.rm = TRUE` because the second argument is `trim`. R
wouldn’t know what to do if we do something like this:

``` r
mean(airquality$Ozone, TRUE)
```

    ## Error in mean.default(airquality$Ozone, TRUE): 'trim' must be numeric of length one

# Finally, let’s load some data

# Advanced users

**You may safely ignore this section if you don’t care about writing
functions.**

(talk about using functions/parameters) and ::
