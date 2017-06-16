+++
title = "Decentralized Certificate Authorities"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

The security of TLS depends on trust in certificate authorities, and
that trust stems from their ability to protect and control the use of
a private signing key.  The signing key is the key asset of a
certificate authority (CA), and its value is based on trust in the
corresponding public key which is primarily distributed by browser
vendors.  Compromise of a CA private key represents a single
point-of-failure that could have disastrous consequences, so CAs go to
great lengths to attempt to protect and control the use of their
private keys. Nevertheless, keys are sometimes compromised and may be
misused accidentally or intentionally by insiders. 

<center>
<a href="/images/dca.png"><img src="/images/dca-small.png" width=750 height=500></a>
</center>

We propose a new model where a CA's private key is split among
multiple parties, and signatures are produced using a secure
multi-party computation protocol that never exposes the actual signing
key. This could be used by a single CA to reduce the risk that its
signing key would be compromised or misused. It could also enable new
models for certificate generation, where multiple CAs would need to
agree and cooperate before a new certificate can be generated, or even
where certificate generation would require cooperation between a CA
and the certificate recipient (subject). We demonstrate the
practicality of this concept with an evaluation of a prototype
implementation that uses secure two-party computation to generate
certificates signed using ECDSA on curve secp192k1. We show that using
Amazon AWS EC2 c4.2xlarge nodes to jointly sign a certificate costs as
little as 28.2-32.6 cents without bandwidth cost and approximately
8.54 dollars with bandwidth cost.

<center>
</center>

## Paper

Bargav Jayaraman<sup>&dagger;</sup>, Haina Li<sup>&dagger;</sup>, David Evans. <a
href="/docs/dca.pdf"><em>Decentralized Certificate
Authorities</em></a>. 11 June 2017. <a href="https://arxiv.org/abs/1706.03370">arXiv:1706.03370</a> [[PDF](https://arxiv.org/pdf/1706.03370.pdf), 12 pages] (&dagger; The first two authors contributed as co-equal first authors.)

## Code

(Coming Soon - please contact us if you would like early access.)

## People

[Bargav Jayaraman](https://bargavjayaraman.github.io/)  
[Haina Li](https://github.com/HainaLi)  
[David Evans](https://www.cs.virginia.edu/evans)  






