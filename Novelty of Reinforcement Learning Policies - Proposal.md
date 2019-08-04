# Novelty of Reinforcement Learning Policies - Proposal

:fountain_pen: Peng Zhenghao

:spiral_calendar: August 4, 2019



## Motivation

* Given two policies: $\pi_1, \pi_2$, which are both functions $a= \pi_{\theta}(action|state)$, how to evaluate the **novelty** or say the **difference** of them?
* Given a policy, how to train/guide the training algorithm to **train** a novel policy?



* [learning] use the **reconstrcution error** of an auto-encoder trained from policy $\pi_1$ when evaluating on policy $\pi_2$ to serve as the "novelty metric".
  * The auto encoder may fails to capture large state/action spaces
  * The reconstruction error is not so "natural" as a metic, because so many factors may affect it
  * Training the auto encoder itself is also computational expensive and not reliable



- [uber] use the embedding of the **state sequence** as some sort of "policy representation"

  - $Embedding_{policy} = f(s_1, s_2, …, s_n)$
  - Can we use both the state and action produced by the policy to depict a policy? 
- Namely $Embedding_{policy} = f(s_1, a_1, s_2, …, s_n, a_n)$
  - That is because a state sequence may not imply a fixed action sequence, when the environment is stochastic.
  
  

* KL Divergence is really famous and useful tool to demonstrate the **difference** of two distrubition,
* especially useful to describe the **bias** of a distrubition when compared to a given one.
* Why don't we expand it to RL region?



## Definition

* Original KL Divergence for two distrubition P and Q:
  * $KL(P || Q) = \mathbb{E}_{x\sim P}[\log \cfrac{P(x)}{Q(x)}]= \mathbb{E}_{x\sim P}[\log P(x)-\log {Q(x)}]$

* Given a policy $\pi$, define experience replay buffer: $R(\pi) = \{(s, a)\sim P\pi\}$
* Define KL divergence for two policy $\pi_1, \pi_2$:
  * $KL(\pi_1||\pi_2) =  \mathbb{E}_{(s, a)\sim R(\pi_1)}[\log \cfrac{\pi_1(a|s)}{\pi_2 (a|s)}] = KL(\pi_1||\pi_2) =  \mathbb{E}_{(s, a)\sim R(\pi_1)}[\log \pi_1(a|s) - \log \pi_2 (a|s)]$
  * We can name it with new name ~
  * Most important factor is we sample (s, a) from "**old policy replay buffer**"
  



## Method

### Naive Expression of Novelty Loss

* The KL divergence can be directly use to serve as the loss function, just as what it is used for introduction of the Mean Square Error in machine learning ("nvt" for "novelty"):
  * $L_{nvt} = -\mathbb{E}_{(s, a)\sim R(\pi_{old})}[\log {\pi_{new} (a|s)}]$
* Then the graident is easy to calculate:
  * $\nabla L_{nvt} = -\mathbb{E}_{(s,a)\sim R(\pi_{old)}}[\cfrac{\nabla \pi_{new}(a,s)}{\pi_{new}(a,s)}]$
* We should maintain a replay buffer and store all the (s, a) pair in it, namely the $R(\pi_{old})$



* Now we have two gradient: **Policy Gradient** and **Novelty Gradient**
* Then use **weighted sum** or other techniques to solve this **two-objective optimization** problem 



### Mocking Curiosity-driven Learning

* [curiosity] argues that curiosity as the only reward can also lead to great performance
* It use $||f(s_t, a_t) - s_{t+1}||^2$ as the reward, wherein f is a function predicting the next state $s_{t+1}$
* Define a new curiosity:
  * $KL(\pi_{i}||\pi_{i+1})$, wherein $i$ denotes the training iteration
  * Use $L_{nvt}$ defined above as the **only** loss
  * Then the training is complelty **offline** and only need dataset $R(\pi_i)=\{(s^{\pi_i}_t, a^{\pi_i}_t)\}$
* It should work for those environments which curiosity-driven learning work.



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



* :warning: ​ It should be note that the Exp1 & 2 require no training, given a pretrained agent to serve as the $\pi_{old}$.



### Experiment 3: Optimization

* **Goal**: verify if the extra **novelty loss** can help boost novelty / performance
* **Subjects**:
  1. A pretrained agent
  2. An agent trained with novelty loss (*hyper-param: weights of two loss*)
* **Potential Figures**:
  1. Figure: novelty vs total reward
* **Expected Results**:
  1. (High Probability) As novelty grows, total reward keep steady
  2. (Medium Probability) As novelty grows, total reward grows

* :warning: Accroding to [curiosity] and [soft ac], encouraging diverse observations lead to **robustness** and **better performance**. — That is what we call **exploration**



### Experiment 4: Compare to Curiosity-driven Learning

* **Goals**: verify if KL can be used in curiosity-driven learning framework
* **Potential Figures**:
  1. Figure: training curve of KL
  2. Figure: training curve of curiosity-driven learning
  3. Figure: training curve of baseline (external reward)
  4. Figure: training curve of KL + external reward



## Conclusion

* I doubt somebody may have done this, since it sound really natural
* It's a good way to encourage "exploration"



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

[curiosity] : Large-Scale Study of Curiosity-Driven Learning (ICLR'19)

[soft ac] : Soft Actor-Critic: Off-Policy Maximum Entropy Deep Reinforcement Learning with a Stochastic Actor *(Add a entropy term in Q function)*