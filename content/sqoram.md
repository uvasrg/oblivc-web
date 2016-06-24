+++
title = "Revisiting Square Root ORAM"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

## Efficient Random Access in Multi-Party Computation

Hiding memory access patterns is required for secure computation, but
remains prohibitively expensive for many interesting applications. Prior
work has either developed custom algorithms that minimize the need for
data-dependant memory access, or proposed the use of Oblivious RAM
(ORAM) to provide a general-purpose solution. However, most ORAMs are
designed for client-server scenarios, and provide only asymptotic
benefits in secure computation. 

We show how the classical square-root ORAM of Goldreich and Ostrovsky
can be modified to provide a practical ORAM for use in secure
computation, even though it is asymptotically worse than the best known
schemes. Specifically, we show a design that has over 100_x_ lower
initialization cost, and provides benefits over linear scan for just 8
blocks of data. Our scheme outperforms alternate approaches across a
wide range of parameters, often by several orders of magnitude.

<img src="/images/sqoramcompare.png" align="center" width="70%">

## Code

[https://github.com/samee/sqrtOram](https://github.com/samee/sqrtOram)  

This repository includes the implementation of Square-Root ORAM in
[Obliv-C](/), as well as several example applications and benchmarks.

## Paper

Samee Zahur, Xiao Wang, Mariana Raykova, Adrià Gascón, Jack Doerner,
David Evans, Jonathan Katz. [_Revisiting Square-Root ORAM Efficient
Random Access in Multi-Party Computation_](/docs/sqoram.pdf). In [_37<sup>th</sup> IEEE
Symposium on Security and
Privacy_](http://www.ieee-security.org/TC/SP2016/) ("Oakland"). San
Jose, CA. 23-25 May 2016.

## People

[Samee Zahur](https://www.cs.virginia.edu/~sza4uq/) (University of Virginia), Project Founder and Leader  
[Xiao Wang](https://github.com/wangxiao1254) (University of Maryland)  
[Mariana Raykova](http://cpsc.yale.edu/people/mariana-raykova) (Yale University)  
[Adrià Gascón](http://www.cs.upc.edu/~agascon/) (University of Edinburgh)  
[Jack Doerner](https://jackdoerner.net/) (University of Virginia)  
[David Evans](https://www.cs.virginia.edu/evans) (University of Virginia)  
[Jonathan Katz](https://www.cs.umd.edu/~jkatz/) (University of Maryland)







