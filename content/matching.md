+++
title = "Secure Stable Matching at Scale"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

## Secure Stable Matching at Scale

When a group of individuals and organizations wish to compute a _stable
matching_ &mdash; for example, when medical students are matched to
medical residency programs &mdash; they often outsource the computation
to a trusted arbiter to preserve the privacy of participants’ preference
rankings. Secure multi-party computation presents an alternative that
offers the possibility of private matching processes that do not rely on
any common trusted third party. However, stable matching algorithms are
computationally intensive and involve complex data-dependent memory
access patterns, so they have previously been considered infeasible for
execution in a secure multiparty context on non-trivial inputs.

We adapt the classic Gale-Shapley algorithm for use in such a
context, and show experimentally that our modifications yield a
lower asymptotic complexity and more than an order of magnitude
in practical cost improvement over previous techniques. Our
main insights are to design new oblivious data structures that exploit
the properties of the matching algorithms. We then apply our
secure computation techniques to the instability chaining algorithm
of Roth and Peranson, currently in use by the National Resident
Matching Program. The resulting algorithm is efficient enough to
be useful at the scale required for matching medical residents nationwide,
taking just over 17 hours to complete an execution simulating
the 2016 NRMP match with more than 35,000 participants
and 30,000 residency slots.

## Code

Our code will be available soon under and open source license.  Please
send email to jhd3pa@virginia.edu to be notified when it is available.

## Paper

Jack Doerner, David Evans, and abhi shelat. [_Secure Stable Matching at
Scale_](/docs/matching.pdf) (pre-publication version).  12 June 2016.

## People

[Jack Doerner](https://jackdoerner.net/) (University of Virginia)  
[David Evans](https://www.cs.virginia.edu/evans) (University of Virginia)  
abhi shelat (Northeastern University)






