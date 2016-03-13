+++
title = "Obliv-C: A Language for Extensible Data-Oblivious Computation"
+++

### Obliv-C: A Language for Extensible Data-Oblivious Computation

Obliv-C is a simple GCC wrapper that makes it easy to embed secure
computation protocols inside regular C programs. The idea is simple: if
you are performing a multi-party distributed computation with sensitive
data, just write it in our Obliv-C langauge and compile/link it with
your project. The result will be a secure multi-party cryptographic
protocol that performs this operation without revealing any of the
inputs or intermediate values of the computation to any of the
parties. Only the outputs are finally shared.

### Paper

Samee Zahur and David Evans. [_Obliv-C: A Language for Extensible
Data-Oblivious Computation_](http://eprint.iacr.org/2015/1153.pdf),
[Cryptology ePrint Archive: Report
2015/1153](http://eprint.iacr.org/2015/1153)
[[PDF](http://eprint.iacr.org/2015/1153)], November 2015.

### Tutorial

### Projects Using Obliv-C






