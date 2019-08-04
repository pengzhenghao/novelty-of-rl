# Novelty of Reinforcement Learning Policies - Proposal

:fountain_pen: Peng Zhenghao

:spiral_calendar: August, 2019



** THIS ARTICLE IS NOT FINISHED YET **



## Motivation

* Given two policies: $\pi_1, \pi_2$, which are both functions $a= \pi_{\theta}(action|state)$, how to evaluate the **novelty** or say the **difference** of them?
* Provided a given policy, how to train/guide the training algorithm to **train** a novel policy?



* [learning] use the **reconstrcution error** of a trained auto encoder for policy $\pi_1$ to evaluate how novel the policy $\pi_2$ is.
  * The auto encoder may fails to capture large state/action spaces
  * The reconstruction error is not so "intuitive" as a metic, because so many factors may affect it
  * Training the auto encoder itself is also computational expensive



- [uber] use the embedding of the **state sequence** as some sort of "policy representation"

  - $Embedding_{policy} = f(s_1, s_2, …, s_n)$
  - Can we use both the state and action produced by the policy to depict a policy?

  

* KL Divergence is really famous and useful tool to demonstrate the **difference** of two distrubition,
* especially useful to describe the **bias** of a distrubition when compared to a given one.
* Why don't we expand it to RL region?



## Definition

* Original KL Divergence for two distrubition P and Q:
  * $KL(P || Q) = \mathbb{E}_{x\sim P}[\log \cfrac{P(x)}{Q(x)}]= \mathbb{E}_{x\sim P}[\log P(x)-\log {Q(x)}]$

* Given a policy $\pi$, define experience replay buffer: $R(\pi) = \{(s, a)\sim P\pi\}$
* Define KL divergence for two policy $\pi_1, \pi_2$:
  * $KL(\pi_1||\pi_2) =  \mathbb{E}_{(s, a)\sim R(\pi_1)}[\log \cfrac{\pi_1(a|s)}{\pi_2 (a|s)}]$
  * We can name it with new name ~
  



## Method

### Naive Expression of Novelty Loss

* The KL divergence can be directly use to serve as the loss function, just as what it is used for introduction of the Mean Square Error in machine learning ("nvt" for "novelty"):
  * $L_{nvt} = -\mathbb{E}_{(s, a)\sim R(\pi_{old})}[\log {\pi_{new} (a|s)}]$
* Then the graident is easy to calculate:
  * $\nabla L_{nvt} = -\mathbb{E}_{(s,a)\sim R(\pi_{old)}}[\cfrac{\nabla \pi_{new}(a,s)}{\pi_{new}(a,s)}]$
* We should maintain a replay buffer and store all the (s, a) pair in it, namely the $R(\pi_{old})$



* Now we have two gradient: **Policy Gradient** and **Novelty Gradient**
* Then use **weighted sum** or other techniques to solve this **two-objective optimization** problem 



## Experiment Design & Expected Results

### Experiment 1: Convergence

* **Goal**: verify the convergence of the KL divergence as the samples from replay buffer expanding
* **Subjects**: two given policies, great if they trained from different algorithms.
* **Changed Variables**: the number of samples in the replay buffer
* **Observed Measures**:
  1. $KL(\pi_1||\pi_2)$
  2. $KL(\pi_2||\pi_1)$
* **Potential Figures**: 
  1. Figure: KL vs number of samples
* **Expected Results**:
  1. As number of samples grows, the KL (both) converge to a value.



### Experiment 2: Visualization

* **Goal**: verify if the KL is correspondance to different "distance metrics"
* **Subjects**:
  1. The RAM state embedding, and the distance between different policies
  2. The Auto Encoder embedding, and the distance between different policies
  3. KL (both)
* **Potential Figures**:
  1. Table: $dist(\pi_1, \pi_2)$ and $KL(\pi_1||\pi_2)$ for different $\pi_1, \pi_2$
  2. Figure: 2D visualization figures of (1), (2) embeddings (draw some colors)
* **Expected Results**:
  1. For which pair of policies that KL is large, the distance (both) is large.
  2. For which pair of policies that KL is large, the two points of them in 2D visualization figure are far away from each other.



:warning: ​ It should be note that the Exp1 & 2 require no training, given a pretrained agent to serve as the $\pi_{old}$ .



### Experiment 3: Optimization

** WAIT FOR MORE CONTENT **

* **Goal**: verify if the extra **novelty loss** can help boost novelty
* **Subjects**:
  1. A pretrained agent
  2. An agent trained with novelty loss (*hyper-param: weights of two loss*)
  3. ...
* **Expected Results**:

  ...



## Conclusion

### Future: The Curiosity

...



### Future: Modification for online learning

* To caucluate the novelty, the replay buffer of old policy is used.

* This is not a online-learning fashion.
* It takes large memory and is the bottleneck of computing gradient.

* I can't find a way toward online-learning:
  1. $\nabla L_{nvt} = -E_{(s,a)\sim P_{\pi_{old}}}[\cfrac{\nabla \pi_{new}(a,s)}{\pi(a,s)}]$
  2. $\nabla L_{nvt} = -E_{(s,a)\sim P_{\pi_{new}}}[\cfrac{P_{\pi_{old}}}{P_{\pi_{new}}}\cdot\cfrac{\nabla \pi_{new}(a,s)}{\pi_{new}(a,s)}]$, changed the sample distrubition
  3. $P_\pi (a, s) = \pi(a|s)\cdot P(s)$, Conditional Probability Equation
  4. (2) becomes: $\nabla L_{nvt} = -E_{(s,a)\sim P_{\pi_{new}}}[\cfrac{P_{\pi_{old}}(s)}{P_{\pi_{new}}(s)}\cdot \cfrac{\pi_{old}(a|s)}{\pi_{new}(a|s)}\cdot\cfrac{\nabla \pi_{new}(a,s)}{\pi_{new}(a,s)}]$
  5. The probability of a given state $s$ can not be cauculated. Q.E.D.



## Reference

[learning] : Learning Novel Policies for Tasks

[uber] : Creating a Zoo of Atari-Playing Agents to Cataly Understanding of Deep Reinforcement Learning