# Novelty of Reinforcement Learning Policies - Proposal

:fountain_pen: Peng Zhenghao

:spiral_calendar: August, 2019



## Motivation

* Given two policies: $\pi_1, \pi_2$, which are both functions $a= \pi_{\theta}(action|state)$, how to evaluate the **novelty** or say the **difference** of them?
* Provided a given policy, how to train/guide the training algorithm to **train** a novel policy?



* [learning] use the **reconstrcution error** of a trained auto encoder for policy $\pi_1$ to evaluate how novel the policy $\pi_2$ is.
  * The auto encoder may fails to capture large state/action spaces
  * The reconstruction error is not so "intuitive" as a metic, because so many factors may affect it
  * Training the auto encoder itself is also computational expensive



* KL Divergence is really famous and useful tool to demonstrate the **difference** of two distrubition
* Why don't we expand it to RL region?



## Definition

* KL Divergence:
  * $KL(P || Q) = \mathbb{E}_{x\sim P}[\log \cfrac{P(x)}{Q(x)}]= \mathbb{E}_{x\sim P}[\log P(x)-\log {Q(x)}]$



* Given a policy $\pi$, define experience replay buffer: $R(\pi) = \{(s, a)\sim P\pi\}$
* Define KL divergence for two policy $\pi_1, \pi_2$:
  * $KL(\pi_1||\pi_2) =  \mathbb{E}_{(s, a)\sim R(\pi_1)}[\log \cfrac{\pi_1(a|s)}{\pi_2 (a|s)}]$
  * We can name it with new name ~
  



## Method

### Naive Expression of Novelty Loss

* The KL divergence can be directly use to serve as the loss function, just as what it is used for introduction of the Mean Square Error in machine learning ("nvt" for "novelty"):
  * $L_{nvt} = -\mathbb{E}_{(s, a)\sim R(\pi_1)}[\log {\pi_2 (a|s)}]$



### Modification for Online-learning

* However, the "distrubition shift" is happen when using the online-learning algorithm like PPO.





### Extra Idea: Policy Embedding

* Very simple idea: Using the hidden expression of an auto-encoder that takes $\{(s, a)\sim P_{\pi}\}$ as inputs
* Just like what [Uber] did we can then visualize the embedding by dimension reduction



## Experiment Design & Expected Results

### Experiment 1: Convergence

* **Subjects**: two given policies, great if they trained from different algorithms.
* **Goal**: verify the convergence of the KL divergence as the samples from replay buffer expanding
* **Factors**: the number of samples in the replay buffer
* **Observed Measures**:
  1. $KL(\pi_1||\pi_2)$
  2. $KL(\pi_2||\pi_1)$

* **Potential figures & tables**: 
  1. KL vs number of samples figure



### Experiment 2: 





## Conclusion



## Reference

[learning] : Learning Novel Policies for Tasks

[uber] : Creating a Zoo of Atari-Playing Agents to Cataly Understanding of Deep Reinforcement Learning