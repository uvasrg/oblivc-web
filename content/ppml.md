+++
title = "Privacy-Preserving Machine Learning"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

_Distributed learning_ (sometimes known as _federated learning_)
allows a group of independent data owners to collaboratively learn a
model over their data sets without exposing their private data. 

Our approach combines _differential privacy_ with secure _multi-party
computation_ to both protect the data during training and produce a
model that provides privacy against inference attacks.

We explore two popular methods of differential privacy, output
perturbation and gradient perturbation, and advance the
state-of-the-art for both methods in the distributed learning
setting. In our output perturbation method, the parties combine local
models within a secure computation and then add therequired
differential privacy noise before revealing the model. In our gradient
perturbation method, the data owners collaboratively train a global
model via aniterative learning algorithm. At each iteration, the
parties aggregate their local gradients within a secure computation,
adding sufficient noise to ensure privacy before the gradient updates
are revealed. For both methods, we show that the noise can be reduced
in the multi-party setting by adding the noise inside the
securecomputation after aggregation, asymptotically improving upon the
best previous results. Experiments on real world data sets demonstrate
that our methods providesubstantial utility gains for typical privacy
requirements.

<center>
<iframe width="560" height="315"
	src="https://www.youtube-nocookie.com/embed/rwyWiDyVmjE"
	frameborder="0" allow="accelerometer; autoplay; encrypted-media;
	gyroscope; picture-in-picture" allowfullscreen></iframe>
</center>

## Code

<a href="https://github.com/bargavj/distributedMachineLearning"><em>https://github.com/bargavj/distributedMachineLearning</em></a>

## Papers

Bargav Jayaraman, Lingxiao Wang, David Evans and Quanquan Gu. [_Distributed Learning without Distress:
Privacy-Preserving Empirical Risk Minimization_](/docs/neurips2018.pdf). <a href="https://nips.cc/Conferences/2018/">32<sup>nd</sup> Conference on Neural Information Processing Systems</a> (NeurIPS). Montreal, Canada. December 2018. (<a href="//www.cs.virginia.edu/evans/pubs/neurips2018/neurips2018.pdf">PDF</a>, 19 pages, including supplemental materials)

Lu Tian, Bargav Jayaraman, Quanquan Gu, and David Evans. [_Aggregating
Private Sparse Learning Models Using Multi-Party Computation_](/docs/pmpml.pdf). In
[Private Multi‑Party Machine Learning](https://pmpml.github.io/PMPML16/)
(NIPS 2016 Workshop), Barcelona, 9 December 2016.  ([PDF](/docs/pmpml.pdf), 6 pages)

<center>
<img src="/images/mpcaggregation.png" width=650>
</center>







