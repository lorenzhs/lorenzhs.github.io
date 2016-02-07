---
layout: mathpost
title: Verifying a Sorting Algorithm
---

It’s easy to check whether an array is sorted, but that’s only half the
story. We also have to verify that the sorted array has the same elements as the
input. This is called verifying that the output is a *permutation* of the
input. It’s easy to check if you’re allowed to sort the input, but that’s not
very helpful in our case.

## The Naive Solution

We could take each element of the input array and look for it in the output
array. Because the output array is sorted, we can even use binary search to
accomplish this in logarithmic time per element. If there’s a possibility of
duplicate elements we also have to remember a flag with each element to indicate
that we’ve already “used it up” in a previous search, but that’s constant
overhead. All in all, we’d end up with $\mathcal O\left(n \log n\right)$ time
for an input with $n$ elements. That’s about the same cost as sorting the input
in the first place, so not too bad. However, we can do better if all we want to
do is to be *pretty sure* that the output is correct.

## The Randomized Algorithm

A few limitations first: this algorithm is for integers (or other things that
can be mapped to integer keys, e.g. things that have a hash value). It’s also a
Monte Carlo algorithm, so there is an arbitrarily small probability that the
output is incorrect. What this means is that we can choose to spend a bit more
time to get more confidence in the result.

Let’s name the elements of the input array $\{e_1,\ldots,e_n\}$ and the output
$\{o_1,\ldots,o_n\}$. Now consider the polynomial
\\[q(z) = \prod\_{i=1}^{n}{(z-e_i)} -\prod\_{i=1}^{n}{(z-o_i)}\ .\\] We’re
multiplying the differences between the elements of the input and our parameter
$z$, and then do the same with the output and subtract the two. If both arrays
are identical, then it’s easy to see that $q(z)=0$ for every $z$ that we plug in
there—we’re multiplying the same numbers, just in a different order. Otherwise,
there will be a value of $z$ so that the two product’s don’t match up. In fact,
it can be shown that $q(z)$ is zero for all values of $z$ if–and only if!–the
output array is a permutation of the input array. That’s a very hard problem to
check[^1], though, and seems much harder than our original problem. We’re also
multiplying a lot of numbers in that polynomial, they’ll easily exceed the range
of what our machines can handle natively, and we’d really like to avoid using an
arbitrary precision integer library. Fortunately, there’s a way out of both of
these conundrums. We’ll need a quick detour into linear algebra, though (I
promise to keep it short!)

If we don’t want our numbers to grow arbitrarily, we can do math modulo a fixed
number $p$. This way, if the products in our polynomial $q$ become too large, we
just take it *modulo* $p$ and continue. That sounds awfully sketchy, but
fortunately, there’s a thing called *residue fields* in linear algebra. What
they mean for us is that–as strange as it may sound–as long as *p* is a prime
number, we can actually go ahead and do math modulo that number and the math
will be fine. We just have to pick the number *p* large enough to make sure that
everything works for our values. So let’s call the probability with which we’re
allowed to get the verification wrong $\varepsilon$. We can actually choose
$\varepsilon$ pretty high here due to a trick to which I’ll come
later. Something like a quarter will do just fine (in practice we would use a
lower value, but not too low either). Now we have to choose a prime number
that’s bigger than $\frac{n}{\varepsilon}$ and all of the numbers in the input and
output. I promised to keep the math to a minimum so I won’t go into the details
for that. There are libraries to get big prime numbers easily, or we could also
precompute a couple and choose one that is big enough to cover the requirements
but not too large so that the computations don’t get unnecessarily complicated.

With that prime $p$, we can now evaluate the polynomial $q$ and do all the math
*modulo* $p$. If the two arrays have the same elements, the polynomial will
still be zero. But if they’re different, another bit of fancy math makes it so
that the polynomial evaluates to zero ($q(z)=0$ for some $z \in
\{0,\ldots,p-1\}$) with probability at most $\varepsilon$. If we chose
$\varepsilon=0.25$ we can now say that we’re at least 75% sure that the input
sequences are the same.

Hold on, 75%? That’s not very useful at all! We just generated a fairly large
prime number and did a lot of multiplications just to be 75% sure? Of course
that’s not the end of it. We can repeat this process with different values of
$z$ to increase the certainty of the outcome. If we try out $t$ randomly chosen
values between $0$ and $p-1$, then the probability that all $t$ of them just
happen to evaluate to zero even though the elements are different decreases
exponentially. Thus, the probability of mistakenly claiming that the elements
are sorted becomes $\varepsilon^t$. If we want to reduce this probability to a
value $\delta$ of our choice (say, $10^{-9}$), then we need to evaluate the
polynomial $t=\log_\varepsilon \delta$ times. Continuing our example, that
requires 15 repetitions with $\epsilon=0.25$. If the polynomial is zero for all
15 values, then we can claim with good confidence that the sorting algorithm
did, in fact, sort the input and not some other elements. We’ll be right
999,999,999 out of 1,000,000,000 times. And if that’s still not certain enough
for you, why don’t you try 30 repetitions with $\epsilon=0.1$?  You’ll get a
certainty that’s bigger than the probability of your computer making a mistake
because of cosmic radiation.

### Alternative Approaches

Another approach, as described in
[this MathOverflow post](http://mathoverflow.net/a/25384) by UC Irvine Professor
David Eppstein would be to take a hash function $h(x)$ and compute
$\sum\_{i=1}^{n}{h(e_i)}$ and $\sum\_{i=1}^{n}{h(o_i)}$. If the elements are the same,
the sums will match. However, some care has to be taken in choosing the hash
function, and a sufficiently wide number type has to be used to deal with very
large inputs, as the sums could overflow otherwise. Also note that the
polynomials he mentions in a comment to his answer are different from the
polynomial $q(z)$ used here–I think he's referring to using low-degree
polynomials with random coefficients.

*This article is based on ideas from Exercise 5.5 or 5.6 (depending on edition)
from the book* Algorithms and data structures: The basic toolbox *by Kurt
Mehlhorn and Peter Sanders, Springer Science & Business Media, 2008. The authors
have made the individual chapters
[available online](https://people.mpi-inf.mpg.de/~mehlhorn/Toolbox.html).*

[^1]:
    This is slightly unrelated, but verifying that a polynomial is identical to
    zero when you’re not allowed to look at the coefficients is one of those
    problems that’s in a complexity class of probabilistic algorithms called
    **BPP** (bounded-error probabilistic polynomial time), but nobody knows
    whether it’s possible to do the it *deterministically* in polynomial
    time. Primality check used to be in the same spot for a long time, but was
    finally shown to be possible deterministically in polynomial time in 2002.
