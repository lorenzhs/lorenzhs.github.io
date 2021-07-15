---
layout: post
title: Communication-Efficient Probabilistic Algorithms – Selection, Sampling, and Checking
---

On November 26 last year I successfully defended my dissertation at Karlsruhe
Institute of Technology. The full text has been published through the KIT
library with the DOI
[10.5445/IR/1000127719](https://publikationen.bibliothek.kit.edu/1000127719).

If you find any mistakes or have corrections of any kind, please email me at my
name at this domain.  I will add them to the [list of corrections and
remarks](/files/diss_corrections.pdf).

## Abstract

This dissertation focuses on three fundamental problem families in big data
systems, for which we develop communication-efficient probabilistic algorithms.
In the first part, we consider various *selection* problems, in the second,
*weighted sampling* problems, and the third, *checking* of basic operations in
big data systems. This is motivated by a growing need for communication
efficiency as the network and its usage increasingly dominate supercomputer
system cost and energy consumption as well as running times of distributed
applications. Surprisingly few communication-efficient algorithms are known for
fundamental big data problems, and we close several of these gaps.

We first consider different selection problems, starting with selecting the
element with rank *k* from a large, distributed input. There, we show how this
can be achieved without assuming a random distribution of the input by
redesigning the pivot selection step. Next, we show that selection from locally
sorted sequences—also known as *multisequence selection*—becomes considerably
easier if we allow the precise rank of the output to vary in some range. We then
describe how this can be used to construct a bulk priority queue, which we later
use to construct an algorithm for weighted reservoir sampling. Lastly, we
consider finding the globally most frequent objects as well as those whose
associated values add up to the highest sums with a sample-based approach.

In the weighted sampling chapter, we begin by giving new construction algorithms
for a classical data structure for weighted sampling, *alias tables*.  After
presenting the first linear-time construction algorithm with only constant
memory overhead, we proceed to parallelise this algorithm for shared memory,
obtaining the first parallel construction algorithm for alias tables. We then
show how to approach the problem in distributed memory with a two-level
algorithm. Next, we present an output-sensitive algorithm for weighted sampling
with replacement, meaning that it requires time linear in the number of *unique*
items in the sample. This algorithm works sequentially as well as in shared and
distributed memory and is the first such algorithm in all three categories. We
subsequently adapt it to weighted sampling without replacement by combining it
with an estimator for the number of unique items in a sample with
replacement. Poisson sampling, a generalisation of Bernoulli sampling to
weighted items, can be reduced to integer sorting, and we show how an existing
approach can be parallelised. For sampling from data streams, we adapt a
sequential approach and show how it can be parallelised in a mini-batch model
using our bulk priority queue introduced in the selection chapter. The chapter
is concluded by an extensive evaluation of our alias table construction
algorithms, our output-sensitive algorithm for weighted sampling with
replacement, and our weighted reservoir sampling algorithm.

To probabilistically verify the correctness of distributed algorithms, we
propose *checkers* for fundamental operations of big data systems. We show that
checking numerous operations can be reduced onto two ‘core’ checkers, namely
checking aggregations and whether one sequence is a permutation of
another. While multiple approaches exist for the latter and can be easily
parallelised, our sum aggregation checker is a novel application of the same
data structure that underpins counting Bloom filters and count-min sketches. We
implemented both checkers in Thrill, a big data framework. Experiments with
deliberately introduced errors confirm the detection accuracy predicted by
theory, even when using real-world hash functions with non-ideal properties.
Scaling experiments on a supercomputer show that our checkers have very little
runtime overhead, in the area of 2 % for near-certainty in the correctness of
the result.
