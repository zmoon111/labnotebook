---
layout: post
tags: []
categories: evolution
---





 





MATICCE
-------

-   Testing out [Andrew
    Hipp's](http://redwood.mortonarb.org/lab_pages/hipp/ "http://redwood.mortonarb.org/lab_pages/hipp/")
    [maticce](http://cran.r-project.org/web/packages/maticce/index.html "http://cran.r-project.org/web/packages/maticce/index.html")
    package,
    [doi:10.1093/bioinformatics/btp625](http://hdl.handle.net/10.1093/bioinformatics/btp625 "doi:10.1093/bioinformatics/btp625").
-   Goal of package is to model-average over trees and regimes. Takes a
    brownie approach to regimes, identifying nodes at which a transition
    may have occurred.

\

### The Approach

Takes a brute force approach to avoid user-defined paintings in the ouch
framework. Users identify nodes of interest, software tries combinations
of possible shifts.

#### runBatchHansen()

-   User identifies a set of nodes of interest, and optionally a maximum
    number of transitions.

**What models/regimes are tested?**

-   You choose a set of candidate nodes and a maximum number of
    transitions that can occur (defaults to all nodes). For instance,
    you may specify four candidate nodes but set a max at 2 changes
    (three peaks). Then the batch will evaluate 12 models: the 4 choose
    2 = 6 three-peak models, the 4 choose 1 = 4 two peak models, a
    single peak, and a brownian motion model. If you didn't specify a
    max, it would allow the 1 five-peak (4 choose 4), and the 4
    four-peak (4 choose 3) models, for a total of 17 models.

-   Has summary function. Model averaging is a weighted average by BIC

-   characterStates -- the trait data, currently must be
    one-dimensional, seems like it can be in ape or ouch format.

-   nodes of interest have to be specified by listing all the species
    descendants. somewhat cumbersome.

-   Takes an ouchtree or list of ouchtrees.

**Output**

See loglik, sigma and alpha values of the models tested (doesn't include
brownian motion by default)

~~~~ {.de1}
trialBayesConsensus <- runBatchHansen(ape2ouch(carex$ovales.tree), carex$ovales.data, carex$ovales.nodes[1:4], maxNodes = 2)
> trialBayesConsensus$hansen
[[1]]
     loglik dof sigma.squared theta / alpha
1  52.72744   5    5.31005363    331.626872
2  50.54000   5    5.86424413    337.252821
3  50.21561   5    6.04474990    343.369492
4  49.95720   4    5.14354118    289.387746
5  51.75330   5    5.12434568    308.473054
6  51.81077   5    4.84664447    292.440242
7  51.75142   4    5.16258108    310.762680
8  44.31518   5    6.55448190    297.993592
9  44.17736   4    8.49647580    384.329498
10 42.89766   4    6.87292796    296.238909
11 39.57676   3    0.09810907      3.341806
 
 
## See which nodes involved a shift:
 
> trialBayesConsensus$regMatrix
[[1]]
   1 2 3 4
1  1 1 0 0
2  1 0 1 0
3  1 0 0 1
4  1 0 0 0
5  0 1 1 0
6  0 1 0 1
7  0 1 0 0
8  0 0 1 1
9  0 0 1 0
10 0 0 0 1
11 0 0 0 0
~~~~

Where the four nodes are shown as separate columns, and there numbers
correspond to their indices specified in ovales.nodes.

#### multiModel()

For a focal node, there are 5 hypotheses we want to distinguish:

1.  BM
2.  OU
3.  Two OU
4.  Two BM, censored as separate sub trees (data of non-descendants is
    not considered)
5.  Two OU, censored.

Note that the first three of these cases are among the cases covered in
a runBatchHansen() over the node.

#### Other functions

-   paintBranches() turns a maticce specification of a focal node into
    an OUCH regimes list, identifying the regime of each branch.

\

### Other Notes

### Evaluation

-   A useful package for batch runs of hansen hypotheses, reasonably
    polished. Two chief drawbacks:

1.  Method of specifying which nodes are of interest seems very
    annoying. Should require only two tips, and hence specify their
    common ancestor as the node. Should have an option to identify the
    node merely by id number.
2.  Having to specify nodes doesn't really get around the painting
    problem.

-   *The parameter space of paintings is too big!!* A new approach is
    needed to thinking about this; one that does not consider all
    possible outcomes as different models. (but of course I'd say that).

Notes / Reading
---------------

-   [Linked
    Data](http://www.slideshare.net/juansequeda/introduction-to-linked-data-2341398 "http://www.slideshare.net/juansequeda/introduction-to-linked-data-2341398")
    a new approach to semantic web ideas.
-   [Newsweek](http://www.newsweek.com/id/235084 "http://www.newsweek.com/id/235084")
    on why scientists are losing PR battle.
-   Recently started wikipedia page on [Science
    2.0](http://en.wikipedia.org/wiki/Science_2.0 "http://en.wikipedia.org/wiki/Science_2.0")
-   [lots](http://stackexchange.com/directory/list?sort=4 "http://stackexchange.com/directory/list?sort=4")
    of Stack Overflow models. Came across: [Semantic
    Overflow](http://www.semanticoverflow.com/questions/744/what-is-a-good-elevator-pitch-for-linked-data/767#767 "http://www.semanticoverflow.com/questions/744/what-is-a-good-elevator-pitch-for-linked-data/767#767")
    (thanks Rod Page).

\

Headline text
-------------
