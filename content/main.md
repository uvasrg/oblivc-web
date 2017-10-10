+++
title = "Obliv-C: A Language for Extensible Data-Oblivious Computation"
+++

<center><img src="/images/oblivc-cropped.png" align="center" width="90%"></center>

**Obliv-C** is a simple GCC wrapper that makes it easy to embed secure
computation protocols inside regular C programs. 

The idea is simple: if you are performing a multi-party distributed
computation with sensitive data, just write it in our Obliv-C langauge
and compile/link it with your project. The result will be a secure
multi-party cryptographic protocol that performs this operation without
revealing any of the inputs or intermediate values of the computation to
any of the parties. Only the final outputs are revealed.

This paper motivates and describes Obliv-C:

> Samee Zahur and David Evans. [_Obliv-C: A Language for Extensible
Data-Oblivious Computation_](http://eprint.iacr.org/2015/1153.pdf),
[Cryptology ePrint Archive: Report
2015:1153](http://eprint.iacr.org/2015/1153)
[[PDF](http://eprint.iacr.org/2015/1153)], November 2015.


## Getting Started

**Code: [https://github.com/samee/obliv-c](https://github.com/samee/obliv-c)**

> This repository includes the implementation of Obliv-C and several example applications and benchmarks.


[**Tutorial**](/tutorial) 

> Walks through how to install Obliv-C and build your first data-oblivious application using a simple linear regression example application.

[**Documentation**](/documentation)

> Documentation on Obliv-C's language extensions and library functions.


## Projects Using Obliv-C

### Libraries and Protocols

#### Half-Gates

Samee Zahur, Mike Rosulek, and David Evans.  <a href="http://mightbeevil.com/halfgates/"><em>Two Halves Make a Whole: Reducing Data Transfer in Garbled Circuits using Half Gates</em></a>.  In <a href="https://www.cosic.esat.kuleuven.be/eurocrypt_2015/papers.shtml">EuroCrypt 2015</a>.  Sofia, Bulgaria.  26-30 April 2015. [<a href="http://www.cs.virginia.edu/evans/pubs/ec2015/halfgates.pdf">PDF</a>, 28 pages] [<a href="http://github.com/samee/obliv-c">Code</a>]

#### [SqORAM](/sqoram)

Samee Zahur, Xiao Wang, Mariana Raykova, Adrià Gascón, Jack Doerner,
David Evans, Jonathan Katz. [_Revisiting Square-Root ORAM Efficient
Random Access in Multi-Party Computation_](/docs/sqoram.pdf) In
[_37<sup>th</sup> IEEE Symposium on Security and
Privacy_](http://www.ieee-security.org/TC/SP2016/) ("Oakland"). San
Jose, CA. 23-25 May 2016.

#### [Absentminded Crypto Kit](https://bitbucket.org/jackdoerner/absentminded-crypto-kit)

Jack Doerner.  Library of oblivious implementations of cryptographic
primitives implemented in Obliv-C.  Includes big integer math, hash
functions, sorting, graph algorithms, oblivious data structures, and
ORAM implementations.  [Code:&nbsp;[https://bitbucket.org/jackdoerner/absentminded-crypto-kit](https://bitbucket.org/jackdoerner/absentminded-crypto-kit)]

### Applications

#### [Privacy-Preserving Distributed Linear Regression on High-Dimensional Data](https://eprint.iacr.org/2016/892)

Adria&#768; Gasco&#769;n and Phillipp Schoppmann and Borja Balle and
Mariana Raykova and Jack Doerner and Samee Zahur and David Evans. In [Privacy Enhancing Technologies Symposium](https://petsymposium.org/2017/) (PETS).  Minneapolis, Minnesota, 18 &ndash; 21 July 2017. [<a href="/docs/distributedregression.pdf">PDF</a>]

#### [Decentralized Certificate Authorities](/dca)

Bargav Jayaraman<sup>*</sup>, Hannah Li<sup>*</sup>, David Evans. <a
href="/docs/dca.pdf"><em>Decentralized Certificate
Authorities</em></a>. 11 June 2017. (updated 10 October 2017) (* the first two
authors both contributed as co-equal first authors) [<a href="/docs/dca.pdf">PDF</a>]

#### [Privacy-Preserving Machine Learning](/ppml)

Lu Tian, Bargav Jayaraman, Quanquan Gu, and David Evans. [_Aggregating
Private Sparse Learning Models Using Multi-Party
Computation_](/docs/pmpml.pdf). In [Private Multi‑Party Machine
Learning](https://pmpml.github.io/PMPML16/) (NIPS 2016 Workshop),
Barcelona, 9 December 2016. [<a href="/docs/pmpml.pdf">PDF</a>]

#### [Secure Stable Matching](/matching)

Jack Doerner, David Evans, abhi shelat. [_Secure Stable Matching at Scale_](http://oblivc.org/docs/matching.pdf).  In [_23<sup>rd</sup> ACM Conference on Computer and Communications Security_](https://www.sigsac.org/ccs/CCS2016/) (CCS). Vienna, Austria. 24-28 October 2016. [<a href="/docs/matching.pdf">PDF</a>]

#### Secure Deep Learning

[https://github.com/bargavjayaraman/SecureDeepLearning](https://github.com/bargavjayaraman/SecureDeepLearning)  
Bargav Jayaraman (Accenture Technology Labs, Bangalore &rarr; now at UVA)

### Applications Built with Obliv-C by Others

[Pretzel: Email encryption and provider-supplied functions are
compatible](http://www.cs.nyu.edu/~mwalfish/papers/pretzel-sigcomm17.pdf),
Trinabh Gupta, Henrique Fingler, Lorenzo Alvisi, and Michael
Walfish. ACM SIGCOMM 2017.

[SECCOMP - The Secure Spreadsheet](https://calctopia.com), Calctopia, 2017.

## Selected Talks

David Evans. [_Secure Multi-Party Computation: Promises, Protocols, and Practicalities_](https://www.jeffersonswheel.org/2017/secure-multi-party-computation-promises-protocols-and-practicalities). <a href="http://crypto-events.di.ens.fr/ecryptnet/">ECRYPT NET Workshop
on Crypto for the Cloud &amp; Implementation</a>, Paris, France, 27 June
2017. [<a
href="https://speakerdeck.com/evansuva/secure-multi-party-computation-promises-protocols-and-practicalities">Speaker&nbsp;Deck</a>]

David Evans and Denis Nekipelov. <a href="https://www.jeffersonswheel.org/2016/ftc-visit"><em>Private Data Analysis using Multi-Party Computation</em></a>.
Federal Trade Commission (joint presentation), 18 August 2016.

David Evans. <a href="https://www.jeffersonswheel.org/2016/shanghaitech-symposium"><em>Memory for Data Oblivious Computation</em></a>.  <a href="http://ssist2016.shanghaitech.edu.cn/index.html">ShanghaiTech Symposium</a>, 25 June
  2016. [<a href="https://speakerdeck.com/evansuva/memory-for-data-oblivious-computation">Speaker Deck</a>]

David Evans. <a href="https://www.jeffersonswheel.org/2016/aarhus-workshop-on-theory-and-practice-of-secure-multiparty-computation"><em>From Mercury Delay Lines to Magnetic Core Memories: Progress in Oblivious Memories</em></a>.
<a href="http://ctic.au.dk/events/workshops-conferences/mpc-2016/">Workshop
  on Theory and Practice of Secure Multiparty Computation</a>, Aarhus
  University, Denmark. 1 June 2016. [<a href="https://speakerdeck.com/evansuva/from-mercury-delay-lines-to-magnetic-core-memories-progress-in-oblivious-memories">Speaker Deck</a>]

Samee Zahur. [_Obliv-C: A Simple C Extension for
SMC_](http://www.humangenomeprivacy.org/2015/slides/UV.pdf), iDash
Privacy & Security Workshop 2015.  (Won award for fasted "Hamming
Distance" execution.)

David Evans. [_Multi-Party Computation for the
Masses_](http://www.cs.virginia.edu/~evans/talks/crossing2015/)
(includes video).  [CROSSING Conference 2015: Where Quantum Physics,
Cryptography, System Security and Software Engineering
Meet](https://www.crossing.tu-darmstadt.de/en/news-events/crossing-conference-2015/). Darmstadt. 1 June 2015

Samee Zahur. [_Obliv-C: A Lightweight Compiler for Data-Oblivious
Computation_](http://research.microsoft.com/apps/video/dl.aspx?id=20989)
(includes video). Applied Multi-Party Computation.  Microsoft Research,
Redmond, WA. 20 February 2014.

## People

[Samee Zahur](https://www.cs.virginia.edu/~sza4uq/), Project Founder and Leader (now at Google)

[Darion Cassel](http://darioncassel.me/), Undergraduate Researcher  
[Jack Doerner](https://jackdoerner.net/), Wizard of Oblivion  
[David Evans](https://www.cs.virginia.edu/evans), Faculty Advisor  
[Samuel Havron](https://havron.xyz), Undergraduate Researcher  
[Bargav Jayaraman](https://bargavjayaraman.github.io/), PhD Student  
[Hannah Li](https://github.com/HainaLi), PhD Student  


Contributors: [Natnatee ("Ko") Dokmai](https://github.com/ndokmai), [Richard
Li](https://github.com/Lichard), [Michael Mahoney](https://github.com/mdh3hc), [Xiao Wang](https://github.com/wangxiao1254).





