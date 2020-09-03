What are libraries/packages?
================
Joshua Megnauth

  - [What are libraries?](#what-are-libraries)
  - [What sorts of functionality is provided by
    libraries?](#what-sorts-of-functionality-is-provided-by-libraries)
  - [How do I use a library?](#how-do-i-use-a-library)
  - [Addendum](#addendum)

# What are libraries?

Unlike SAS or SPSS, R is free, libre, and open source with a community
that publishes free books and code constantly. The source code (think:
**recipe**) for open source software is freely available forever for
anyone to peruse or modify. The benefits of FLOSS are basically infinite
and thus impossible to recount here, but mentioning R’s FLOSSyness is
important for our next brief subject: **libraries**.

Skilled R programmers write libraries (a.k.a. packages) that encapsulate
some functionality. R does not come with every statistical or data
concept. From a pragmatist’s standpoint implementing every stats concept
ever would be way too much work and prone to error. R includes the basic
archeciture from which to build strong statistical functionality.

R is a global community effort as R programmers around the world
contribute to the total functionality of the R ecosystem. For example,
let’s say that functions for standardizing and normalizing variables
aren’t provided with R out of the box. I may write my own functions and
author a package to make using my work easier then provide said library
for everyone in the universe to use.

The best part is that all of this is open source too—as it should be\!

# What sorts of functionality is provided by libraries?

You’ll encounter many libraries throughout your data career such as
[ggplot2](https://ggplot2.tidyverse.org/), [the
tidyverse](https://tidyverse.org/),
[caret](https://topepo.github.io/caret/),
[mice](https://cran.r-project.org/web/packages/mice/mice.pdf),
[ranger](https://cran.r-project.org/web/packages/ranger/ranger.pdf), et
cetera.

Amazingly, almost all of R’s machine learning functionality as well as
the entire concept of tidily working with data with pipes *as well as*
the perenially amazing ggplot2 are all libraries written by *other*
people beyond the base R team\! R is infinitely extensible and
adaptable. You will never be stuck with a static set of closed source
functions that may not be updated if you don’t shell out hundreds of
dollars as with SAS.

# How do I use a library?

**You should not pell-mell install every library you come across.** I
helped out my peers with R in Spring 2020 when I had 712. I’ve seen many
instances where students installed packages they found online but didn’t
really know how to use them. Usually, if you’re installing a library,
you need to read through the documentation in order to understand how to
actually use the functionality provided. In other words, libraries may
help you solve your problems but can also stress you out if you don’t
put in the extra work to learn how to use it. With that said, I install
libraries judiciously by often researching the best practice and
recommendations as well as relentlessly aiming to stay on the cutting
edge. (Don’t do this.)

Anyway, you install packages with
[install.packages()](https://www.rdocumentation.org/packages/utils/versions/3.6.2/topics/install.packages)
which takes a string of the name of the package you wish to install.

``` r
install.packages("tidyverse")
```

Notice that the package name is in quotation marks (that is, a string).

I recommend you install packages using the **console** window. You
should *not* put **install.packages()** in your Rmd because you’ll keep
reinstalling the same package over and over again. Packages **should
only be installed once.** Installing a package is like installing a
program on your computer. You don’t reinstall the same program each time
you wish to run it; you install once. Like programs, packages should be
periodically updated.

``` r
update.packages()
```

I don’t recommending upgrading packages the day before you have an
assignment due just in case something breaks. However, packages are
often upgraded with cool functionality so do upgrade them from time to
time.

Next, let’s load our new package.

``` r
library(tidyverse)
```

Loading a package is similar to starting a program. You (generally) have
to load a library before you may use the associated functions.

Notice that *tidyverse* isn’t a string. Why? R knows that tidyverse
exists because we installed it.

# Addendum

**Skip this section if you don’t care about computers.**

I recommend looking into [Anaconda](https://www.anaconda.com/) for those
of you who are more into computing and programming. Anaconda is
generally recommended for Python and R across Linux, Windows, and macOS.
Anaconda is a distribution that intelligently manages dependencies.
Conda is indispensible for Python and the sciences since dependencies on
Python are a complete pain. For example, unless you’re on Gentoo (and
even if you’re on Gentoo), you’d have to manually install via pip and
track version dependencies if you’re distributing a script. Anaconda is
also a great and stable deterministic base. I’d rather manage R
dependencies via Anaconda too for similar reasons.

As a distribution, Anaconda ensures that dependencies play nicely as
well similar to a Linux distro. Conda updated to R 4.0 slightly after
the official version dropped in order to make sure everything works. I
like to be on the cutting edge (hence Gentoo and also Rust), so I like
that Anaconda engenders safe but rapid updates.
