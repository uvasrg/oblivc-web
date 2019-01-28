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

This mini-book provides an introduction to secure multi-party computation:

> David Evans, Vladimir Kolesnikov and Mike Rosulek. [_A Pragmatic Introduction to Secure Multi-Party Computation_](https://www.securecomputation.org/), NOW Publishers, December 2018. [[PDF](https://www.securecomputation.org/docs/pragmaticmpc.pdf)]

## Getting Started

**Code: [https://github.com/samee/obliv-c](https://github.com/samee/obliv-c)**

> This repository includes the implementation of Obliv-C and several example applications and benchmarks.


[**Tutorial**](/tutorial) 

> Walks through how to install Obliv-C and build your first data-oblivious application using a simple linear regression example application.

[**Documentation**](/documentation)

> Documentation on Obliv-C's language extensions and library functions.

[**Rust Wrapper**](/rust)

> Calling Obliv-C protocols from Rust programs ([Phillipp
Schoppmann](https://www.informatik.hu-berlin.de/de/Members/schopmaf/))

<a name="projects"></a>

## Projects Using Obliv-C 

   <div class="row3">
   <div class="column3">
### Libraries and Protocols

Fast Oblivious Memories:  
[**floram**](https://gitlab.com/neucrypt/floram)  
[**SqORAM**](/sqoram)

Cryptographic library:  
[**Absentminded Crypto Kit**](https://bitbucket.org/jackdoerner/absentminded-crypto-kit)

[**Half-Gates**](https://mightbeevil.com/halfgates/)
   </div>
   <div class="column3">
### Applications

[**Distributed Learning**](/ppml)  
[Distributed Linear Regression](https://eprint.iacr.org/2016/892)  
[MPC + Differential Privacy](/ppml)

[**Secure Stable Matching**](/matching)

[Decentralized CAs](/dca)

Sub-String Search:  
[Oblivious Knuth-Morris-Pratt](https://github.com/jnayak1/kmp-mpc)


   </div>
   <div class="column3">
### Applications by Others   

[Blind Justice: Fairness with Encrypted Sensitive Attributes](https://arxiv.org/abs/1806.03281)

[Private Nearest Neighbors](https://eprint.iacr.org/2018/289.pdf)  
[Secure Deep Learning](https://github.com/bargavjayaraman/SecureDeepLearning)  

Encrypted email: [**Pretzel**](http://www.cs.nyu.edu/~mwalfish/papers/pretzel-sigcomm17.pdf) (SIGCOMM 2017)

[SECCOMP - The Secure Spreadsheet](https://calctopia.com), Calctopia, 2017.
   </div>
   </div>

## Publications and Talks

   <div class="row">
   <div class="column">

**NeurIPS 2018**: [_Distributed Learning without Distress:
Privacy-Preserving Empirical Risk Minimization_](/docs/neurips2018.pdf)

**PETS 2017**: [_Privacy-Preserving Distributed Linear Regression on High-Dimensional Data_](/docs/distributedregression.pdf)

**CCS 2016**: [_Secure Stable Matching at Scale_](http://oblivc.org/docs/matching.pdf)

**S&amp;P 2016**: [_Revisiting Square-Root ORAM Efficient
Random Access in Multi-Party Computation_](/docs/sqoram.pdf) 

**EuroCrypt 2015**: [_Two Halves Make a Whole: Reducing Data Transfer in Garbled Circuits using Half Gates_](//www.cs.virginia.edu/evans/pubs/ec2015/halfgates.pdf">PDF</a>)

**Obliv-C 2015**: [_Obliv-C: A Language for Extensible
Data-Oblivious Computation_](http://eprint.iacr.org/2015/1153.pdf)


[Full publications list...](/pubs)
   </div>
   <div class="column">

**ECRYPT NET**: [_Secure Multi-Party Computation: Promises, Protocols, and Practicalities_](https://www.jeffersonswheel.org/2017/secure-multi-party-computation-promises-protocols-and-practicalities) (Paris, 27 June 2017) [[SpeakerDeck](https://speakerdeck.com/evansuva/secure-multi-party-computation-promises-protocols-and-practicalities)]

**Federal Trade Commission**: <a href="https://www.jeffersonswheel.org/2016/ftc-visit"><em>Private Data Analysis using Multi-Party Computation</em></a> (joint presentation with Denis Nekipelov, 18 August 2016)

**Aarhus**: <a href="https://www.jeffersonswheel.org/2016/aarhus-workshop-on-theory-and-practice-of-secure-multiparty-computation"><em>From Mercury Delay Lines to Magnetic Core Memories: Progress in Oblivious Memories</em></a> (<a href="http://ctic.au.dk/events/workshops-conferences/mpc-2016/">MPC Workshop</a>, June 2016) [<a href="https://speakerdeck.com/evansuva/from-mercury-delay-lines-to-magnetic-core-memories-progress-in-oblivious-memories">Speaker Deck</a>]

**iDash**: [_Obliv-C: A Simple C Extension for
SMC_](http://www.humangenomeprivacy.org/2015/slides/UV.pdf) (Samee
Zahur's talk at _iDash Privacy &amp; Security Workshop_ 2015, won award
for fastest "Hamming Distance" execution)

**CROSSING**: [_Multi-Party Computation for the
Masses_](http://www.cs.virginia.edu/~evans/talks/crossing2015/)
(includes video) (CROSSING Conference, Darmstadt, June 2015)

[Full talks list...](/talks)
   </div>
   </div>

## People

[Bargav Jayaraman](https://bargavjayaraman.github.io/), PhD Student  
Nathaniel Grevatt, Undergraduate Researcher

[David Evans](https://www.cs.virginia.edu/evans), Faculty Advisor  


### Alumni

[**Samee Zahur**](https://www.cs.virginia.edu/~sza4uq/), Project Founder and Leader (now at Google)  
[Darion Cassel](http://darioncassel.me/), Undergraduate Researcher (now at CMU)  
[Natnatee ("Ko") Dokmai](https://github.com/ndokmai), Undergraduate Researcher (now at Indiana University)  
[Jack Doerner](https://jackdoerner.net/), Wizard of Oblivion (now at Northeastern)  
[Samuel Havron](https://havron.xyz), Undergraduate Researcher (now at Cornell)  
[Hannah Li](https://github.com/HainaLi), Undergraduate at Masters Student (now at Facebook)  
[Jesse Nayak](https://github.com/jnayak1), Undergraduate Researcher
    

Other Notable Contributors:  
[Richard
Li](https://github.com/Lichard), [Michael Mahoney](https://github.com/mdh3hc), [Xiao Wang](https://github.com/wangxiao1254).





