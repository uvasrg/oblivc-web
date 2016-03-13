+++
title = "Obliv-C: A Language for Extensible Data-Oblivious Computation"
+++

<img src="/images/oblivc.png" align="center" width="90%">

**Obliv-C** is a simple GCC wrapper that makes it easy to embed secure
computation protocols inside regular C programs. 

The idea is simple: if you are performing a multi-party distributed
computation with sensitive data, just write it in our Obliv-C langauge
and compile/link it with your project. The result will be a secure
multi-party cryptographic protocol that performs this operation without
revealing any of the inputs or intermediate values of the computation to
any of the parties. Only the outputs are finally shared.

## Paper

Samee Zahur and David Evans. [_Obliv-C: A Language for Extensible
Data-Oblivious Computation_](http://eprint.iacr.org/2015/1153.pdf),
[Cryptology ePrint Archive: Report
2015:1153](http://eprint.iacr.org/2015/1153)
[[PDF](http://eprint.iacr.org/2015/1153)], November 2015.

## Code

[https://github.com/samee/obliv-c](https://github.com/samee/obliv-c)

This repository includes the implementation of Obliv-C and several
example applications and benchmarks.

## Tutorials

[Introduction to Obliv-C](http://samuelhavron.github.io/obliv-c/)  

[Obliv-C Language Introduction](http://goo.gl/TXzxD0)  


## Projects Using Obliv-C

#### Half-Gates

Samee Zahur, Mike Rosulek, and David Evans.  <a href="http://mightbeevil.com/halfgates/"><em>Two Halves Make a Whole: Reducing Data Transfer in Garbled Circuits using Half Gates</em></a>.  In <a href="https://www.cosic.esat.kuleuven.be/eurocrypt_2015/papers.shtml">EuroCrypt 2015</a>.  Sofia, Bulgaria.  26-30 April 2015. [<a href="http://www.cs.virginia.edu/evans/pubs/ec2015/halfgates.pdf">PDF</a>, 28 pages] [<a href="http://github.com/samee/obliv-c">Code</a>]

#### Sqoram

Samee Zahur, Xiao Wang, Mariana Raykova, Adrià Gasco, Jack Doerner,
David Evans, Jonathan Katz. [_Revisiting Square-Root ORAM Efficient
Random Access in Multi-Party
Computation_](http://www.mightbeevil.com/sqoram.pdf) (pre-publication
version).  To appear in [_37<sup>th</sup> IEEE Symposium on Security and
Privacy_](http://www.ieee-security.org/TC/SP2016/) ("Oakland"). San
Jose, CA. 23-25 May 2016.

## People

[Samee Zahur](https://www.cs.virginia.edu/~sza4uq/), Project Leader

[Jack Doerner](https://jackdoerner.net/), Wizard of Oblivion  
[David Evans](https://www.cs.virginia.edu/evans), Faculty Advisor  
[Samuel Havron](https://github.com/samuelhavron), Undergraduate Researcher  

Past contributors: Natnatee ("Ko") Dokmai, [Richard
Li](https://github.com/Lichard), Michael Mahoney.





