---
layout: post
tags: [code-tricks, model-choice]
categories: evolution
---





 





Research
--------

### New Simulations

-   Now have code to run partitioned models on the Anoles dataset,
    compare partitioned models to the paintings chosen by Butler and
    King. Preliminary figures below. Models with \_s are partitioned by
    my algorithm, others are the the original ones from earlier.

![image](http://openwetware.org/images/thumb/c/cb/Anoles_partition.png/300px-Anoles_partition.png)

-   Also have examples with simulated data with a known number of
    partitions, to check the method. Tested with data produced at two
    peaks on the Labrid tree, the pattern seems similar to that of the
    Labrids -- comparisons involving any model with more than 2 peaks
    produce LR ratios that become very unlikely in either pairwise
    comparison. Still needs further exploration. Preliminary figures:

![image](http://openwetware.org/images/thumb/2/22/Simulated_LR.png/300px-Simulated_LR.png)

\

### Interpreting Results

-   Pattern: In the bootstrapping, the model generating the data always
    scores substantially higher than the test model, making all
    distributions fall in the positive range. Note that parameters are
    fixed, hence the more complicated model does not have an advantage
    in this bootstrapping. Meanwhile, the more complicated model does
    substantially better at the real data than the simpler model, since
    in this case both have there parameters fit to the data.
    Consequently the red line falls deep in the negative for the
    bootstraps simulated under the simple model and makes it appear as
    though the more complicated model is always correct. Perhaps
    comparing models in which the parameters are allowed to vary may
    indeed be more meaningful.

-   Compare to parameter varying case.

-   Now simulating with parameter varying using simulated two-peak
    dataset.

### Beating bugs and improving code

-   Found a work-around in the bug that was preventing me from running
    the Anoles dataset under partitioning. The problem seems to be the
    reorder.phylo() by pruning order command, which is called by the
    ace() function on the phylogenetic tree. This function calls a C
    function to do the reordering, which somehow gets stuck on an
    infinite loop when given a file converted from ouch format to ape
    format using my function. Writing my converted tree out to a nexus
    file with write.nexus() and then reading it back in seems to solve
    this problem.

-   Made my convert() function build its "phylo" tree arguments in the
    same order (edge, Nnode, tip.label, edge.length) as they are
    assigned in the "phylo" objects created by read.nexus, though this
    doesn't seem to matter as most calls are made with phy$edge rather
    than phy[1].

-   Code now avoids bootstrapping a model against itself.

-   **Could avoid re-simulating** datasets for each figure in the same
    row, but should profile the code first to make sure this is worth
    while.

-   should start logging simulation runs directly and fixing seed.

-   png and cairo devices haven't been installed on the server, figures
    output in pdf and converted to png by imagemagick with bash:

~~~~ {.de1}
for i in *.pdf; do convert "$i" "${i/.pdf}".png; done
~~~~

-   see [git
    log](http://github.com/cboettig/Comparative-Phylogenetics/commits/master "http://github.com/cboettig/Comparative-Phylogenetics/commits/master")
    for more details.

Notes
-----

-   Google Summer of Code includes [NesCent "phyloinformatics"
    research](https://www.nescent.org/wg/phyloinformatics/index.php?title=Phyloinformatics_Summer_of_Code_2010#Mentors "https://www.nescent.org/wg/phyloinformatics/index.php?title=Phyloinformatics_Summer_of_Code_2010#Mentors")

-   [obvious...](http://www3.interscience.wiley.com/journal/123328987/abstract?CRETRY=1&SRETRY=0 "http://www3.interscience.wiley.com/journal/123328987/abstract?CRETRY=1&SRETRY=0")

-   still reading: [open
    science](http://blog.openwetware.org/scienceintheopen/2009/11/05/reflections-on-science-20-from-a-distance-part-i/ "http://blog.openwetware.org/scienceintheopen/2009/11/05/reflections-on-science-20-from-a-distance-part-i/"),
    [part
    2](http://blog.openwetware.org/scienceintheopen/2009/11/06/reflections-on-science-20-from-a-distance-part-ii/ "http://blog.openwetware.org/scienceintheopen/2009/11/06/reflections-on-science-20-from-a-distance-part-ii/"),
    and also enjoyed this

### CSGF Conference HPC Survey

-   Not directly phylogentics, but also working on today:
-   I was contacted by Mary Ann 3 days ago on doing another survey of
    CSGF fellows. [Last year's
    survey](http://www.surveymonkey.com/s/VMTTFXS "http://www.surveymonkey.com/s/VMTTFXS")
    was pretty basic
    ([results](http://www.surveymonkey.com/MySurvey_Responses.aspx?sm=SXNbpnWs3a%2blQnEwe%2bfNiKvBGCZEs/jdQhlVfunE9YI%3d "http://www.surveymonkey.com/MySurvey_Responses.aspx?sm=SXNbpnWs3a%2blQnEwe%2bfNiKvBGCZEs/jdQhlVfunE9YI%3d")).
    Also interested to explore alternatives to surveymonkey, though it
    did make things quite easy and Krell can probably get the
    subscription. Possibly Google forms?

Some goals I have for the survey:

1.  **Who knows/uses what.** Be able to see what languages,
    parallelization, and scale of computation fellows are computing on.
    The crucial goal for this year is to be able to break this down by
    subject area and by year in the fellowship.
2.  **Effective practices in computing.** Hugely important but too
    rarely taught (in sciences anyway). Version control, debugging,
    visualization, etc. A great example of this stuff is to see the quiz
    on [Software
    Carpentry](http://software-carpentry.org/intro.html "http://software-carpentry.org/intro.html")(scroll
    down to \#5)
3.  [**Science
    2.0**](http://openwetware.org/wiki/Science_2.0/Brainstorming "http://openwetware.org/wiki/Science_2.0/Brainstorming")
    This goes a little further afield and might deserve its own session.
    Computers and the internet continue to change many aspects in the
    way we do science and it makes sense that CSGFs should be at the
    forefront. Large-scale online collaborations. The benefits and risks
    of science blogging. Standardized and publicly accessible data,
    source code, publications, and notebooks. Social networking for
    scientists. There's a wealth of rapidly emerging tools in this area
    that I think we should all know about. Two quick examples: Mendeley
    for organizing our collections of scientific papers and
    stackoverflow.com for solving programming problems collaboratively.

\

