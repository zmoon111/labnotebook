---
layout: post
tags: [code-tricks, model-choice]
categories: evolution
---





 





Research
--------

-   Bootstrapped against 2000 replicate simulations overnight on both
    the Anoles dataset and the Labrids dataset.
-   Distributions that overlap zero represent replicates that choose the
    wrong model.
-   The Anoles data below; the first column corresponds to the
    likelihood ratios shown in [Butler & King
    (2004)](http://www.journals.uchicago.edu/doi/abs/10.1086/426002 "http://www.journals.uchicago.edu/doi/abs/10.1086/426002"),
    comparing each model against BM.

![image](http://openwetware.org/images/thumb/0/0f/Anoles_full_LR.png/600px-Anoles_full_LR.png)

-   The Labrid dataset:

![image](http://openwetware.org/images/thumb/6/6c/Labrid_full_LR.png/600px-Labrid_full_LR.png)

-   Find matching figures for the case of parameters held fixed on more
    recent entry

-   Recall I simulate the dataset under the model labeled "true" and
    then evaluate the likelihood ratio of that model vs the test model.
    So along a row data is produced by simulation of the same model. The
    pattern is basically that the upper triangle of the matrix (simulate
    simple, compare to complex) always has negative LR for the true data
    but a very positive distribution of LRs. It's still a bit surprising
    to me that when data simulated under the simpler model that the
    simple model should always drastically outperform the complex one in
    LR, but it does.

#### Discussion w/ Peter

-   Bootstraps in this implementation refit the model to the simulated
    data each time.
-   This largely comes from relying on the behavior of the built-in
    functions: the bootstrap call and the update call on ouch models
    both refit the model.

Computing
---------

-   R objects can have multiple classes. For instance, in ouch, a
    hansentree and a browntree are both also of class ouchtree. To check
    if an object is a member of a class, calling class(object) directly
    only returns the top class. use:

~~~~ {.de1}
is(object, "classname")
~~~~

to check if the object is a member of a class.

-   Was trying to figure out the best way for an R function to return
    multiple objects, particularly when those objects are complicated
    classes and not just things that can be concatenated into a
    data.frame, equivalent to an array of pointers to objects in C. A
    vector of type "list" does the trick:

~~~~ {.de1}
out <- vector("list", 5)
out[[1]] <- object 1
~~~~

etc.

-   Trying to include more error handling in my codes. For instance,
    checking classes of objects

~~~~ {.de1}
if (!is(tree,'ouchtree'))
  stop(sQuote("tree")," must be an object of class ",sQuote("ouchtree"))
~~~~

-   Trying to start following Google's literate programming [inline
    documentation for
    functions](http://google-styleguide.googlecode.com/svn/trunk/google-r-style.html#functiondefinition "http://google-styleguide.googlecode.com/svn/trunk/google-r-style.html#functiondefinition")
    in R. The standard R documentation method really encourages writing
    documentation *after* writing the code, rather antithetical to
    literate programming.

