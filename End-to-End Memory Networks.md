# End-to-End Memory Networks [[pdf]](https://arxiv.org/pdf/1503.08895.pdf)

* Author
	* Sainbayar Sukhbaatar (New York University)
	* Arthur Sziam (Facebook)
	* Jason Weston (Facebook)
	* Rob Fergus (Facebook)
* Title of Conference(Journal)
	* NIPS 2015

## Abstract
* A neural network with a recurrent attention model over a large external network.
* A form of Memory Network but trained end-to-end, requires less supervision and competitive.
* An extension of RNNsearch where multiple computational steps(hops) are performed per output symbol. Flexible to be applied to diverse tasks e.g QA, Language modeling.
* Key concept : multiple computational hops!

## Introduction
* 2 Challenges in AI : To make multiple computational steps in QA or completing a task / To describe long term dependencies in sequential data.
* Models used explicit storage and attention :
  * Memory network
  * Neural Turing Machine
  * Attention Model
* read from, write to the storage, other steps are modeled by neural networks.

## Approach
* $$x = {-b \pm \sqrt{b^2-4ac} \over 2a}$$
* $\\( x(t)=\frac{-b\pm \sqrt{{b}^{2}-4ac}}{2a} \\)$
	