# End-to-End Memory Networks [[pdf]](https://arxiv.org/pdf/1503.08895.pdf)

---

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

## Model structure
![](https://user-images.githubusercontent.com/8953934/38286863-9633404a-3802-11e8-8100-bd1929f11694.JPG)

* Input1 : *{x<sub>i</sub>}* - memory(sentences for QA, words for LM)
* Input2 : *q* - question for QA, constant for LM
* target : <img src="https://latex.codecogs.com/svg.latex?\hat{a}" title="\hat{a}" /> - answer for QA, next word for LM  

##### Input memory representation
* *{x<sub>i</sub>}* are converted into memory vectors *{m<sub>i</sub>}* using matrix *A*  
*m<sub>i</sub>* = *Ax<sub>i</sub>*
* *q* is embedded into internal state *u* with matrix *B*  
*u* = *Bq*
* compute match between *u*u and each memory *m<sub>i</sub>* as  
*p<sub>i</sub>* = Softmax(*u<sup>T</sup>m<sub>i</sub>*)

##### Output memory representation
* Each *x<sub>i</sub>* is converted to output vector with matrix *C*  
*c<sub>i</sub>* = *Cx<sub>i</sub>*
* response vector from the memory *o* is:  
<img src="https://latex.codecogs.com/svg.latex?o&space;=&space;\sum_{i}&space;p_{i}c_{i}" title="o = \sum_{i} p_{i}c_{i}" />

##### Final prediction
* <img src="https://latex.codecogs.com/svg.latex?\hat{a}" title="\hat{a}" /> = Softmax(*W(o+u)*)

### Multiple Layers
* *u<sup>k+1</sup> = u<sup>k</sup> + o<sup>k</sup>*
* Need embedding matrices for each layer : *A<sup>k</sup>, C<sup>k</sup>*
* <img src="https://latex.codecogs.com/svg.latex?\hat{a}" title="\hat{a}" /> = Softmax(*Wu<sup>K+1</sup>*) = Softmax(*W(o<sup>K</sup>+u<sup>K</sup>)*)
* Use weight tying

#### 1. Adjacent
* *A<sup>k+1</sup> = C<sup>k</sup>*, *W<sup>T</sup> = C<sup>K</sup>* , *B = A<sup>1</sup>*

#### 2. Layer-wise(RNN-like)
* *A<sup>1</sup> = A<sup>2</sup> = ... = A<sup>K</sup>*
* *C<sup>1</sup> = C<sup>2</sup> = ... = C<sup>K</sup>*
* Use another matrix *H* to get *u*  
*u<sup>k+1</sup>* = *Hu<sup>k</sup>* + *o<sup>k</sup>*
*Using this, the model can be cast as a traditional RNN  
*u* is a hidden state, *p* is internal output.
* ?????? 이해 안됨.... ㅋㅋㅋㅋ

## Details for QA task
* *x<sub>ij</sub>*, the *j*th word of sentence *i* is a one-hot vector
* |Vocab| = 177

### Sentence representation

#### Bag-of-words
* <img src="https://latex.codecogs.com/svg.latex?m_{i}&space;=&space;\sum_{j}&space;Ax_{ij}" title="m_{i} = \sum_{j} Ax_{ij}" />  
Same for *c<sub>i</sub>*, *u*

#### Position encoding
* Let the order of the words affects *m<sub>i</sub>*
* <img src="https://latex.codecogs.com/svg.latex?m_{i}&space;=&space;\sum_{j}&space;l_{j}\cdot&space;Ax_{ij}" title="m_{i} = \sum_{j} l_{j}\cdot Ax_{ij}" /> dot means an element-wise multiplication.
* For column vector *l<sub>j</sub>*, *l<sub>kj</sub>* = (1-*j/J*) - (*k/d*)(1-2*j/J*). *J* = #words in the sentence, *d* is the dimension of the embedding.

### Temporal encoding
* Let the model understand temporal context, i.e. "Sam walks into the kitchen. ... Sam walks into the bedroom." the model needs to understand that Sam is in the bedroom after he is in the kitchen.
* <img src="https://latex.codecogs.com/svg.latex?m_{i}&space;=&space;\sum_{j}&space;Ax_{ij}&space;&plus;&space;T_{A}(i)" title="m_{i} = \sum_{j} Ax_{ij} + T_{A}(i)" /> where *T<sub>A</sub>(i)* is the *i*th row of a special matrix *T<sub>A</sub>* that encodes temporal information. 
* The output embedding is augmented in the same way.

### Injecting random noise 
* Add "dummy" memories to regularize *T<sub>A</sub>*. At training time, randomly add 10% of empty memories to the stories.

## Details for Language Modeling task

* Operate on word level, as opposed to the sentence level. The previous *N* words in the sequence including the current word are embedded into memory separately. Employ the temporal embedding.
* No question is needed. *q* is fixed to a constant vector 0.1. Use layer-wise weight sharing.
* The "sequence" over which the network is recurrent is not in the text but in the memory hops.

## Conclusions and Future Work
* Unable to exactly match the performance of the memory networks trained with strong supervision. 
* Fail on several of the 1k QA tasks.
* Smooth lookups may not scale well to the case where a largeer memory is required.
* Plan to explore multiscale notions of attention or hashing.
