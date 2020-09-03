Garbage Collection and Working Directory
================
Joshua Megnauth

  - [Why do we run the following at the beginning of our
    code?](#why-do-we-run-the-following-at-the-beginning-of-our-code)
      - [What is each line doing?](#what-is-each-line-doing)
      - [Working directory and seed](#working-directory-and-seed)
  - [A slightly more technical
    explanation](#a-slightly-more-technical-explanation)

# Why do we run the following at the beginning of our code?

``` r
rm(list=ls())
gc()
setwd("~/Documents/Projects/Data/pokemon_cluster")
set.seed(314)
```

Each R session stores variables you define until you close the
interpreter. In other words, variables exist as long as an R session is
running and you haven’t manually deleted your variables.

Professor Cohen advises you to run the first three lines above to clean
up before your code runs proper. Imagine you ran some fancy
transformation on your data:

``` r
video_games_df <- fancy_transform(video_games_df, extrafancy=TRUE)
```

You’d end up mangling your variables while causing terribly strange
problems. Do you want more stress in your life? **Nope\!** So you may
just follow professor Cohen’s advice here.

## What is each line doing?

Do me a favor. Run `? FUNCTION-NAME-HERE` for each of the functions
above before
[setwd()](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/getwd).

So:

``` r
?ls
?rm
# Et cetera...
```

The help page for
[ls()](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/ls)
states that the function “ls shows what data sets and functions a user
has defined” when invoked outside of a function.

Next we’ll take a gander at
[rm()](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/remove)
which explains that “remove and rm can be used to remove objects.”

Finally,
[gc()](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/gc)
“causes a garbage collection to take place.”

Now we can unravel professor Cohen’s magic trick\! The code above first
gets a vector of the data sets and functions users have defined via
**ls()**. Next, we pass that list of object names into **rm()** which
removes the objects from our working space. Finally, **gc()** clears up
the actual objects in order to return the memory to the operating
system.

## Working directory and seed

The working directory is the default base path where R will look for
your data sets or save files. Setting a working directory via
**setwd()** saves you from typing in long paths whenever you’re ready to
load a file.

Next up is setting a **random seed.** Computers technically use
pseudorandom numbers. Random numbers are generated using seed values
from ostensibly random sources. Setting a random seed ensures that the
same numbers are produced in the same sequence. The benefit of setting a
random seed is that your results are reproducible.

Random seeds may be any number. Some scientists have what is known as a
**personal seed.** A personal seed is a value you always use when
setting random seeds; mine is **314.** Personal does not imply that only
one person may use the seed, by the way. For example, you’ll see **42**
used as a personal seed often.

**You shouldn’t capriciously change your random seed.** Switching around
your random seed from paper to paper may be a sign of disingenuous
science.

# A slightly more technical explanation

You may skip this section if you don’t care about computers.

R; like Python, Java, C\#, and others; is a garbage collected language.
Each instance of the R interpreter manages a slice of virtual memory
where your objects are stored. Thus, your objects exist for the lifetime
of the R process which created it.

I actually find interpreted languages extremely useful for data as I
often wrangle my objects in the console and test critical sections, like
RegEx or complicated cleaning pipelines, right in the interpreter.

Anyway, technically cleaning out your variables prior to knitting an
RMarkdown file isn’t necessary. RStudio has the option for running your
scripts and Rmds in a fresh R process. However, I agree with professor
Cohen that the safest method is to simply ensure everyone wipes their
interpreter clean. You may skip erasing your old variables and setting a
working directory if you know what you’re doing and manage your
environment properly. I never do either, for example\!
