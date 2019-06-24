# My 10 takeaways from the 2019 Intelligent Vehicle Symposium

!!!! WORK IN PROGRESS !!!!
*If some pictures are not loaded, try to refresh the page.*

| ![2019 Intelligent Vehicle Symposium](media/pics/iv19.jpg "2019 Intelligent Vehicle Symposium")  |
|:--:|
| *2019 Intelligent Vehicle Symposium* |

```bash
TL;DR:
- Look how other industries (e.g. aviation) deal with risk assessment and decision making under uncertainty.
- Stop separating prediction and planning and use POMDP formulations.
- Combine learning and planning (e.g. MCTS + RL).
- Interaction-aware planning.
- Bring interpretability into learning-based methods.
# - Generalize with decomposition or image-like states.
# - Think distribution, instead of mean expectation or point-estimate.
- Abandon the *Vision Zero* buzzword.
- AD could be a disaster if perpetuating inequalities and ignoring shared mobility.
- Some demos were impressive. But far from series production. # -> Back to work.
```

Last week, experts from the autonomous driving (AD) world came together for the 2019 IEEE Intelligent Vehicle Symposium ([IV19](https://iv2019.org/)) in Paris to discuss about research and applications for autonomous driving.

This **30th edition** included one full day of **workshops and tutorials**, two days of **oral presentations**, **poster sessions** and **keynotes** as well as one whole day of **on-track demonstrations**. Not to mention the several **social events** that were ideal for deeper discussions and intense debates.

Some interesting figures:

- Around `700` attendees.
- `525` paper submissions.
- `62%` acceptance rate.
- More than `1100` reviewers.
- Authors coming from `37` countries, with almost **one third from Germany**, `16%` China, `14%` USA, `7%` France, `5%` Japan. *I sometime felt more in Germany than in France!*

From the [List of Events for Autonomous Driving Engineers and Researchers](https://github.com/esrlabs/Conferences-for-Autonomous-Driving), IV was identified as a top priority for our [Machine Learning Team](https://www.esrlabs.com/works/autonomous/). I would like to **thank [ESR Labs AG](https://www.esrlabs.com)** for this opportunity to exchange, during four days, with other engineers, researchers, practitioners, and students, from industry, universities and government agencies about the latest trends, insights and challenges related to the fast-moving field of AD.

In this post, I would like to **share some personal highlights** about the latest exciting trends in decision making for autonomous driving.

Disclaimers:

- These notes are **personal highlights** and do **not aim at being an exhausted review** of the IV19 publications.
- I mainly focus on the topic of **decision making for AD** (as opposed to perceptions, localisation or control for instance).
- Many subjects I am discussing are new to me. I really hope I did not misinterpret any concept when referencing to papers. Please notify me if any statement or interpretation is incorrect!

## Structure

- [We are living in a POMDP](#we-are-living-in-a-pomdp)
  - [POMDP Benefits](#pomdp-benefits)
  - [POMDP Formulation](#pomdp-formulation)
  - [POMDP Solvers](#pomdp-solvers)
- [Reinforcement Learning](#reinforcement-learning)
  - [Difference Learning / Planning](#difference-learning-/-planning)
  - [Pure RL for decision making in AD is hard](#pure-rl-for-decision-making-in-ad-is-hard)
  - [Using RL for parameter tuning](#using-rl-for-parameter-tuning)
  - [Combining Learning and Planning](#combining-learning-and-planning)
- [Learning-based versus Non-Learning-based Approaches](#learning-based-versus-non-Learning-based-approaches)
  - [Benefits of the Learning / Non-Learning Combination](#benefits-of-the-learning-/-non-learning-combination)
  - [Learning-based Methods Now used in Prediction](#learning-based-methods-now-used-in-prediction)
  - [Interpretability is not an Option](interpretability-is-not-an-option)
  - [In Validation Process](in-validation-process)
- [Uncertainty](#Uncertainty)
  - [Sources of uncertainty](#sources-of-uncertainty)
  - [Heteroscedastic Uncertainties](#heteroscedastic-uncertainties)
  - [Unfreezing the Robot under Occlusion](#unfreezing-the-obot-under-occlusion)
  - [Reducing Uncertainty](#reducing-uncertainty)
- [Generalization in decision modules](#generalization-in-decision-modules)
  - [Generic scene representation](#generic-scene-representation)
  - [Scene decomposition methods](#scene-decomposition-methods)
- [Considering interaction between traffic participants](#considering-interaction-between-traffic-participants)
  - [Combining prediction and planning](#combining-prediction-and-planning)
  - [Interaction-aware prediction](#interaction-aware-prediction)
  - [Negotiation as a next step](#negotiation-as-a-next-step)
- [Scenarios and Datasets](#scenarios-and-datasets)
  - [Needs for New Datasets](#needs-for-new-datasets)
  - [Generating edge-case scenarios](#generating-edge-case-scenarios)
  - [Common Critical Scenarios](#common-critical-scenarios)
  - [Defining Scenarios](#defining-scenarios)
- [Do not Forget Safety](#do-not-forget-safety)
  - [Safety Proof for AD](#safety-proof-for-ad)
  - [RSS](#rss)
  - [Reachability analysis](#reachability-analysis)
  - [Risk Assessment and Safety Checkers](#risk-assessment-and-safety-checkers)
- [AD and Society](#ad-and-society)
  - [Social Benefit](#social-benefit)
  - [Social Acceptance](#social-acceptance)
- [Demonstrations](#demonstrations)
  - [Test Day](#test-day)
  - [Miscellaneous Comments](#miscellaneous-comments)
  - [Discussions With Some Sponsors](#discussions-with-some-sponsors)
- [Conclusion](#conclusion)
- [A Last Word](#a-last-word)
- [References](#references)

## We are living in a POMDP

**_“POMDP”_** stands for _Partially Observable Markov Decision Process_. It is a *model that can be used to formulate the **problem of decision making under uncertainty**. It is therefore very interesting for **AD applications**.

In this first section, I review the different **POMDP implementations** used for IV19, mentioning the **benefits for AD applications of such a formulation** , and compare the different **solving techniques**. The section is structured as follows:

- Benefits of using POMDP for decision making in AD.
- Comparison of POMDP formulations.
- Comparison of POMDP solving techniques.

Additional facts

- (Schörner et al. 2019) received the **Best Paper Award** for **“Predictive Trajectory Planning in Situations with Hidden Road Users Using Partially Observable Markov Decision Processes”**.
- The term _POMDP_ appeared in `4%` of the released papers.
- Some were using the term _Mixed Observable MDP_ instead of _Partially Observable MDP_ to indicate that a portion of the underlying state is observable (e.g. the ego vehicle information).
- Many research labs and companies were showing interest in POMDPs. For instance, all participants received in the symposium bag a position offer from Daimler (IV19 sponsor), looking for a position about decision making with experience with POMDP.

The first day I could take part to the workshop _“Prediction and Decision Making for Socially Interactive Autonomous Driving_, shortened [**SIPD**](https://sites.google.com/berkeley.edu/iv19-interaction).
It was fantastic to have **Mykel Kochenderfer**, from the Department of Aeronautics and Astronautics at Stanford University, since his lab is regarded as one top reference in the topic of **decision making under uncertainty**. He explained how he is trying to **transfer to the automotive domain** what he has learnt from his **works for the aviation domain** such as traffic collision avoidance system. **Looking for solutions in other industries** (e.g. _aviation_ and _pharmaceutic_ sectors) was a recurring idea during the four days. Especially for **safety validation** and for **risk assessment**.

### POMDP Benefits

The POMDP formulation offers several advantages.

- As an MDP, it is meant for **sequential decision process**, seeking optimal policies for **long-term objectives**, contrary to reactive approaches. It is for instance benefitial for highway exit cases where a long planning horizon is required.
- As a **belief-state planner** with **probabilistic transition and measurement models**, it can deal with several sources of uncertainty to make **uncertainty-aware decisions**:
  - uncertainty in sensor measurements (perception)
  - uncertainty in car controls
  - partial observability and stochasticity of other traffic participants
  - limited information and understanding about the environment
- As a **generative model**, it can use its belief on the intention of other participants to **make prediction**. Prediction and planning can hence be done in the same module, **avoiding the loss of information**.

### POMDP Formulation

A POMDP is defined by six elements (plus a discount factor and a horizon):

- A **state** space,
- An **observation** space,
- An **action** space,
- A **reward** function,
- A **measurement** model,
- And a **transition** model.

Authors usually reserve one section per element, together with a part about **_belief tracking_**.

For the Markov property to be valid, the **state** should contain all information necessary to predict future states. But choosing the right level of representation is critical, as discussed in the subsequent section on [generalization](#generalization-in-decision-modules). **Static background knowledge** such as road network topology, road geometry, static objects like buildings or parking vehicles **should not be included in the state space** as it might be accessed at any time.

In a **partially observable MDP** (POMDP), the agent **does not have a full access to the state**. The **probabilistic distribution over the current state** is available but not the state itself. It makes use of the observation to **maintain** (or _track_) some belief over the state. This belief will be used to select an action.

Most belief trackers use some **particle filters as a belief update algorithm**. (Bouton et al. 2019b) shows that an **ensemble of RNN** can **emulate the role of a particle filter**. Several RNNs are trained on independent data sets and during inference, each one outputs a belief. As I understood, these believes are treated uniformly.

The **observation** is usually a manually defined **vector of features** and sometimes an **image** such as a top view scene. It can also be a concatenation of both, as in (Folkers, Rick, and Christof 2019) or in (Pusse and Klusch 2019) where the last received reward, the last executed action and the current car velocity come to enrich the car intention RGB image.

| ![The input of the RL network is composed of a ternary perception map and a target state. Source: (Folkers, Rick, and Christof 2019).](media/pics/folkers_hybrid_input.PNG "The input of the RL network is composed of a ternary perception map and a target state. Source: (Folkers, Rick, and Christof 2019).")  |
|:--:|
| *The input of the RL network is composed of a ternary perception map and a target state. Source: (Folkers, Rick, and Christof 2019).* |

The **observation function** usually models the **perception noise**. For instance, (Bouton et al. 2019) uses a Gaussian distribution around the position and velocity. They also **add false positive** and **false negative detections** of cars and pedestrian. This should increase the **robustness** of the approach.

The **hidden part of the state** (sometimes called **_latent variable_**) usually describes the **intention** of other participants. It can be one **manoeuvre classification** (e.g. _the other vehicle is completing the "go straight" manoeuvre_), a goal point or **target destination** (e.g. _the other vehicle wants to reach the third exit of the round-about_), or an **intended route** among a set of predefined routes as in (Joonatan and Folkesson 2019). Sometimes, other drivers are assumed to follow **driver parametric models** such as Intelligent Driver Model (**IDM**). In this case, the parameters are hidden, and their values are tracked within the belief.

Most approaches apply the **path-velocity decomposition**. This means the **path** (list of waypoints) is planned separately from the **velocity profile**. In other words, one decides for the **steering commands** (_lateral control_), while the other takes care of the **acceleration** (_longitudinal control_). This reduces the problem size. Most IV19 contributions using POMDPs model the sequential decision problem about the **longitudinal control** as a POMDP, while some **pre-defined paths** can be extracted from the map. For instance (Bouton et al. 2019b) uses the discrete set {`−4m/s2`, `−2m/s2`, `0m/s2`, `2m/s2`}. While most **state spaces** are **continuous** or **hybrid** (some features are continuous while other are discrete), working with a **continuous action space** is still hard and not so common. The [POMCPOW](https://github.com/JuliaPOMDP/POMCPOW.jl) algorithm from (Sunberg and Kochenderfer 2017) uses progressive widening and weighted particle filtering to approximately solve POMDPs with continuous state, action, and observation spaces. It has been mentioned several times during the SIPD workshop and is worth being investigated.

I was surprised to see most POMDPs working with **low level actions** (acceleration mainly), as opposed to some **semantic manoeuvres**. (Schörner et al. 2019) suggests reasoning about higher level behaviours. For instance, _lane changes_, _overtaking_ or _following the vehicle in front_. An underlying **motion planning unit** can then implement the selected manoeuvre into low level commands. Most participants to the workshop argue for such a **hierarchy between the decision modules**, as the 3M concept of (Chauvin 2018), and the use of **higher abstraction actions** in the POMDP. One challenge is the **communication and especially the feedback loop** between the manoeuvre selection module and the motion planning module (MPC-based for instance).

(Folkers, Rick, and Christof 2019) note that manoeuvres _drive_ and _stop_ require a rather different behaviour of the agent. Hence two policies (the _DRIVER_ and the _STOPPER_) are separated, using **two different reward functions**. It requires a **switch**, such as the **state machine** of (Kapania et al. 2019).

**Reward functions** are usually combination of several terms. It shoud encourage the agent to _reach the goal_, _avoid collisions_, _avoid deviation from a predefined path_ or _from a desired speed_, consider _passenger comfort_, consider _social behaviour_ ... The question is then **how to weight the different contributions**. This can be very challenging, as acknowledged by (Schratter et al. 2019a) where a tuning of the reward function is performed.

One could used **inverse reinforcement learning** (IRL) to determine appropriate values for the reward function. (Sun et al. 2019) learns the cost function of an MPC is via IRL, but I did not see any IRL used for POMDP. For sure this will be done in the future.

(Folkers, Rick, and Christof 2019) organizes the training into two phases and **varies the weighting of each reward contributions** between both. When the policy learns to get high rewards in the first phase, additional behaviours such as an appropriate speed and small steering angles are considered.

| ![Interesting to see how the belief tracker of the POMDP maintains hypotheses about occluded pedestrian. Source: (Schratter et al. 2019).](media/pics/schratter_belief.PNG "Interesting to see how the belief tracker of the POMDP maintains hypotheses about occluded pedestrian. Source: (Schratter et al. 2019).")  |
|:--:|
| *Interesting to see how the belief tracker of the POMDP maintains hypotheses about occluded pedestrian. Source: (Schratter et al. 2019).* |

Let's now consider the last two elements of the POMDP formulation: the **measurement** model and the **transition** model. Here, both models are supposed to be known, leading to a planning problem (see the section on [Difference Learning / Planning](#difference-learning-/-planning)). They express the **probability** of ending in an arbitrary state (resp. emitting an observation) given the selected action and the current state.

It is not always possible to represent **directly their probability distributions**. A commonly used approach is to use a **generative model** instead. From a given state and action which, it generates a new hidden state (**transition** model) and observation (**measurement** model).

The transition model usually holds **one part for the ego vehicles** whose state is fully known and **another part for the dynamic obstacles**. The deterministic _bicycle kinematic model_ of (Kong et al. 2015) is widely used to derive the new position, based on the current ego position, the current vehicle and the selected acceleration.

**Describing the transition for other participants** is more challenging since it requires a **driver behaviour model**. Such models are not restricted to forward simulation-based motion planning. They are also used as transition models for iterative trajectory prediction and for filtering-based intention estimation (e.g. with DBN). One simple option is to assume **constant velocity** (CV) of the observed vehicle. It can be extended with **additive noise** on both the position and the velocity, as implemented in (Schörner et al. 2019). The volume of noise can be fix or not: (Schulz et al. 2019) learns the **variance in a driver’s actions** to have a **context-dependent magnitude of uncertainty** in the transition model.

Another possibility is to infer the action of other participant, by assuming they follow the Intelligent Driver Model (IDM). IDM is a _crash-free microscopic car-following model_ that is widely applied for driver intention estimation and for **modelling how others react to specific plans** of an AD vehicle. It estimates **longitudinal dynamic** speed controls.

Besides point-mass models and probabilitic approaches, (Schratter et al. 2019a) uses a simple **reachability** model. Depending on the current pedestrian state, future positions for the pedestrian are computed based on a **set of possible acceleration values**.

### POMDP Solvers

Solving the POMDP means **finding the optimal policy**, i.e. the function that tells which action to take given any state to **maximize some notion of cumulative reward**. As a probabilistic non-linear optimization problem under state uncertainty, it is hard to solve, and many methods rely on the **approximation of the optimal policy**.

The **lack of documentations or tutorials** of common solvers was mentioned several times. [POMDPs.jl](http://juliapomdp.github.io/POMDPs.jl/latest/), a Julia-based interface for defining, solving, and simulating POMDPs, was praised for its usability and active development.

A POMDP solver can be said to be **_“offline”_**, if the policy is determined beforehand, or **_“online”_** if it seeks the optimal immediate action **for the current belief**. Most approaches presented at IV19 use **online solvers**.

| ![Example of construction of a policy tree in an online solver. [Source](https://papers.nips.cc/paper/5189-despot-online-pomdp-planning-with-regularization.pdf)](media/pics/somani_despot_tree.PNG "Example of construction of a policy tree in an online solver. [Source](https://papers.nips.cc/paper/5189-despot-online-pomdp-planning-with-regularization.pdf)")  |
|:--:|
| *Example of construction of a policy tree in an online solver. [Source](https://papers.nips.cc/paper/5189-despot-online-pomdp-planning-with-regularization.pdf)* |

The notion of **_tree_** is important for **online** solving. From one **initial state** (or belief on it), one could **consider all the possible actions** and sample from the transition model to derive new states (and new believes on them). One option could then repeat the process to **expand the policy tree up to a given horizon**. Finally, the **reward collected on each path** in this tree could be counted and the first action on the best path can be selected. This is a very simplified (an non-efficient) description of the idea of online methods. An efficient method to do that is to use **Monte Carlo Tree Search** ([MCTS]( https://en.wikipedia.org/wiki/Monte_Carlo_tree_search)). This type of algorithms is particularly appropriate for **online decision-making**. Here are some advantages:

- Generating the **full tree** would be very expensive. Instead, MCTS works by **sampling episodes**.
- Contrary to optimisation methods like **A***, MCTS is said **“anytime”**, i.e. it **can return a valid solution even if it is interrupted before it ends**. It is expected to find better and better solutions the longer it keeps running.
- For the construction of the belief tree to be efficient, it is important to **guide the search with a heuristic**. At each of the action nodes, a **utility function** `U(b, a)` (or _value function_), tries to **estimate the cumulative reward** that the agent will get if it takes the action `a` being in belief `b` and then follows the policy. This heuristic, together with the **number of times the action node has been visited** serve to decide which node to choose when expanding the tree (UCB-based exploration and exploitation). One very promising approach is to use learning methods (like RL) to **learn this utility function** (detailed in the section [Combining Learning and Planning](#combining-learning-and-planning))  
- **Recycling the constructed tree** at the next search can be beneficial from the computational point of view and make MCTS methods very efficient.

| ![For each time step, the policy tree is expanded down and the first action (`a1` or `a2`) of the most promising path is selected. The belief, represented by a set of particles, is updated at each transition. Source: (Hubmann et al. 2019)](media/pics/hubmann_contruction_belief_tree.PNG "For each time step, the policy tree is expanded down and the first action (`a1` or `a2`) of the most promising path is selected. The belief, represented by a set of particles, is updated at each transition. Source: (Hubmann et al. 2019)")  |
|:--:|
| *For each time step, the policy tree is expanded down and the first action, `a1` or `a2`, of the most promising path is selected. The belief, represented by a set of particles, is updated at each transition. Source: (Hubmann et al. 2019)* |

(Schörner et al. 2019) and (Hubmann et al. 2019) are using [**TAPIR**](http://robotics.itee.uq.edu.au/~hannakur/dokuwiki/doku.php?id=wiki:tapir), which is a software toolkit implementation of the online solver Adaptive Belief Tree (ABT).
(Pusse and Klusch 2019) is extended the online solver [**DESPOT**](https://github.com/AdaCompNUS/despot) with **Importance Sampling** to enable outcomes that have a low probability of occurring, e.g. crashes to be sample. It reminds me the [Prioritized Experience Replay](https://arxiv.org/abs/1511.05952) used to replay important transitions more frequently in DQN.

## Reinforcement Learning

This section is structured as followed:

- Difference Learning / Planning
- Pure RL for decision making in AD is hard
- Using RL for parameter tuning
- Combining Learning and Planning

Interesting facts

- (Folkers, Rick, and Christof 2019) received the **Best Student Paper Award** for _“Controlling an Autonomous Vehicle with Deep Reinforcement Learning”_.
- The term _reinforcement learning_ appeared in `41` of the released papers (`12`%). Among them, `11` were **presenting an implementation** while the others where referencing RL-related works in the literature review.
- I did not see anything about **_continuous learning_**, i.e. improvement of the RL approach when exposed to additional real-world experience. Yet this is usually presented as one advantage of RL techniques.
- No model-based RL? All IV19 RL-based approaches were **model-free**. During the [2019 ICML AD workshop](https://sites.google.com/view/icml2019aiad/talk-abstract-and-speaker-bio), taking place at the same time, Sergey Levine discussed the potential of **model-based RL for AD**. An affair to follow ...
- **Value-based approaches** were dominant. I counted:

| Count | Method                   |
| :---: |:------------------------:|
| 7     | Q-learning-based methods |
| 2     | PPO                      |
| 1     | DDPG                     |
| 1     | NavA3C                   |
| 0     | Model-based RL           |

### Difference Learning / Planning

The AD decision or AD control can be **formulated as a Markov Decision Process** (MDP), or its extension POMDP with partial observation. There are **two types of methods to solve** it, i.e. to find the optimal policy, depending if the transition and reward models are known or not:

- If the transition and reward models are known, the solving task is referred to as planning.
  - Planning is attractive since it **does not require any interaction with the MDP environment**. But the **design of a realistic transition model** is challenging. Given some acceleration and steering command, deriving the position of the ego vehicle at the next step is feasible. But defining the responses of other driver to these decisions is way more difficult.
- If not, it is called (reinforcement) learning.
  - To collect experience for learning, RL requires an MDP environment to interact with. The environment can be a **simulator** (most approaches) or the **real-world**. [WAYVE](https://wayve.ai/blog/learning-to-drive-in-a-day-with-reinforcement-learning) has demonstrated impressive performance with the later technique. But the problem of **_sample efficiency_**, i.e. the amount of experience the agent needs to generate in an environment during training to reach a certain level of performance, advocate for the **use of a simulator**. This is especially true for **model-free approaches** which need a lot of experience data to derive a good policy. Not to mention the **safety considerations**.

I think it is worth to have this distinction in mind when analysing the different presented algorithms.

### Pure RL for decision making in AD is hard

Although there has been some successful decision making approaches using RL, **model-free techniques**, especially when trained directly on images, are **complex**.

Earlier this year, (J. Chen, Yuan, and Tomizuka 2019) tested three state-of-the-art model-free deep RL algorithms: _DDQN_, _TD3_ and _SAC_. The task was to **navigate through a round-about** and take the third exit. The three approaches where tested `50` times. As shown on their results, **DDQN and TD3 could not reach the goal point on single time**.

| ![Results of model-free RL for a specific task: roundabout handling. Source: (J. Chen, Yuan, and Tomizuka 2019).](media/pics/chen_rl_result.PNG "Results of model-free RL for a specific task: roundabout handling. Source: (J. Chen, Yuan, and Tomizuka 2019).")  |
|:--:|
| *Results of model-free RL for a specific task: roundabout handling. Source: (J. Chen, Yuan, and Tomizuka 2019).* |

Even if RL is hard to implement, it could offer a **reasoning longer horizon** and the **ability to solve task that are too complex** for classical rule-based methods.
(Bouton et al. 2019a) finds that using a **pure RL technique does not enable the agent to act more safely** than when using simple **rule-based methods**. But when the **scenario becomes more complex**, with a flow of cars and pedestrians, the benefit of using RL over a rule-based method become clear.

Among successful approaches, only few report **evaluations on real vehicles**. (Folkers, Rick, and Christof 2019) conducted test on a full-size research vehicle during and show the potential of their RL-based controller for autonomous exploration of a parking lot. It would have been nice to have demonstration form them or from the RL research teams as [WAYVE](https://wayve.ai/blog) during the track-demo day!

### Using RL for parameter tuning

Using RL for decision making is not the only option.

(Ure et al. 2019) used an RL-based approach to **automate the tuning of MPC parameters** for an Adaptive Cruise Control (ACC) system. The DQN net chooses among three sets of parameters: _distance weight_, _acceleration command weight_ and _velocity weight_. These weights are then used in the constrained optimization of the MPC to yield the acceleration command.

This idea of **using RL for automated weight tuning** is also present in (L. Chen et al. 2019). **Proximal Policy Optimization** (PPO) is used to tune the weights of a Pure-Pursuit Proportional Integral Derivative (**PP-PID**) controller.

### Combining Learning and Planning

Earlier that year, (Hoel et al. 2019) presented a tactical decision making that **combines the concepts of planning with MCTS and learning with RL**. It forms a **win-win** situation. On the one hand, the **neural network can bias the sampling** towards the most relevant parts of the search tree. As I understood, the **learnt value function serves as heuristic**. On the other hand, the MCTS improves the **sampling efficiency** of the RL training process. It especially helps it for the _credit assignment_ problem. This combination has been inspired from the success of **AlphaGo Zero**. It was extended to cope with a **continuous state space** (using progressive widening) and to deal with **partial observability**.

| ![A RL-based learnt value function is used to expand the MCTS belief tree when solving the POMDP. Source: (Pusse and Klusch 2019).](media/pics/pusse_tree.PNG "A RL-based learnt value function is used to expand the MCTS belief tree when solving the POMDP. Source: (Pusse and Klusch 2019).")  |
|:--:|
| *A RL-based learnt value function is used to expand the MCTS belief tree when solving the POMDP. Source: (Pusse and Klusch 2019).* |

At IV19, (Pusse and Klusch 2019) introduced a similar approached, named **_HYLEAP_**. The network acts as an **experience-based critic**, estimating the **value function** which is used to **guide the belief tree construction**.

I really like this idea of **combining planning and learning** and expect other examples to follow. Maybe with a car experiment as well...

## Learning-based versus Non-Learning-based Approaches

While machine learning is massively used for **perception**, its impact on **decision** and **control** has been limited due to reservations about **interpretability and reliability**.

Non-learning-based systems are mostly interpretable and seem to work on non-complex scenarios. But:

- **Rule design is so painful!**
- They can impose **strong modelling assumptions** (DBN).
- They fail in **complex scenarios** (IDM).
- Contrary to some RL approaches, they do not offer **continuous improvement**.

On the other hand, learning-based approaches have shown promising results. But:

- They are mainly **non-explanatory** and **non-interpretable**.
- They require some **data** to train (annotated in the case of supervised learning) or some **environment** to interact with (reinforcement learning).
- They can **be hard to adjust manually**. For instance, **fixing MPC errors on the fly** easier than fixing a model-free RL, which requires **tuning the reward function** and then restarting the training.

My main take-away is that, as for the reinforcement learning section, a **hybrid framework that combines both learning-based and non-learning-based methods** seems to be the way to go.

The following is structure as follows:

- Benefits of the Learning / Non-Learning Combination
- Learning-based Methods Now used in Prediction
- Interpretability is not an Option
- In Validation Process

### Benefits of the Learning / Non-Learning Combination

This **need for such combination** is illustrated in (Schulz et al. 2019). A probabilistic mapping from context and route intention to action distribution is learnt with deep neural networks. The benchmark include a non-learnt approach. They show that for **short-term predictions** of up to around `2` seconds, all the **learning-based models outperform the rule-based action model**. But the rule-based model becomes **more accurate for predictions with very long horizons**, exceeding `15s`.

The reason is that the rule-based system is designed to be **attracted by the centre of the lane**. Hence the vehicle will always stay close to the centreline. On the opposite, **prediction errors can accumulate** for the learning-based models by **recursively making one-step ahead prediction**. And once the cumulative error gets too high, the **features determined during forward simulation** are **not represented within the training data** anymore.

It reminds me the issue of _drift_ in imitation learning, when error accumulates fast in a trajectory, and this **brings the agent into situations that it never dealt with before** (_cascade errors_). A common solution it then to use _Dataset Aggregation_ methods like DAgger. Or in this case maybe to **combine the rule-based with the learning-based model**.

I have listed other example of successful combinations in the RL sections. The benefits can relate to the **generalization capability**, the **interpretability gain** or the **safety**. Here is another example.

(Schratter et al. 2019a) combine a POMDP planner with an Autonomous Emergency Braking (AEB) system. In scenarios with occlusion, the **POMDP planner enables the system to anticipate this uncertainty** and to prevent an emergency stop. In **parallel**, the AEB system runs at a **higher frequency and intervenes** with a strong brake intervention **when a collision is unavoidable**. The resulting combination can pass obstacles faster than a sole POMDP, while avoiding all collisions.

| ![For each pedestrian, a probability of collision is computed from the time-to-brake TTB and the predicted position of the pedestrian. The difference between time to collision and time needed to stop is used to decide an emergency braking. Source: (Schratter et al. 2019).](media/pics/schratter_risk_assessement.PNG "For each pedestrian, a probability of collision is computed from the time-to-brake TTB and the predicted position of the pedestrian. The difference between time to collision and time needed to stop is used to decide an emergency braking. Source: (Schratter et al. 2019).")  |
|:--:|
| *For each pedestrian, a probability of collision is computed from the time-to-brake TTB and the predicted position of the pedestrian. The difference between time to collision and time needed to stop is used to decide an emergency braking. Source: (Schratter et al. 2019).* |

### Learning-based methods now used in prediction

Other learning-based approaches to **replicate the behaviour of a non-learning-based methods** such as probabilistic or  kinematics-based approaches. And they show promising results, especially in **high complex interactions**.

For instance, (Joonatan and Folkesson 2019) use Conditional Variational Auto Encoders (CVAEs) to show that deep-learning approaches can replicate and **outperform classic probabilistic behaviour predictors** like DBNs. The idea is to learn a **distribution as a latent representation** which can then be **sampled** to generate possible future trajectories. In this case, the unit Gaussian prior is sampled as the latent representation. One advantage is that the predicted manoeuvres can be **approximated by sampling** without the need for computationally expensive purely Bayesian approaches.

| ![CVAE network used for prediction. At inference time, the unit Gaussian prior is sampled as the latent representation and concatenated with the conditioned input to produce likely outcomes of the input scenario. Source: (Joonatan and Folkesson 2019).](media/pics/joonatan_CVAE.png "CVAE network used for prediction. At inference time, the unit Gaussian prior is sampled as the latent representation and concatenated with the conditioned input to produce likely outcomes of the input scenario. Source: (Joonatan and Folkesson 2019).")  |
|:--:|
| *CVAE network used for prediction. At inference time, the unit Gaussian prior is sampled as the latent representation and concatenated with the conditioned input to produce likely outcomes of the input scenario. Source: (Joonatan and Folkesson 2019).* |

(Y. Li et al. 2019) propose a review of the **learning-based approaches for pedestrian trajectory prediction**.
In their conclusion, the authors suggest investigating the idea of a **unified framework that combines _Seq2Seq_ models** (learning-based) with **probabilistic graphical models**. Again, a fusion seems beneficial.

### Interpretability is not an option

In their prediction framework for driver’s manoeuvre intention, (Rehder et al. 2019) note that **Bayesian network** (BN) is a white box model, and that contrary to neural networks, model’s parameters are **human interpretable** and **manually adjustable** as needed.

It reminded me the work of (Koenig, Rehder, and Hohmann 2017), who **preferred to use BNs due to the high safety demands in AD**, even though they were **outperformed by DNN in some of their experiments**.

(Akai et al. 2019) also recognize that deep learning-based modelling has become popular, but they **prefer to focus on more explanatory modelling methods**. For their work on driving behaviour, they use an autoregressive input-output HMM (**AIOHMM**). However, they note that the descriptive and extraction abilities of the HMM-based methods for complex nonlinear relations are **not higher than that of the deep learning-based methods**. In their conclusion, the authors mention the benefits that a combination could have.

| ![The vanilla HMM (a) is extended to handle past and input information for driving behaviour modelling. Input variables are represented by x, hidden states by z while y denotes the observable variables. Source: (Akai et al. 2019).](media/pics/akai_hmm_types.png "The vanilla HMM (a) is extended to handle past and input information for driving behaviour modelling. Input variables are represented by x, hidden states by z while y denotes the observable variables. Source: (Akai et al. 2019).")  |
|:--:|
| *The vanilla HMM (a) is extended to handle past and input information for driving behaviour modelling. Input variables are represented by x, hidden states by z while y denotes the observable variables. Source: (Akai et al. 2019).* |

Several methods are proposed to **increase or regain interpretability** of learning-based approaches.

After training a RL agent with PPO, (Folkers, Rick, and Christof 2019) compute an **attention map of the actor-critic network** to evaluate **what parts of it are most important**. It can be seen that high attention is paid to the agent’s **immediate surrounding** as well as to important points in the long term such as the **corner**, where it must turn around, or the surrounding of the target.

| ![Attention map. Source (Folkers, Rick, and Christof 2019).](media/pics/folkers_attention.PNG "Attention map. Source (Folkers, Rick, and Christof 2019).")  |
|:--:|
| *Attention map. Source (Folkers, Rick, and Christof 2019).* |

(Hu, Zhan, and Tomizuka 2019) demonstrate how to extract interpretability from their **prediction-based CVAE**. Conditional VAE is a **latent variable generative model** that can learn a factored, low-dimensional representation of an input scene. When plotting the **learnt latent space**, the two motion patterns (`pass`/`yield`) could be somehow distinguished. Moreover, they show that the generating trajectories can be explained by the **location of generative latent variable**. For instance, they fix one component of the latent space while decreasing the second one. This causes the second car to increase its speed and shifts from yielding the first car to passing it.

### In Validation Process

While we do not want to have a **GAIL-based agent generating the policy** of the autonomous vehicle, it can serve for **simulation and validation purposes**. Especially for the policy of other traffic participants in simulation. It does **not need to be 100% safe** or interpretable. And it can even find bugs in the hand designed engineering system. The learning can be done **relatively fast** using naturalistic datasets to **model the most likely behaviour**. And it does not require much parameter tuning. Moreover, obtaining a mean behaviour does not need much data.

But in validation, it is important to also build model to estimate **low-probability events** that can lead to failures. And this is where **expert knowledge** (non-learnt) is extremely useful.

## Uncertainty

> “It is far better to foresee even without certainty than not to foresee at all” - Henri Poincaré

I have already mentioned that belief state planning like POMDP has the potential to significantly improve robustness of AD. They give the ability to reason about the future given the uncertain information about latent states and the future behaviours.

In this section, I would like to examine:

- The sources of uncertainty.
- The use of probabilistic distributions in action selection and prediction.
- The case of occlusion.
- Some techniques to reduce uncertainty in AD.

### Sources of uncertainty

Christoph Stiller, one of the fathers of KITTI datasets, starting his keynotes by listing the sources of uncertainty**. He grouped them into four categories.

- Uncertainty due to **noise** in perception.
- Uncertainty due to **occlusion** and **limited sensor range**.
- Uncertainty in **maps** and localisation.
- Uncertainty in the **intent** of other traffic participants. More generally this encompass all the **social behavioural uncertainties**, such as local driving preferences.

| ![Keynote by Christoph Stiller. Source: author provided.](media/pics/stiller_keynote.jpg "Keynote by Christoph Stiller. Source: author provided.")  |
|:--:|
| *Keynote by Christoph Stiller. Source: author provided.* |

To deal with occlusions, Christoph Stiller mentioned the benefits of reasoning in terms of reachable sets, referencing the work of his team (Orzechowski, Meyer, and Lauer 2018).

He then spent a long time discussing about uncertainties in **maps** and **localisation**. Maps contain information used for **tactical long-horizon decision making** such as the possible actions that one can take at that crossing or simply the speed limitation. But if maps **do not get updated** as the environment changes, it can be **very misleading**.

This situation made me think of a quote about software engineering:

> No documentation is better than a deprecated documentation.

On a highway **construction site**, for instance, lanes could have been **laterally shifted by two meters**. The relative position between lane markers on the road may remain valid, but their positions will not match anymore with signs and poles. This could cause a **large trouble** in the **visual-based localization**.

To address the question of **trust in map**, especially in building zones, Christoph Stiller mentioned a [dataset](https://www.mrt.kit.edu/software/mapvalidation/) for map verification. Recordings have been performed on roads before and during a construction work.

One conclusion is that the AD vehicle should ideally be able to **get rid of map if it is causing difficulties**.

### Heteroscedastic Uncertainties

Another commonly-used classification of uncertainty distinguishes two categories.

- Aleatoric Uncertainties. There are of statistical origin and apply especially for sensors. The noise inherent to measurement is often modelled by introducing probability densities, such as Gaussian distributions in the case of Kalman filters for instance.
- Epistemic Uncertainties. They describe missing or incomplete information such as occluded areas. In this case hypothetical objects are considered.

> I learnt a new term: “Heteroscedastic Aleatoric Uncertainties”!

| ![The variance in the actions can vary. In the first half section (`t` `∈` [`0`; `100s`]), the estimate is highly confident (zero variance) since the vehicle is driving on a straight road. When approaching the intersection, the estimation shows more uncertainty. Source: (Schulz et al. 2019).](media/pics/schulz_heteroscedastic.PNG "The variance in the actions can vary. In the first half section (`t` `∈` [`0`; `100s`]), the estimate is highly confident (zero variance) since the vehicle is driving on a straight road. When approaching the intersection, the estimation shows more uncertainty. Source: (Schulz et al. 2019).")  |
|:--:|
| *The variance in the actions can vary. In the first half section (`t` `∈` [`0`; `100s`]), the estimate is highly confident (zero variance) since the vehicle is driving on a straight road. When approaching the intersection, the estimation shows more uncertainty. Source: (Schulz et al. 2019).* |

(Schulz et al. 2019) learn a **probabilistic mapping** from context and route intention to **some action distribution** (acceleration and steering). Since the **variance is not constant** (contrary to *homoscedastic* regressions), this mapping is said **"heteroscedastic"**. It can express the **varying uncertainty in the choice of action** depending on the situation. The negative *log likelihood* is used as a loss function to learn both the mean and variance in one training procedure.

(Pool, Kooij, and Gavrila 2019) and (Zernetsch et al. 2019) also include uncertainty in prediction by outputting a Gaussian distribution over the future position.

| ![Incorporating uncertainties in trajectory prediction. Source: (Zernetsch et al. 2019).](media/pics/zernetsch_uncertainty_in_prediction.PNG "Incorporating uncertainties in trajectory prediction. Source: (Zernetsch et al. 2019).")  |
|:--:|
| *Incorporating uncertainties in trajectory prediction. Source: (Zernetsch et al. 2019).* |

But modelling a future trajectory with a Gaussian could be problematic. Especially when considering multiple possible routes at an intersection. Instead of **predicting positions with ellipses**, (Hu, Zhan, and Tomizuka 2019) assign **intention probability** to some **reference paths**. This is known as *multi-modality prediction*.

| ![Example of multi-modality in trajectory prediction. Source: (Hu, Zhan, and Tomizuka 2019).](media/pics/hu_multimodal.PNG "Example of multi-modality in trajectory prediction. Source: (Hu, Zhan, and Tomizuka 2019).")  |
|:--:|
| *Example of multi-modality in trajectory prediction. Source: (Hu, Zhan, and Tomizuka 2019).* |

Uncertainty quantification can also be applied to **action selection and control**. The policy learnt in (Folkers, Rick, and Christof 2019) does not return a single action value but rather a pair (`µ`, `σ`), i.e. the **mean and standard deviation of a gaussian probability distribution** for each possible action.

The **distributional perspective** is also applied by (Bernhard and Knoll 2019) where a value-based **Distributional RL** method is used. In common *value-based* MDP and POMDP solvers (e.g. *classical DQN*, *MCTS*, *Adaptive Belief Tree*), the cumulated discounted reward is **expressed as an expectation**. The term *Distributional* in their approach means that learnt value function is not a single numeric value. But rather a **distribution**.

This concept reminds me the DeepMind post blog [*Going beyond average for reinforcement learning*](https://deepmind.com/blog/going-beyond-average-reinforcement-learning/) introducing [C51](https://arxiv.org/abs/1707.06887).

In (Bernhard and Knoll 2019), the value-distribution function is learnt offline and can be then used to select risk-sensitive actions. The selection differs from the classical RL approaches where the action with the **highest expectation** is chosen.

My interpretation is as follows. For safety reasons, we are interested in the **lower tail of the return distribution**. The action selection could be based on the **inverse CDF** at a certain percentage (e.g. `10%`). But as shown on the figure below, this percentage, representing a **risk measure**, differs between actions. It is around `50%` for the red distribution, and maybe less than `20%` for the other two. I must admit I did not understand all the details on how these risk metrics are computed. Anyway, the **action with the highest bound is selected** (*high. accel.* in the following figure).

| ![Working with distributions instead of mean expectation enable to improve uncertainty-aware action selection. Source: (Bernhard and Knoll 2019).](media/pics/bernhard_distribution.PNG "Working with distributions instead of mean expectation enable to improve uncertainty-aware action selection. Source: (Bernhard and Knoll 2019).")  |
|:--:|
| *Working with distributions instead of mean expectation enable to improve uncertainty-aware action selection. Source: (Bernhard and Knoll 2019).* |

Do you know how to **measure the difference between two probability distributions**? I knew the [KL-divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence). Reading the paper, I discovered an alternative: the [Wasserstein metric](https://en.wikipedia.org/wiki/Wasserstein_metric), (a proper “distance” this time) used in the training phase.

While most of these methods for perception and decision-making **output an uncertainty measure**, they treat the information from localisation and perception as **point estimates** (with zero variance). Nevertheless, the **propagation of uncertainty measurements through all the AD modules** is essential.

The **environment representation** based on **probabilistic occupancy grids** used in (Barbier et al. 2019) seems appropriate to capture the perception uncertainty.

### Unfreezing the Robot under Occlusion

A common approach to deal with uncertainty is the **worst-case assumption**. But in highly complex scenarios, this can lead to the issue of *"Freezing Robot"*: uncertainty in future states explodes and the AD vehicle **does not find any valid action**. It stands still.

The POMDP formulation can address this issue. Being a **belief state planning** problem, it allows for **decision making under uncertainty**. (Schörner et al. 2019) use a POMDP to tackle uncertainty in occlusion. Similarly, (Hubmann et al. 2019) presents an **occlusion-aware POMDP** (OCAPOP) showing that:

> "Postponing the decision is not always a bad idea".

The idea is to simulate the potential **field of view** (*fov*) over the complete scenario and consider at the same time the **reachable set** for the vehicles that may be occluded. The reward function motivates an **increase in the _fov_** while **punishing lateral offsets**, which may be **conflicting** e.g. when following a tall vehicle.

The vehicle sometimes decides to **postpone the decision** about crossing the intersection and prefers increasing its _fov_ by **taking a lateral offset to capture more information**. This behaviour, called **"information gathering"**, allows for less conservative behaviour.
I think one could draw a parallel with the concept of *exploration* / *exploitation* used in RL. But here, this decision is taking when driving. Not during the training phase.

The resulting planner can drive nearly the same trajectories as an **omniscient planner would**.

| ![The AD car wants to turn left. But the other vehicle is occluded by a building (black line with red point). The AD car keeps driving to increase its field of view. It contemplates two trajectories: a _direct left-turn_ (thick blue polyline) and a _backup stopping trajectory_ (thin blue polyline going down). As soon as a real vehicle pops out and is seen, the AD car executes the braking manoeuvre. Source: author provided – recorded during the SIPD workshop.](media/gif/hubmann_occlusion.gif "The AD car wants to turn left. But the other vehicle is occluded by a building (black line with red point). The AD car keeps driving to increase its field of view. It contemplates two trajectories: a _direct left-turn_ (thick blue polyline) and a _backup stopping trajectory_ (thin blue polyline going down). As soon as a real vehicle pops out and is seen, the AD car executes the braking manoeuvre. Source: author provided – recorded during the SIPD workshop.")  |
|:--:|
| *The AD car wants to turn left. But the other vehicle is occluded by a building (black line with red point). The AD car keeps driving to increase its field of view. It contemplates two trajectories: a _direct left-turn_ (thick blue polyline) and a _backup stopping trajectory_ (thin blue polyline going down). As soon as a real vehicle pops out and is seen, the AD car executes the braking manoeuvre. Source: author provided – recorded during the SIPD workshop.* |

### Reducing Uncertainty

A first approach to reduce uncertainty consists in **direct communication** between traffic participants.

During his keynote, Christoph Stiller called for more research in **_“Cooperative perception, behaviour and planning”_**. He sees a huge potential to improve safety, reduce emission and increase the traffic flow efficiency.

Another approached, not based on V2V communication, was presented during the SIPD workshop. I really enjoyed this work on behavioural planning presented by (Sun et al. 2019). It introduces the concept of **"Social Perception"**. The idea is to **infer some information** about the environment by **observing the behaviour of other traffic participants**. Two quotes from them:

> “Human drivers on the road are intelligent agents whose behaviours can not only tell us what they want to do, but also what might have been missed by our own sensors.”

> “Human participants should be treated not only as dynamic obstacles, but also as distributed sensors that provide via their behaviours additional information about the environment beyond the scope of physical sensors”.

They show how it can be used to reduce uncertainty in the case of **occlusions** or **limited sensor range**.

| ![From the behaviour of the other driver, the ego car can infer and become more confident about the probability of a pedestrian crossing. Source: (Sun et al. 2019).](media/pics/sun_social_perception.PNG "From the behaviour of the other driver, the ego car can infer and become more confident about the probability of a pedestrian crossing. Source: (Sun et al. 2019).")  |
|:--:|
| *From the behaviour of the other driver, the ego car can infer and become more confident about the probability of a pedestrian crossing. Source: (Sun et al. 2019).* |

## Do not Forget Safety

> “10 years ago, safety was often said to be for serious production, not for research. Now, it has become a research area.”

> "Academics love to be distracted by a future in which self-driving vehicles make life-or-death decisions while moving at high speed. Whether the robot trolley will crash into the businessman or the older woman is the question of the day." [Source](https://www.nature.com/articles/d41586-019-01473-3)

This topic was new to me. I have heard of:

- Safety Validation for AD
- RSS
- Model Checkers
- Reachability sets

### Safety Proof for AD

One big question was: _**"How to prove safety in AD?"**_

This question was addressed by Jack Weast, Vice President at Mobileye, in his keynote “An Open, Transparent, Industry-driven Approach to AV Safety”.

| ![Keynote by Jack Weast. Source: author provided.](media/pics/weast_keynote.jpg "Keynote by Jack Weast. Source: author provided.")  |
|:--:|
| *Keynote by Jack Weast. Source: author provided.* |

What do you want to mean by *safety*? Let us start by the definition _"AV is safe if it_ **doesn’t experience collisions**_"_.

**A first intuition could be to use statistics**. For instance, with an **expectation over the distance between two accidents**. That could be computed using the total number of accidents and the total distance travelled. But only considering the statistical argument is not a good metric, since **it does not capture the scenario complexity**. One kilometre driven in Paris, on narrow streets, with all these crazy electric scooters and reckless pedestrians cannot be fairly compared with drives on large and straight highways. And the Paris example seems easy compared to some situations in India. Not to mention the weather and day/night conditions.

A second intuition could be to just **make the vehicle follow the traffic rules and prove it always abides to them**. But following the rules is not enough to avoid a crash. Moreover, it can be sometimes **socially accepted and safer to break a traffic rule**. For instance, when crossing a solid line to takeover a badly-parked vehicle.

What about **testing the vehicle on many critical scenarios**? Potentially using simulation. Thousands of thousands of scenarios can be generated and tested. But **the world is complex, and humans are imaginative**. There will always be yet another critical scenario that has not been considered before.

Another idea could be to **try to avoid crashes** at all cost. And to (try to) prove it to the authorities. But this is not realistic. Imagine you are surrounded by cars that are coming together close to you. **Avoiding accidents is just impossible**.

| ![Mobileye RSS and Nvidia SFF agree: an absolute safety is impossible as long as AVs share the road with human drivers. [Source](https://newsroom.intel.com/wp-content/uploads/sites/11/2019/03/Intel-SFFvsRSS-table.pdf).](media/pics/rss_absolute_safety_impossible.PNG "Mobileye RSS and Nvidia SFF agree: an absolute safety is impossible as long as AVs share the road with human drivers. [Source](https://newsroom.intel.com/wp-content/uploads/sites/11/2019/03/Intel-SFFvsRSS-table.pdf).")  |
|:--:|
| *Mobileye RSS and Nvidia SFF agree: an absolute safety is impossible as long as AVs share the road with human drivers. [Source](https://newsroom.intel.com/wp-content/uploads/sites/11/2019/03/Intel-SFFvsRSS-table.pdf).* |

> Another definition is hence suggested: _"AV is safe if it_ **doesn’t cause collisions**_"_.

Instead of defining scenarios in advance or trying to avoid all collisions, Jack Weast suggested to **put constraints on the driving policy**, i.e. the decision making system, which leads to the introduction of **RSS**.

### RSS

Intel, as the Platinum sponsor, could advertise the mathematical model for AV safety named [Responsibility-Sensitive Safety](https://www.mobileye.com/responsibility-sensitive-safety/) (RSS) developed by its subsidiary Mobileye. The main idea of RSS is **to mimic human judgement using math formulation**. It acts like a **safety checker on the proposed actions** and can let them proceed on the actuators or not.

While RSS starts to become popular among the AD community, it seems it has already [inspired Nvidia](https://newsroom.intel.com/editorials/innovation-requires-originality/#gs.kiu5nl). 😉

[![IMAGE ALT TEXT](http://img.youtube.com/vi/EceAB6TUYzo/0.jpg)](http://www.youtube.com/watch?v=EceAB6TUYzo "Introduction to RSS")

First, RSS is based on the **notion of "blame"**: *ensure that the AV does not initiate any dangerous situation*.

This concept is implemented in (Naumann et al. 2019) for motion planning under occlusions, where two safety conditions are defined.

- (C1) Being in a **safe state before** the conflict zone.
- (C2) **Safely passing** the conflict zone.

Only trajectories that fulfil both can be selected, as stated by their authors:

> If we cannot ensure a safe trajectory through a conflict zone, we must ensure the reachability of a safe state before it.

I will remember a second idea of RSS: the model of **human implicit rules**.

Humans follow two kind of rules:

- **Explicit rules**. Such as *“should stop at a red light”*.
- **Implicit rules**. For instance, the US law says that you should drive at a *speed that is appropriate to the traffic condition*.

RSS model human implicit rules with **five principles**.

| ![The five RSS principles meant to formularize human common sense. As opposed to explicit rules, they are subjective and open to interpretation. Source: author provided.](media/pics/rss_principles.jpg "The five RSS principles meant to formularize human common sense. As opposed to explicit rules, they are subjective and open to interpretation. Source: author provided.")  |
|:--:|
| *The five RSS principles meant to formularize human common sense. As opposed to explicit rules, they are subjective and open to interpretation. Source: author provided.* |

In addition to *concept of blame* and the *five human implicit rules*, I noted that **the RSS checker is based on parametrized models**. This can be shown by the presence of equations on the above picture (sorry - it is hard to see). This parametrization offers opportuinities and challenges.

- **Driving is cultural**. Regulators and governments can **plug in different numbers**, based on what they want. RSS can be **used as a metric**.
- But **estimating the RSS parameters can be complicated**. How would you estimate the **maximum braking capability** of the vehicle in front of view? The same as yours? The average on the market? What if it is a sport car with higher braking capabilities? This value, usually denoted by `β max`, plays a significant role in the model since it conditions the safe gap distance.

| ![Presentation of AD-RSS-LIB. Source: author provided.](media/pics/weast_rss_lib.jpg "Presentation of AD-RSS-LIB. Source: author provided.")  |
|:--:|
| *Presentation of AD-RSS-LIB. Source: author provided.* |

Finally, I have retained two main announcements from RSS:

- First, RSS will use the **KITTI dataset to infer RSS parameters** that best fit to the recorded human drivers. It made me think of Inverse RL approaches which aim at finding the reward function of an MDP, given some optimal demonstration. This will be particularly useful to offer a **more realistic** (yet German specific) model. But the question of `β max` will still remain.
- IV19 was the opportunity for Jack Weast to mention the release of a [library](https://intel.github.io/ad-rss-lib/) that provides a C++ implementation of RSS. It comes with support for integration with Baidu Apollo and CARLA.

| ![First, the perceived information needs to be extracted according to the RSS world model. The RSS module provides then actuator command restrictions as output to enforce safe behaviour. The checker can be placed at several location: within the behaviour planner, around the planning module or outside the AD (CARLA example).](media/pics/rss_lib.jpg "First, the perceived information needs to be extracted according to the RSS world model. The RSS module provides then actuator command restrictions as output to enforce safe behaviour. The checker can be placed at several location: within the behaviour planner, around the planning module or outside the AD (CARLA example).")  |
|:--:|
| *First, the perceived information needs to be extracted according to the RSS world model. The RSS module provides then actuator command restrictions as output to enforce safe behaviour. The checker can be placed at several location: within the behaviour planner, around the planning module or outside the AD (CARLA example).* |

### Reachability analysis

During the three days of workshop and conference, I heard a lot about **reachability analysis** and **reachability sets**. These concepts were new to me.

During the [SIPD workshop](https://sites.google.com/berkeley.edu/iv19-interaction), Katherine Driggs-Campbell showed how to make **set-based prediction**. She distinguished between **two approaches** to modelling human behaviour: *informative* and *robust* methods. Using models learnt from a dataset, their method outputs so-called *empirical reachable sets*. The idea is to **reject unlikely samples up to some probability threshold**, in order to balance between *robustness* and *informativeness*.

| ![Difference between **Informative** and **Robust** approaches for human behaviour modelling. Source : (Driggs-Campbell et al. 2017).](media/pics/driggs_informative_vs_robust.PNG "Difference between **Informative** and **Robust** approaches for human behaviour modelling. Source : (Driggs-Campbell et al. 2017).")  |
|:--:|
| _Difference between **Informative** and **Robust** approaches for human behaviour modelling. Source : (Driggs-Campbell et al. 2017)._ |

That reminds me the [concept](https://www.youtube.com/watch?v=o9A4e7zopu8) of **precision** (*type I error* leading to *false positives*) and **recall** (*type II error* leads to *false negatives*) used in **object detection**.

When asked about future trajectory, one could draw a **very large set that is guaranteed to encapsulate all the possible trajectories**. This will present a **high recall** but will probably be **over-conservative** and not applicable for high speeds. Another option could be to **restrict the prediction to one single trajectory**, potentially with some uncertainty. This second method will offer a **high precision** but will be meet difficulties to guarantee safety.

Besides prediction, **reachable sets can be used in motion planning** and collision avoidance, as demonstrated in (Hartmann and Watzenig 2019). The **maximal jerk** in the motion of pedestrians is predicted iteratively using Gaussian processes. From these **physical constraints**, a **reachable set on positions** can be formulated and incorporated in the motion planning. It is finally solved with Mixed Integer Linear Programming (MILP).

| ![Using reachability analysis for motion planning. Source: (Hartmann and Watzenig 2019)](media/pics/hartmann_sets_for_planning.PNG "Using reachability analysis for motion planning. Source: (Hartmann and Watzenig 2019)")  |
|:--:|
| *Using reachability analysis for motion planning. Source: (Hartmann and Watzenig 2019)* |

Another example I liked was presented by (Pierson et al. 2019), where another type of set, *risk level sets*, is used to safely navigate **congested environments**. The authors define a cost function and tune its parameters to fit the human data, based on the NGSIM and highD. Thresholds corresponding to low, medium, and high-risk driving are also learnt from these highway recordings.
These thresholds can be used for **planning with different supported risk**. The vehicle will seek a **path in the region when the estimated risk is below a certain threshold**. For instance, a low-risk agent will have a more restricted planning space. As a result, it will make fewer lane changes along the congested highway.

| ![Planning can be based on Congestion quantification and risk assessment for the ego vehicle using risk level sets. Here a high-risk threshold is applied. Source: (Pierson et al. 2019)](media/gif/pierson_risk_sets.gif "Planning can be based on Congestion quantification and risk assessment for the ego vehicle using risk level sets. Here a high-risk threshold is applied. Source: (Pierson et al. 2019)")  |
|:--:|
| *Planning can be based on Congestion quantification and risk assessment for the ego vehicle using risk level sets. Here a high-risk threshold is applied. Source: (Pierson et al. 2019)* |

This **risk-based decision making** was for me a paradigm shift. In many works, **criticality of a situation** is estimated using metrics like **time to collision** (TTC). But **evaluating the future situation risk** is probably **more human-like** than TTC-based collision avoidance.

> "Human response to traffic situations depends on their risk assessment and tolerance to risk." Source: (Pierson et al. 2019).

My takeover from all these sets is that active safety and driver modelling are tightly linked. Starting with concerns on safety guarantees, the reachability analysis naturally leads to the definition of solutions for planning and predictions.

I would like to end this section on safety by mentioning other techniques to embed risk assessment in decision modules.

### Risk Assessment and Safety Checkers

Besides RSS, other techniques for safety checker were presented.

(Barbier et al. 2019) proposed a method to validate perception and decision-making systems via *Statistical Model Checking*. The idea is to compute the **probability for a system to stay in a certain range of KPIs**, for instance for the acceleration to stay bounded.

Another interesting aspect is the use of **Bounded Linear Temporal Logic** (BLTL). Here, the term *bounded* means that the operators can be **parameterized by a time bound**. This method offers a finer formulation for validation. For instance, one can work with the **probability of crossing the intersection in less than a given time**.

Another point I liked was their method for **risk assessment** at road intersections. The idea, built from their previous work (Barbier et al. 2018), is to compute the **difference between what the driver IS DOING** (called *intention*) and **what a driver SHOULD DO** according to priority rules (called *expectation*). Situations where *intention* and *expectation* do not match could result in a risky interaction. The resulting discrepancy can be useful for risk assessment since it can **capture some misinterpretation** of the scene by the ego vehicle, especially in scenarios with occlusion.

LTL was also applied in (Esterle, Aravantinos, and Knoll 2019), not for safety or risk assessment but for traffic rule compliance. They show how LTL can be used to verify high-level manoeuvre plans. As argued, **"this may help shift part of the development from writing code to writing specifications"**. The idea is to construct *semantic traces* that represent possible manoeuvre variants. And then to check whether each trace satisfies the specified traffic rules. **Vienna Convention of Traffic Rules** are used described in the LTL formulas.

| ![Semantic traces are first generated depending on the scenario. Their validity with respect to LTL rules are then checked. Source: (Esterle, Aravantinos, and Knoll 2019).](media/pics/esterle_overview.PNG "Semantic traces are first generated depending on the scenario. Their validity with respect to LTL rules are then checked. Source: (Esterle, Aravantinos, and Knoll 2019).")  |
|:--:|
| *Semantic traces are first generated depending on the scenario. Their validity with respect to LTL rules are then checked. Source: (Esterle, Aravantinos, and Knoll 2019).* |

In their conclusion, the authors point that it should be allowed to **violate some rules in emergency situations**. They suggest investigating the difference between *soft* and *hard* rules to distinguish between *should* and *must*.

In the previous sections, I have been advocating for the POMDP formulation. I was interested to know how safety checkers can be incorporated to MDP and POMDP.

In many previous works about MDP, a deterministic **action masking mechanism** was used. For instance, in the lane changing problem, if the ego car is in the left most lane, then taking a *left* action will result in getting off the road. Therefore, a mask can be put on the *Q-value* associated with the left action to ensure it is never selected in such a state.

At IV19, (Bouton et al. 2019) presented a probabilistic model-checker for POMDP, using RL. The advantage is that the threshold can be adapted depending on the risk acceptance.

As I understood, for a **particular reward function**, the **optimality value (or *utility*) function** of an MDP is the **probability of reaching the goal safely**. This utility function can be learnt offline using value iteration. The values, in this case probabilities, are then used to learn the policy: the RL agent can **chose among the set of actions** that have a **probability of safety larger than a safety threshold**.

Finally, during the SIPD workshop, Mykel Kochenderfer mentioned a [project](https://github.com/sisl/POMDPModelChecking.jl) from his team on verification in POMDP from LTL formulas. It relies on [`POMDPs.jl`](https://github.com/JuliaPOMDP/POMDPs.jl) for expressing the POMDP model and [`Spot.jl`](https://github.com/sisl/Spot.jl) for manipulating LTL formulas.

| ![POMDP Model Checker. Source: author provided.](media/pics/kochenderfer_pomdp_checker.jpg "POMDP Model Checker. Source: author provided.")  |
|:--:|
| *POMDP Model Checker. Source: author provided.* |

As I understood, the [SPOT library](https://spot.lrde.epita.fr/index.html) is used to translate an LTL formula into some automata. This automata is then combined with the POMDP model and generates a larger POMDP that can be solved with modern solvers. The output is the probability that the LTL formula is verified.

Mykel Kochenderfer ended his presentation by mentioning two remaining **challenges about safety checker for decision making**:

- It is still not clear **how to show** that the system that comes out of this **optimization** can be trusted.
- **POMDPs with integrated model checkers** can be huge and works still need to be done to overcome the **computational tractability**.

## Generalization in decision modules

Most authors show the relevance of their approaches on one or few scenarios. Usually with a fix number of traffic participants and a specific/arbitrary road layout. I was wondering how these approaches can **generalize to a different road structure**, in diverse scenarios with **a various number of participants**.

A first challenge concerns the **computation**. Scaling to more than two-agent interactions is challenging due to the **Curse of Dimensionality** (_problem caused by the exponential increase in volume associated with adding extra dimensions to Euclidean space_), as introduced by R. Bellman in 1957.

I found two interesting methods:

- Generic scene representation.
- Scene decomposition methods.

### Generic Scene Representation

Generalization raises the **question of representation**. Vectors of **features** such as *distance to the other vehicle* and *relative velocity to the cyclist* can be used. But the **variation in the number of participants** will change the **size of the joint representation**. And the **non-constant size of the input** may be problematic for the algorithm, such as neural networks.

Instead of describing the state of other participants with a vector and then **combining all the instance in one joint state vector**, **images representing the whole scene** can be used. This is done in (Joonatan and Folkesson 2019), where a generic visual representation is designed. The main advantage of representing the traffic scenario with an image is that number of traffic participants does not affect the size of the state and the computational complexity can be kept constant. Yet the resolution of the generated paths is dependent on the resolution used in image, `128`x`128` single-channel in this case. Moreover, the encoding of information such as the **semantic intention** may be tricky, especially if expressed with uncertainties.

Several **hybrid representations**, i.e. the combination of an image and vector of some features, were used at IV19.

In (Folkers, Rick, and Christof 2019), the state of the MDP is composed of an **ego feature vector** and a **obstacle grid**. In this cost map, also called _ternary perception map_, the target position is given as −1, free space is encoded by 0, while obstacles and lane boundaries are indicated by 1.

| ![Construction of the ternary perception map (c) used as part of the MDP state. Source: (Folkers, Rick, and Christof 2019).](media/pics/folkers_perception_map.PNG "Construction of the ternary perception map (c) used as part of the MDP state. Source: (Folkers, Rick, and Christof 2019).")  |
|:--:|
| *Construction of the ternary perception map (c) used as part of the MDP state. Source: (Folkers, Rick, and Christof 2019).* |

The idea of an **hybrid representation** is also used in (Pusse and Klusch 2019). The observation input for the IS-DESPOT solver contains, inter alia, a `84`x`84` image representing the intention of the other vehicles.

### Scene Decomposition Methods

**Online scene decomposition** is used in (Bouton et al. 2019). The idea is to **combine the utility functions of simple tasks** to approximate the solution of a **more complex task**.

Here, the simple task aims at navigating the intersection assuming only one car, one pedestrian, and one obstacle. Including three agents enables to **capture interactions** between cars and pedestrians. This defines the so-called *canonical scenario*. In the presence of multiple cars and pedestrians, the global belief can be decomposed into **multiple instances of this canonical scenario**.

Finally, the action is selected based on the most conservative instance, i.e. with **worst probability of success** and the **worst utility**.

| ![Scene decomposition technique based on multiple instances of a canonical scenario. Source: (Bouton et al. 2019).](media/pics/bouton_generalization.PNG "Scene decomposition technique based on multiple instances of a canonical scenario. Source: (Bouton et al. 2019).")  |
|:--:|
| *Scene decomposition technique based on multiple instances of a canonical scenario. Source: (Bouton et al. 2019).* |

The utility decomposition method is also used in (Schratter et al. 2019a), where the simple task only considers one pedestrian.

Nevertheless, such decomposition methods **do not capture all the interactions**, especially those between the other vehicles or pedestrians.

This brings us to the topic of **interaction-aware reasoning**.

## Considering interaction between traffic participants

**Interaction-aware probabilistic decision making** is what most teams are targeting. The idea is to try to predict how ego actions could impact other traffic vehicles. **Predicting likely responses** is at the core of the human ability to navigate through a dense crowd, for instance in a train station. Such a skill is especially important for AD in **cooperative manoeuvres** such as *lane merging* and when considering *Vulnerable Road Users* (VRUs). Moreover, it allows for **negotiation between agents**.

Three points are considered in this section:

- The combination of prediction and planning.
- The consideration of interaction in prediction.
- The upcoming task of negotiation.

### Combining prediction and planning

Most AD software architectures **separate the prediction module and the planning module**. First, a trajectory of the surrounding vehicles is predicted. These **trajectories are then considered as obstacles** for the ego vehicle in the planning stage. But this separation results in a **reactive behaviour** which **does not consider interaction** during the trajectory planning.

The POMDP formulation can incorporate prediction (forward roll-outs based on the belief state) and planning, which allows to model **interactive behaviour**.

| ![Example of architecture where both prediction and planning are done in a single module, using a POMDP formulation. Source: (Schörner et al. 2019).](media/pics/schörner_pomdp.PNG "Example of architecture where both prediction and planning are done in a single module, using a POMDP formulation. Source: (Schörner et al. 2019).")  |
|:--:|
| *Example of architecture where both prediction and planning are done in a single module, using a POMDP formulation. Source: (Schörner et al. 2019).* |

### Interaction-aware prediction

Prediction approaches can be divided into three groups.

- In **Physics-based** models, traffic participants are assumed to keep a **constant velocity** (CV) or a **constant acceleration** (CA) for the next time step. The state of the observed vehicle can be **extrapolated** using kinematic *CA* or *CV* models. This usually works for **short horizons** (<`1s`) where **interaction does not play an important role**.
- In **Manoeuvre-based** models, traffic participants are assumed to execute their intended maneuvers independently from other traffic. The task is to try to infer the underlying manoeuvre among a finite set of discrete manoeuvres (e.g. lane keeping or lane change). The past trajectory or vehicle features are used for this classification problem. Again, interactions are not considered.
- In **Interaction-aware** models, future motion of each vehicle is assumed to be **influenced by other traffic participants**. An optimal predicted scene can be found by **minimizing the risk for all** the traffic participants. Such approaches provide a more reliable long-term prediction since, among other, they work on a symbolic level.

Several interaction-aware models were presented at IV19. To be honest, prediction is not my expertise and I found most related papers not trivial to understand. Here is was I will remember.

(Diehl et al. 2019) proposes to use Graph Neural Network (GNN). Interactions are modelled as directed edges leading from the up to 8 neighbouring vehicles to the ego vehicle.

| ![Graph Neural Networks are used to model interaction between traffic participants. Source: (Diehl et al. 2019).](media/pics/diehl_interaction_graph.PNG "Graph Neural Networks are used to model interaction between traffic participants. Source: (Diehl et al. 2019).")  |
|:--:|
| *Graph Neural Networks are used to model interaction between traffic participants. Source: (Diehl et al. 2019).* |

While many were using neural networks, a **Dynamic Bayesian Network** (DBN) were used by (Pool, Kooij, and Gavrila 2019). They use the concept of **_environment context cues_** to model the interaction between the cyclist and the ego vehicle. For instance, by indicating whether the arm of the cyclist is raised.

I noticed that two approaches were inspiring several IV19 works:

- The **social LSTM framework** for human trajectory predictions from (Alahi et al. 2016). Each trajectory is modelled as one LSTM and the different LSTMs **share information through a social pooling layer**.
- The **_DESIRE_** concept from (Lee et al. 2017). Trajectories are first generated using a **conditional variational auto-encoder** (CVAE), then ranked and refined using inverse optimal control (IOC) and finally used to build the scene context, encoded with a CNN.

My next observation was that **conditional variational auto-encoder** (CVAE) and **generative adversarial imitation learning** (GAIL) were largely used for interaction-prediction.

**CVAE** was used for instance in (Hu, Zhan, and Tomizuka 2019). The idea is to **jointly predict motions for pairs of interacting agents**. But not all vehicles are interacting with each other. This idea therefore to start by identifying the interacting pairs. To do that, intention probabilities are derived for each vehicle. Then, for each pair of vehicles, reference paths are used to **determine if a conflict point exists**. If not, their future trajectories will be independent from each other. For interacting pairs, their **joint motions are jointly predicted using a CVAE**.

This idea of **first identifying interation** and **then predict trajectories** is also found in (J. Li et al. 2019). They introduce a **hierarchical** structure for Coordination and Trajectory Prediction System (CTPS). First, a **macro-level recognizes the coordination** between the multiple interactive agents. This information, together with on historical states and the context information, is used by the **micro-level pattern prediction module** to generate motion hypotheses.

(Si and Liu 2019) shows that **GAIL** methods can learn interactive models. The approach, known as AGen (Adaptive Generative Model), is based on the learning method **PS-GAIL** (Parameter Sharing GAIL). However, it is not able to consider individual differences in the behaviour of each vehicle. PS-GAIL is therefore combined with RLS-PAA (Recursive Least Square Parameter Adaptation Algorithm) to achieve a better **adaptation to the multi-actor situation**.

I was not familiar with GAIL before and was more interested by **IRL-based methods** to learn from expert demonstration. But while IRL is trying to infer the underlying MDP **reward function**, it does not give the policy directly. The **optimal policy has to be derived afterwards**. Moreover, IRL-based methods require to model the problem as an MDP. Which is not easy. From this point of view, GAIL offers a valuable advantage.

### Negotiation as a next step

>> Merging is about creating space versus waiting for full space.

Jack Weast argues that **RSS is adapted to negotiation**, since it defines a safety envelop around the vehicle that can be used to **test how the others will respond**. For complex interactive scenarios such as _merging_, _overtaking_ and _yielding_, the vehicle can go right to the limit of the envelop, whose size is defined by the **risk tolerance**.

I did not hear so much about negotiation. Among of the papers, only one was mentioning the term "negotiation" more than once.

An **affair to follow** (_maybe looking at game theory?_) together with the **promising GAIL- and CVAE-approaches**.

## Scenarios and Datasets

I was interested in three challenges about scenarios for testing decision modules:

- How to **describe traffic scenarios**? What formalism / language to use?
- Where to find **frequent crash scenarios**, i.e. where human drivers usually fail?
- How to **generate critical scenarios** to test edge cases?
- What **datasets** can be used to learn and test algorithms for prediction and decision making?

These interrogations are addressed in this section.

Before I start, do you know the difference between a *situation*, a *scene* and a *scenario*? How would you define them? These terms are not used consistently in the literature. (Ulbrich et al. 2015) proposed **consistent definitions** for each of these terms of context modelling.

### Defining Scenarios

(Queiroz, Berger, and Czarnecki 2019) deplores the **lack of a language to formally describe test scenarios** that cover the complexity of road traffic situations. This hinders the **reproducibility of tests** and impairs the exchangeability between tools. To address that, they introduced [GeoScenario](https://git.uwaterloo.ca/wise-lab/geoscenario), a domain-specific language for **scenario representation**. This language is built on top of Open Street Maps (OSM) primitives. GeoScenario has a similar goal to the file format [OpenSCENARIO](http://www.openscenario.org/), which **describes dynamic contents** in driving simulation applications and is frequently used together with [OpenDRIVE](http://www.opendrive.org/) for the static content.

| ![JOSM adapted to design GeoScenario. Source: (Queiroz, Berger, and Czarnecki 2019).](media/pics/queiroz_geoScenario_tool.jpg "JOSM adapted to design GeoScenario. Source: (Queiroz, Berger, and Czarnecki 2019).")  |
|:--:|
| *JOSM adapted to design GeoScenario. Source: (Queiroz, Berger, and Czarnecki 2019).* |

Another tool, [CommonRoad](https://commonroad.in.tum.de/), was used several times, especially since it implements highway scenario from the [NGSIM US 101 dataset](https://www.fhwa.dot.gov/publications/research/operations/07030/). The goal of this **collection of composable benchmarks for motion planning** on roads is to provide researchers with a means of evaluating and comparing their motion planners.

To **represent the road network** in scenarios, I noticed that [Lanelets maps](https://github.com/phbender/liblanelet) (an open extension of the OSM format) are widely used (e.g. _GeoScenario_ and _CommonRoad_). The [Lanelets2 specification](https://github.com/fzi-forschungszentrum-informatik/lanelet2) is used in (Naumann et al. 2019a) to design a scenario with occlusion. Their experiment is based on a modified version of CommonROAD.

### Common Critical Scenarios

Most frequent crash scenarios can be retrieved from the National Highway Traffic Safety Administration (NHTSA - _pronounced “nit-suh”_) traffic crash data. This [NHTSA pre-crash typology](https://www.nhtsa.gov/sites/nhtsa.dot.gov/files/pre-crash_scenario_typology-final_pdf_version_5-2-07.pdf) is for instance used by the [CARLA AD challenge](https://carlachallenge.org/challenge/nhtsa/) to **test submissions on common critical scenarios**.

This **test evaluation** makes me think of the **driving license exams**. Humans are not tested on all possible scenarios. Instead, **a couple of situations is (randomly) sampled**, and the candidate is assessed on. This is how human driving skills are “validated”.

(Pusse and Klusch 2019) used the [GIDAS](https://www.gidas.org/willkommen/) (German In-Depth Accident Study) analysis of several **thousands of accidents that happened in Germany** to create a **benchmark**. Scenarios are then virtually simulated with the open-source 3D driving simulator [OpenDS](https://opends.dfki.de/).

The [EuroNCAP](https://www.euroncap.com/) test protocol for vulnerable road users (VRUs) is used in (Schratter et al. 2019b). The selected subset covers the most amount of accidents **involving pedestrians**. To avoid overly conservative behaviours, they **augmented the suite** of scenarios with a situation **involving occluded areas** at the side of the road.

| ![Example of EuroNCAP scenarios used to evaluate pedestrian collision avoidance systems. Source: (Schratter et al. 2019b).](media/pics/schratter_NCAP.PNG "Example of EuroNCAP scenarios used to evaluate pedestrian collision avoidance systems. Source: (Schratter et al. 2019b).")  |
|:--:|
| *Example of EuroNCAP scenarios used to evaluate pedestrian collision avoidance systems. Source: (Schratter et al. 2019b).* |

Only `75`% of all pedestrian accidents are covered with these crossing scenarios.
As for NHTSA, edge-case scenarios must be separately generated.

### Generating edge-case scenarios

(Klischat and Althoff 2019) present an approach to **generate critical scenarios** for testing motion planners in complex urban traffic situations.

### Needs for New Datasets

In many papers **real-world experiments are left for future works**. On reason it that it is difficult to **transfer models** derived from simulation or hand-crafted scenarios to real cars.

This raises two needs:

- Need for **more realistic simulators**.
- Need for **richer traffic datasets** with **critical scenarios**.

I do not addressed the question of **realistic simulators** here. Just mentioning one sentance that came several time at IV19:

> Chicken and Egg problem: validating the simulator is probably harder than validating the algorithm itself.

Real-world driving datasets are crucial for **learning-based approaches** which try to **model human driving styles** and behaviours.
Their **diversity** and **completeness** are also essential to allow for **generalisation in the prediction and decision models**.

Several collections of **scenarios recorded from real traffic** exist, as reviewed by (Kang, Yin, and Berger 2018) and listed by [scale.ai](https://scale.ai/open-datasets). But most entries **mainly cover perception**.

Works on interactions with **Vulnerable Road Users** (VRUs) (i.e. pedestrians and cyclists) can use Stanford Drone ([SD](http://cvgl.stanford.edu/projects/uav_data/)) dataset, the Tsinghua-Daimler Cyclist ([TDC](http://www.gavrila.net/Datasets/Daimler_Pedestrian_Benchmark_D/Tsinghua-Daimler_Cyclist_Detec/tsinghua-daimler_cyclist_detec.html)) dataset, the Joint Attention for Autonomous Driving ([JAAD](http://data.nvision2.eecs.yorku.ca/JAAD_dataset/)) Dataset or the recently released Eurocity Persons ([ECP](https://eurocity-dataset.tudelft.nl/)) dataset.

For **highway** and **intersection** scenarios, both [HighD](https://www.highd-dataset.com/) and [NGSIM](https://catalog.data.gov/dataset/next-generation-simulation-ngsim-vehicle-trajectories) were frequently mentioned during the workshops and poster sessions.
Vehicle trajectories in HighD were recorded **using a drone** on German highways, while the NGSIM dataset contains vehicle trajectory data recorded for 45 minutes by **cameras mounted on top of a building** in San Francisco Bay area. If this highway real traffic data is widely used, participants of the SIPD workshop argued they were **_"overused"_** and **_"totally boring"_** since there are **lacking complex manoeuvres**.

I realized that **dataset requirements differ between teams**. Some want **aerial views** for omniscience, especially to extract occlusion. Some prefer **data recorded from a vehicle** as (Pool, Kooij, and Gavrila 2019) that capture context cues to infer intention before planning, such as cyclists raising their arm before taking a turn.
An illustration of these **particular needs** is that many works presented at IV19 were using their own motion driving simulator or **build their own dataset**. Nevertheless, these individual developments make **experiments hard to reproduce, compare and benchmark**. In addition, most of these hand-crafted datasets are **quite small**, which is not ideal for **training learning-based models**, and do not contain many diverse situations.

The conclusion was clear: we are missing a dataset with **diverse, complex and critical situations** for interaction-aware prediction and decision making.

| ![Scenario involving negotiation in the new INTERnational, Adversarial and Cooperative moTION ([INTERACTION](http://interaction-dataset.com/)) Dataset. [Source](http://interaction-dataset.com/).](media/gif/interaction_dataset_nego.gif "Scenario involving negotiation in the new INTERnational, Adversarial and Cooperative moTION ([INTERACTION](http://interaction-dataset.com/)) Dataset. [Source](http://interaction-dataset.com/).")  |
|:--:|
| *Scenario involving negotiation in the new INTERnational, Adversarial and Cooperative moTION ([INTERACTION](http://interaction-dataset.com/)) Dataset. [Source](http://interaction-dataset.com/).* |

Wei Zhan, co-organizer of the SIPD workshop, took the opportunity to **announce the release of a new dataset**, named [INTERSECTION](http://interaction-dataset.com/) dataset, for **socially interactive prediction and decision making**. Here are the main points I have noted:

- It has been recorded from **drones** in diverse locations (e.g. US, Germany, China) with **different driving cultures** and traffic rules for **similar scenarios** (merging, roundabout, etc.).
- Critical situations such as near-collision situations and slightly colliding accidents are included, which is interesting to **test edge-cases situations**.
- Another promising feature is that **complex driving behaviours with negotiations** and **inexplicit right-of-way** are covered.
- Finally, all scenarios come with an [Lanelet2](https://github.com/fzi-forschungszentrum-informatik/Lanelet2)-based HD-map with semantic information. It will also have **occlusion as ground truth** in the model, which is key to test social perception.

Participants were **excited about this new dataset**. As Mykel Kochenderfer noted, it would be nice to also include **very unstructured traffic**. He suggested looking at a place like **India** with more than `17%` of the total world population and see if algorithms from western right-driving countries work there.

## AD and Society

_This section solely reflects the opinion and conclusion of its authors, and not ERS Labs AG._

> “Science sans conscience n'est que ruine de l'âme” – Rabelais

Everyone was having fun in the demo of IRSTEA - Institut Pascal. Originally a project on robotization and automation in agriculture, their prototype was presented as a **support for infantrymen**. The robot can follow a foot soldier while **caring heavy material**, memorize its path, and reproduce the trajectory to the base camp for replenishment. **The border from AD to military application is already crossed**. I am not sure every researchers and engineers realized that.

| ![Robot convoys from IRSTEA: the robot learns a trajectory from demonstration in a previously mapped environment. It will be able to reproduce this trajectory based on radar localization. Source: author provided.](media/gif/pascal_fantassin.gif "Robot convoys from IRSTEA: the robot learns a trajectory from demonstration in a previously mapped environment. It will be able to reproduce this trajectory based on radar localization. Source: author provided.")  |
|:--:|
| *Robot convoys from IRSTEA: the robot learns a trajectory from demonstration in a previously mapped environment. It will be able to reproduce this trajectory based on radar localization. Source: author provided.* |

In this section, I would like to address two topics:

- Social Acceptance of AD applications
- Social Benefit brought by AD applications

### Social Acceptance

> Technology-led discussions are often over optimistic.

Before attending the SIPD workshop, I could assist to the panel discussion of the workshop on [Ensuring and Validating Safety for Automated Vehicles](https://www.ifr.ing.tu-bs.de/en/dept-vehicle-electronics/events/iv-workshop-2019) (EVSAV).

The main point was: **The approach _Vision Zero_ promoted by most OEMs and Bay-area newcomers is misleading.**

> Many companies are establishing a strict _Vision Zero_ for road safety. As a result, no one will accept any fatalities. But asking for risk zero is just not realistic.

Markus Maurer, from TU Braunschweig, deplored the absence of a **rational discussion about risk**, arguing that most OEMs do not want to have a public debate about AD fatality risk. “We all know that we must minimize risk. But **it won’t reach zero**”. Engineers cannot decide on themselves and decisions should rather be discussed with the authorities and be **backed-up by the society**.

To show that **_risk zero_ is just not realistic**, Katherine Driggs-Campbell drew a parallel with the **introduction of automation in aviation** during the SIPD workshop. Introducing autonomy is supposed to be safer. But it has been shown by [Dominique Chatrenet](http://ec.europa.eu/invest-in-research/pdf/workshop/chatrenet%20_b3.pdf), for new piece of automation that is introduced, we see an **improvement in the long term** but a **spike in fatalities in the transient period**. Maybe _Vision Zero_ should be renamed _Vision Asymptotically Zero_ 😉

| ![For each new piece of automation that is introduced, we see an improvement in the long term but a spike in fatalities in the transient period. [Source](http://ec.europa.eu/invest-in-research/pdf/workshop/chatrenet%20_b3.pdf)](media/pics/chatrenet_automation_vs_fatalities.PNG "For each new piece of automation that is introduced, we see an improvement in the long term but a spike in fatalities in the transient period. [Source](http://ec.europa.eu/invest-in-research/pdf/workshop/chatrenet%20_b3.pdf)")  |
|:--:|
| *For each new piece of automation that is introduced, we see an improvement in the long term but a spike in fatalities in the transient period. [Source](http://ec.europa.eu/invest-in-research/pdf/workshop/chatrenet%20_b3.pdf)* |

Markus Maurer also warned that one single accident could have **significant detrimental impacts** for the involved manufacturer and for the whole industry.

Did manufacturers and the public learn all the lessons from the [Audi Sudden Acceleration case](https://www.autosafety.org/audi-sudden-acceleration/)? Back to 1985, this was a disaster for the goodwill of the brand and it took [15 years](https://en.wikipedia.org/wiki/Sudden_unintended_acceleration) for Audi to reach the same level of U.S. sales again.

And **media exposure** has increased since then.

For all these reasons, Markus Maurer **called the engineer to stand up**, **abandon _Vision Zero_**, **explain the risk**, and engage a **public discussion** before launching any AD products.

It leads to the **question of social benefits**.

But before going to that, one could argue that such a debate did not take place in the **aviation domain**. It raises an alternative question: _To what extend should engineer should communicate to the public?_ _Should we know everything?_ _Should some details be kept hidden?_ Airplane manufacturers do not explicitly communicate on risk. Or at least it is not covered in the media. But **we trust it**. The difference with the vehicles on the street is probably that not everyone is a pilot and that not everyone can compare the AD decisions with what he/she would have done instead.

> Acceptance is not based on risk. But rather on expectation of benefits, especially in entertainment.

Another participant brought another interesting point: He was arguing that **social acceptance is not (only) corelated to risk**, but rather **to the expectation of benefits**. He then drew a parallel with the development of smartphones. They always have been presented as _cool_. From the beginning, their risks did **not not carry much weight** in the face of the ability to watch video from everywhere, to access the web remotely and to increase one’s “socially” connection. And there has been no public discussion at all.

Today, AD is mostly in the **hand of industrials** and under the **eyes of regulators**. At the same time, **over-optimistic announcements are generating high expectations**. AD is presented as “cool”. Probably no room for any public discussion.

### Social Benefit

I found the keynote **_Public policy challenges of new mobility services_** by transport policy expert **Tom Vöge** was very appropriate. Not only to make a **break with scientific concepts** and algorithms. Also to **step back** and question our (excited) involvement in developing this technology. An **invitation to come down from the ivory (research) tower** and **question the social benefit of AD.

Well, before arguing about any benefit and threat, let me **restrict the following reflexion to level-5 commuting of civilian** (as opposed to long-distance mobility or military applications).

| ![Keynote by Tom Vöge. Source: author provided.](media/pics/vöge_keynote.JPG "Keynote by Tom Vöge. Source: author provided.")  |
|:--:|
| *Keynote by Tom Vöge. Source: author provided.* |

For this application, AD **holds a lot of promises** and is **supported by various groups**.

> "Some of the world’s largest car manufacturers and technology companies are competing to tap into what could be a $7-trillion revenue stream. Of course, industry talking points emphasize something besides money: safety". Ashley Nunes

As [stated](https://www.nature.com/articles/d41586-019-01473-3) by Ashley Nunes, "**Economists** describe how the technology will **upend trade**. **Urban planners** describe how our cities will soon look very different after they have been reshaped, owing to the **diminished need for car parks**. **Medical professionals** proclaim the **end of road-traffic accidents** as we know them." Some mention the mobility offered to people that are not in a state to drive. And the possibility to "let the car" take over in situations where driving is all but fun. One may also add “capturing brain time to offer entertainment, expose advertisement or impose remote work”.

Then come the threats. The main point of this keynote was about **share mobility** and **threat of congestion**.

> “if it leads to more sharing and more people to car, it is a good. Otherwise, there is no benefit at all. In Europe, commuting to/from work has an occupancy rate of 1.2. If could be 2, 3, 4, 10. Or it could become 0. That would be a disaster.”

| ![It was not at all clear that the number of vehicles on the roads would decrease as a result of automation. Source: author provided.](media/pics/vöge_sharing.jpg "It was not at all clear that the number of vehicles on the roads would decrease as a result of automation. Source: author provided.")  |
|:--:|
| *It was not at all clear that the number of vehicles on the roads would decrease as a result of automation. Source: author provided.* |

Tom Vöge warned against the risk of letting the self-driving market **develop on its own**, with little governmental intervention. Bay-areas companies want fast implementation and want to bring large changes. **They want to reinvent mobility with AD** and make authorities, transport operators and regulators lose their oversight. AD services will impose a **direct competition with legacy transport services**, which are often heavily regulated and protected.

AD will not substitute one to one an existing mobility mode and will rather be part of the multi-modal transports. Nevertheless, there is a (_non-zero_ here as well 😉) risk of **modal shift**, i.e. **people moving from green modes** such as cycling and walking to AD. And if it is not from green mode, it could be from **public transport**, especially from bus and trains. Finally, if people can **work during the transport**, longer trips will not be an issue, leading to additional **traffic and urban sprawl**.

The second concern of the keynote was about **perpetuating inequality**, with companies potentially deploying their vehicles in areas that make more economic sense for self-driving cars to operate. This would **marginalized unprivileged** and **low-density areas**. Considering the costly sensors and hardware, the enormous development costs and the non-cheap insurance contracts, AD products or services, even on shared vehicle and at wide scale, will be expensive. Tom Vöge called the governments to **ensure equal distribution in this technology**.

The benefit of safer vehicles technologies (not AD) introduced in the US between 1995 and 2010 already **failed to spread evenly across the socio-economic spectrum**, as shown in [this study](https://academic.oup.com/aje/article/182/7/606/107280). In 1995 the mortality rates related to motor-vehicle **accidents involving people at the bottom of the education spectrum** were **2.4 times higher** than those involving people at the top. By 2010, they were about **4.3 times higher**.

In the recent excellent [article](https://www.nature.com/articles/d41586-019-01473-3) “Driverless cars: researchers have made a wrong turn”, Ashley Nunes builds on that and asks:

>> If there is a group in the United States that stands to benefit most from the life-saving potential of self-driving technology, it’s those who live in the greatest poverty, but only if they can afford the technology. Driverless-car technology might have the potential to improve public health and save lives, but if those who most need it don’t have access, whose lives would we actually be saving?

| ![We tend to overestimate the effect of a technology in the short run and underestimate the effect in the long run. [Source](https://www.gartner.com/smarterwithgartner/5-trends-emerge-in-gartner-hype-cycle-for-emerging-technologies-2018/).](media/pics/gartner18.jpg "We tend to overestimate the effect of a technology in the short run and underestimate the effect in the long run. [Source](https://www.gartner.com/smarterwithgartner/5-trends-emerge-in-gartner-hype-cycle-for-emerging-technologies-2018/).")  |
|:--:|
| *We tend to overestimate the effect of a technology in the short run and underestimate the effect in the long run. [Source](https://www.gartner.com/smarterwithgartner/5-trends-emerge-in-gartner-hype-cycle-for-emerging-technologies-2018/).* |

Tom Vöge ended by mentioning the **potential scenarios of AD development**. From the 2018 Gartner hype cycle, Level-5 ADs are about to **reach their peak of inflated expectations**, while level-4 technology is already going down to the “through of disillusionment”. What is coming next is very uncertain. He warned against **being blinded by the current hype**. The technology is still in its infancy, nobody knows where it will lead. He ended with:

> The distant future can still not be foreseen - but it can be shaped.

## Demonstrations

> “In Theory There Is No Difference Between Theory and Practice.”

### Test Day

Last day was test day! Companies, universities and research institutes were invited to demonstrate technological achievements in AD.

I could assist to most demonstrations. Here are some highlights.

No public HD-map was available for the test tracks. Most exhibitors had been mapping the environment the days before. And localisation was based on feature matching with radar (IRSTEA - Institut Pascal), Lidar (Valeo) or Camera (KIT).

| ![Key-point detections shown on grayscale stereo images in the camera-only localisation system of KIT. Red points are detections that are not matching features extracted from the previously constructed map. Source: author provided](media/gif/kit_detecton.gif "Key-point detections shown on grayscale stereo images in the camera-only localisation system of KIT. Red points are detections that are not matching features extracted from the previously constructed map. Source: author provided")  |
|:--:|
| *Key-point detections shown on grayscale stereo images in the camera-only localisation system of KIT. Red points are detections that are not matching features extracted from the previously constructed map. Source: author provided* |

Several **heavy rainfalls** rhythmed the day. Fortunately, we could seek shelter under tents. IAV’s demo was interrupted to protect the sensors placed on the roof. Meanwhile, the KIT car could keep driving under the rain but must then stop as the sun reappeared because  **reflexions on the road** were fooling its **camera-based localisation**.

| ![Roof sensors of the IAV GmbH vehicle had to be specially protected from rain. Source: author provided.](media/pics/iav_rain.jpg "Roof sensors of the IAV GmbH vehicle had to be specially protected from rain. Source: author provided.")  |
|:--:|
| *Roof sensors of the IAV GmbH vehicle had to be specially protected from rain. Source: author provided.* |

A research team from [TU Delft](http://intelligent-vehicles.org/research/projects/) presented their work on [SafeVRU](https://www.swov.nl/en/project/safevru-safe-interaction-automated-vehicles-vulnerable-road-users), a platform for interaction with *Vulnerable Road Users* (**VRUs**). In the scenario, a pedestrian was entering laterally the path of the AD vehicle. The main goal of the demo was **to detect if the pedestrian saw the car or not**. In the first drive, **no eye contact had been detected**, and an avoidance manoeuvre was performed. In the second drive, the head of the dummy was oriented toward the vehicle that **could detect its sight** *(green confidence at the bottom-left corner)*. The AD vehicle could **deduce that the pedestrian will stop** *(this is a strong assumption)* and consequently did not take an avoidance manoeuvre. With the increasing trend of [Smombie](https://en.wikipedia.org/wiki/Smartphone_zombie), this type of intersection might probably become difficult to capture.

| ![Second drive of TU Delft. This time the walking dummy is looking at us. Source: author provided.](media/gif/tu_delft.gif "Second drive of TU Delft. This time the walking dummy is looking at us. Source: author provided.")  |
|:--:|
| *Second drive of TU Delft. This time the walking dummy is looking at us. Source: author provided.* |

A research team from KIT also demonstrated perception and planning in presence of VRUs. It **detects a bicycle** in the right lane and decides to **overtake it, going to the opposite lane**.

| ![Some issues with the localisation system caused the overtaking manoeuvre to be a little bit abrupt. Source: author provided.](media/gif/kit_overtaking.gif "Some issues with the localisation system caused the overtaking manoeuvre to be a little bit abrupt. Source: author provided.")  |
|:--:|
| *Some issues with the localisation system caused the overtaking manoeuvre to be a little bit abrupt. Source: author provided.* |

| ![Back trunk of the KIT car. Source: author provided.](media/pics/kit_back_trunk.JPG "Back trunk of the KIT car. Source: author provided.")  |
|:--:|
| *Back trunk of the KIT car. Source: author provided.* |

| ![Air-cooling hose coming from the back trunk of KIT car. Source: author provided.](media/pics/kit_cooling_system.JPG "Air-cooling hose coming from the back trunk of KIT car. Source: author provided.")  |
|:--:|
| *Air-cooling hose coming from the back trunk of KIT car. Source: author provided.* |

The European project [AutoMate](http://www.automate-project.eu/) project demonstrated **collaboration between the AD vehicle and the human driver** (hence suffix *“mate”* for *“teammate”*). When approaching a roundabout, the human driver, whose attention is constantly monitored, was **asked to take over the lateral control**, i.e. the steering commands. An explanation for this request was given on the onboard display. After exiting the roundabout, the system **proposed to resume the AD mode**.

I found a little bit impressive to drive at 70 km/h on a countryside-like road with the AD mode.

| ![AD system asking for human collaboration when "approaching" a roundabout. Source: author provided.](media/gif/automate_take_over.gif "AD system asking for human collaboration when "approaching" a roundabout. Source: author provided.")  |
|:--:|
| *AD system asking for human collaboration when "approaching" a roundabout. Source: author provided.* |

This demonstration raises questions about the cooperation between human driver and the autopilot. Here, the driver cannot be described as “distracted”. He was paying much attention and could therefore quickly take over.

In addition, only one request was submitted during the test. I can imagine it could be **annoying to be repetitively asked** for collaboration. That reminded me an oral session, where a reference to [The Boy Who Cried Wolf fable](https://en.wikipedia.org/wiki/The_Boy_Who_Cried_Wolf) was made to highlight the **risk of false alarms**: too many unnecessary warnings and calls for disengagement **might discourage driver vigilance**.

Another thing I noticed is the **central display**. It is supposed to **inform the driver about the reason of disengagement**. But it may be not easy to understand, especially given the of risk/importance of the situation and the short time available to react. **OEMs still must prove** that **handover** of vehicle control can be done **safely**, **intuitively**, without **“disrupting user experience”**. 😉

> To me, it is harder to monitor the autopilot than to drive on my own.

This also raises the question of the **driving licence**. In the aviation domain, pilots are trained to handle such transitions and to collaborate. Introducing **level-3 vehicles** (sometimes called *“conditionally-automated”*) in hands of **untrained people** is questionable. In the debate over **supervised vs unsupervised driving modes**, multiple OEMs have already announced they will **skip level-3**, arguing that **this handover of control is unsafe**.

A research team from UTC presented a **cooperative communication** between two vehicles for roundabout crossing. When arriving at the entrance, our vehicle **agreed** with the vehicle driving on roundabout **who should go first**. This V2V solution removes the need for the **challenging inference of intention**. Given **all participants are connected**.

| ![V2V communication for roundabout crossing. Source: author provided.](media/gif/utc_v2v_roundabout.gif "V2V communication for roundabout crossing. Source: author provided.")  |
|:--:|
| *V2V communication for roundabout crossing. Source: author provided.* |

I felt very comfortable on the forest track driven by the Valeo [Drive4U® Urban Pilot](https://www.valeo.com/en/valeo-drive4u-the-first-autonomous-car-to-be-demonstrated-on-the-streets-of-paris/). Moreover, and compared to other AD demonstrators, all sensors were embedded and there was almost no cable visible in the vehicle cabin. And they did not hide everything in the back trunk. 😉

| ![Valeo Drive4U® Urban Pilot. Source: author provided.](media/pics/valeo.JPG "Valeo Drive4U® Urban Pilot. Source: author provided.")  |
|:--:|
| *Valeo Drive4U® Urban Pilot. Source: author provided.* |

PSA did not show any prototype. Instead I could test a pre-series car with ADAS features like *Autonomous Emergency Braking* (AEB). **AEB was demonstrated with a static target**. From the passenger seat, I found the braking manoeuvre impressive, even at **25km/h**. The car managed to stop before the static "dummy" *(yes, this is the term)* without any contact (approx. one-meter gap).

| ![AEB at 25 km/h with a static dummy. Source: author provided.](media/gif/psa_aeb.gif "AEB at 25 km/h with a static dummy. Source: author provided.")  |
|:--:|
| *AEB at 25 km/h with a static dummy. Source: author provided.* |

### Miscellaneous Comments

| ![Most teams were using ROS (ROS1 or ROS2) and the related visualization tool [RViz](http://wiki.ros.org/rviz). Convenient to display multiple windows. Source: author provided.](media/pics/kit_rviz.JPG "Most teams were using ROS (ROS1 or ROS2) and the related visualization tool [RViz](http://wiki.ros.org/rviz). Convenient to display multiple windows. Source: author provided.")  |
|:--:|
| *Most teams were using ROS (ROS1 or ROS2) and the related visualization tool [RViz](http://wiki.ros.org/rviz). Convenient to display multiple windows. Source: author provided.* |

| ![Most teams were using 3D-printed black casings to prevent reflexion from the dashboard in their cameras. Source: author provided.](media/pics/anti_reflexion_casing.JPG "Most teams were using 3D-printed black casings to prevent reflexion from the dashboard in their cameras. Source: author provided.")  |
|:--:|
| *Most teams were using 3D-printed black casings to prevent reflexion from the dashboard in their cameras. Source: author provided.* |

Another comment, about the type of the presented AD vehicles. Most were car-like. And during the four days of workshop, conference and demonstration, I was surprise not to see more **AD applications for trucks**. Due to its **market size** and its **market value**, and considering he **might present lighter technical challenges** than for AD cars, self-driving trucks are sometimes [predicted to be first to market](https://medium.com/redpoint-ventures/why-self-driving-trucks-will-be-first-to-market-and-why-we-invested-in-ike-6a8f8114c8ec). Nevertheless, **truck applications were almost absent from IV19**. If they were used multiple times as slow dynamic obstacles to avoid, the term “truck” was present in only seven abstracts.

### Discussions With Some Sponsors

Several sponsors were presented solution during the conference. I talked to two of them. Sorry, I do not have anything to show here.

[Intempora](https://intempora.com/) presented, among other, its [Intempora Validation Suite](https://intempora.com/products/ivs-intempora-validation-suite.html) (IVS). No, it does not stand for "Intelligent Vehicle Symposium". 😜

IVS allows to centralize, store and share **recorded sensor data**. Descriptions of the driving situations are added. It is then possible to **search for a specific traffic situation**. Convenient for **regression tests** and for **debugging**.

I was showed how to **quickly retrieve all recorded highway scenarios** where the ego-car was driving on the **left-lane**, with a speed **higher than 110km/h**, while **taking over a truck**. Obviously the quality of this **tag-based search relies on annotations**. They can be done online or offline.

EasyMile and safety validation.

I really enjoyed discussing with some of the engineers from EasyMile! They stressed the importance of the **development process** for **safety validation**. They took the example of the aviation domain where some **sensors must be made redundant** and must even originate from two different manufacturers. This is currently not the case for AD where, despite multi-modality in perception, there is usually one single camera per direction. What is more important to the safety validation authorities is **how the full system has been developed**. In particular the **“safety checker”** that **runs in parallel to the driving functions** must have been **developed independently** of these driving functions, by an **independent team**.

## Conclusion

Tom Vöge ended his keynote with a **gentle joke to EasyMile**. He mentioned the EU-funded project [parkshut](https://staff.washington.edu/jbs/itrans/parkshut.htm) to was developing an AD shuttle service. The park shuttle was released to the public in April 1999. He said:

> “Twenty years later we are still at the same point with AD: demonstration”.

| ![ParkShuttle (1999) and EasyMile (2019). Sources: [top](https://staff.washington.edu/jbs/itrans/parkshut.htm) - [bottom](https://www.facebook.com/EasyMile/).](media/pics/easymile_vs_park_shuttle.jpg "ParkShuttle (1999) and EasyMile (2019). Sources: [top](https://staff.washington.edu/jbs/itrans/parkshut.htm) - [bottom](https://www.facebook.com/EasyMile/).")  |
|:--:|
| *ParkShuttle (1999) and EasyMile (2019). Sources: [top](https://staff.washington.edu/jbs/itrans/parkshut.htm) - [bottom](https://www.facebook.com/EasyMile/).* |

This is a little bit unfair for EasyMile since they are one of the very few to already operate AD vehicles with public. But this punchy conclusion reminded everyone that **we still have a long way to go before AD exit, and become beneficial for the society**.

### A Last Word

IV19 was my first scientific symposium. I could meet leading-edge researchers and engineers in my domain of interest while broadening my perspectives on other AD topics.

Again, many thanks at [ESR Labs AG](https://www.esrlabs.com) for this great opportunity. I am now looking forward to implementing the learnt lessons to [some of our projects](https://www.esrlabs.com/works/autonomous).

### Save The Date - an some dollars :bowtie:

[IV20](https://2020.ieee-iv.org/) will take place in Las Vegas, June 23-26, while Japan will host IV21, with probably a lot of amazing demos to show after the 2020 Olympics.

| ![Brendan Morris advertising IV20. Source: author provided](media/pics/iv20.jpg "Brendan Morris advertising IV20. Source: author provided")  |
|:--:|
| *Brendan Morris advertising IV20. Source: author provided* |

## References

Akai, Naoki et al. [2019].
**“Driving Behavior Modeling Based on Hidden Markov Models with Driver’s Eye-Gaze Measurement and Ego-Vehicle Localization”**
[[pdf](https://www.researchgate.net/publication/332543438_Driving_Behavior_Modeling_Based_on_Hidden_Markov_Models_with_Driver%27s_Eye-Gaze_Measurement_and_Ego-Vehicle_Localization)]

Alahi, Alexandre et al. [2016].
**“Social LSTM: Human Trajectory Prediction in Crowded Spaces”**
[[pdf](http://cvgl.stanford.edu/papers/CVPR16_Social_LSTM.pdf)]

Barbier, Mathieu et al. [2019].
**“Validation of Perception and Decision-Making Systems for Autonomous Driving via Statistical Model Checking”**
[[pdf](https://hal.inria.fr/hal-02127889/document)]

Barbier, Mathieu, Christian Laugier, Olivier Simonin, and Javier Ibanez-Guzmin. [2018].
**“Probabilistic Decision-Making at Road Intersections: Formulation and Quantitative Evaluation”**
[[pdf](https://hal.inria.fr/hal-01940392/document)]

Bernhard, Julian, and Alois Knoll. [2019].
**“Addressing Inherent Uncertainty: Risk-Sensitive Behavior Generation Using Distributional Reinforcement Learning”**
[[html](https://mediatum.ub.tum.de/node?id=1485138)]

Bouton, Maxime, Alireza Nakhaei, Kikuo Fujimura, and Mykel J Kochenderfer. [2019].
**“Safe Reinforcement Learning with Scene Decomposition for Navigating Complex Urban Environments”**
[[pdf](https://arxiv.org/abs/1904.11483)]

Chauvin, Simon. [2018].
**“Hierarchical Decision-Making for Autonomous Driving”**
[[pdf](https://github.com/chauvinSimon/Hierarchical-Decision-Making-for-Autonomous-Driving)]

Chen, Jianyu, Bodi Yuan, and Masayoshi Tomizuka. [2019].
**“Model-Free Deep Reinforcement Learning for Urban Autonomous Driving”**
[[pdf](http://arxiv.org/abs/1904.09503)]

Chen, Longsheng et al. [2019].
**“An Adaptive Path Tracking Controller Based on Reinforcement Learning with Urban Driving Application”**
[-]

Diehl, Frederik, Thomas Brunner, Michael Truong Le, and Alois Knoll. [2019].
**“Graph Neural Networks for Modelling Traffic Participant Interaction”**
[[pdf](https://arxiv.org/abs/1903.01254)]

Driggs-Campbell, Katherine, Roy Dong, S. Shankar Sastry, and Ruzena Bajcsy. [2017].
**“Robust, Informative Human-in-the-Loop Predictions via Empirical Reachable Sets”**
[[pdf](https://arxiv.org/abs/1705.00748)]

Esterle, Klemens, Vincent Aravantinos, and Alois Knoll. [2019].
**“From Specifications to Behavior: Maneuver Verification in a Semantic State Space”**
[[pdf](https://arxiv.org/abs/1905.00708)]

Folkers, Andreas, Matthias Rick, and B Christof. [2019].
**“Controlling an Autonomous Vehicle with Deep Reinforcement Learning”**
[-]

Hartmann, Michael, and Daniel Watzenig. [2019].
**“Optimal Motion Planning with Reachable Sets of Vulnerable Road Users”**
[-]

Hoel, Carl-Johan et al. [2019].
**“Combining Planning and Deep Reinforcement Learning in Tactical Decision Making for Autonomous Driving”**
[[pdf](https://arxiv.org/abs/1905.02680)]

Hu, Yeping, Wei Zhan, and Masayoshi Tomizuka. [2019].
**“Multi-Modal Probabilistic Prediction of Interactive Behavior via an Interpretable Model”**
[[pdf](https://arxiv.org/abs/1903.09381)]

Hubmann, Constantin et al. [2019].
**“A POMDP Maneuver Planner For Occlusions in Urban Scenarios”**
[-]

Joonatan, Mänttäri, and John Folkesson. [2019].
**“Incorporating Uncertainty in Predicting Vehicle Maneuvers at Intersections With Complex Interactions”**
[-]

Kang, Yue, Hang Yin, and Christian Berger. [2018].
**“Test Your Self-Driving Algorithm : An Overview of Publicly Available Driving Datasets and Virtual Testing Environments”**
[[pdf](https://www.researchgate.net/publication/331723628_Test_Your_Self-Driving_Algorithm_An_Overview_of_Publicly_Available_Driving_Datasets_and_Virtual_Testing_Environments)]

Kapania, Nitin R., Vijay Govindarajan, Francesco Borrelli, and J. Christian Gerdes. [2019].
**“A Hybrid Control Design for Autonomous Vehicles at Uncontrolled Crosswalks”**
[[pdf](https://arxiv.org/abs/1902.00597)]

Klischat, Moritz, and Matthias Althoff. [2019].
**“Generating Critical Test Scenarios for Automated Vehicles with Evolutionary Algorithms”**
[[pdf](https://www.researchgate.net/publication/333186174_Generating_Critical_Test_Scenarios_for_Automated_Vehicles_with_Evolutionary_Algorithms)]

Koenig, Alexander, Tobias Rehder, and Soeren Hohmann. [2017].
**“Exact Inference and Learning in Hybrid Bayesian Networks for Lane Change Intention Classification”**
[[html](https://ieeexplore.ieee.org/document/7995927/)]

Kong, Jason, Mark Pfeiffer, Georg Schildbach, and Francesco Borrelli. [2015].
**“Kinematic and Dynamic Vehicle Models for Autonomous Driving Control Design”**
[[pdf](https://borrelli.me.berkeley.edu/pdfpub/IV_KinematicMPC_jason.pdf)]

Lee, Namhoon et al. [2017].
**“DESIRE : Distant Future Prediction in Dynamic Scenes with Interacting Agents”**
[[pdf](https://arxiv.org/abs/1704.04394)]

Li, Jiachen, Hengbo Ma, Wei Zhan, and Masayoshi Tomizuka. [2019].
**“Coordination and Trajectory Prediction for Vehicle Interactions via Bayesian Generative Modeling”**
[[pdf](http://arxiv.org/abs/1905.00587)]

Li, Yang et al. [2019].
**“Pedestrian Trajectory Prediction With Learning-Based Approaches : A Comparative Study”**
[-]

Ma, Hengbo, Jiachen Li, Wei Zhan, and Masayoshi Tomizuka. [2019].
**“Wasserstein Generative Learning with Kinematic Constraints for Probabilistic Prediction of Interactive Driving Behavior”**
[-]

Naumann, Maximilian, Hendrik Königshof, Martin Lauer, and Christoph Stiller. [2019].
**“Safe but Not Overcautious Motion Planning under Occlusions and Limited Sensor Range”**
[[pdf](https://www.mrt.kit.edu/z/publ/download/2019/Naumann2019Occlusions.pdf)]

Orzechowski, Piotr F, Annika Meyer, and Martin Lauer. [2018].
**“Tackling Occlusions & Limited Sensor Range with Set-based Safety Verification”**
[[pdf](https://arxiv.org/abs/1807.01262)]

Pierson, Alyssa, Wilko Schwarting, Sertac Karaman, and Daniela Rus. [2019].
**“Learning Risk Level Set Parameters from Data Sets for Safer Driving”**
[-]

Pool, Ewoud A I, Julian F P Kooij, and Dariu M Gavrila. [2019].
**“Context-Based Cyclist Path Prediction Using Recurrent Neural Networks”**
[-]

Pusse, Florian, and Matthias Klusch. [2019].
**“Hybrid Online POMDP Planning and Deep Reinforcement Learning for Safer Self-Driving Cars”**
[[html](https://www.dfki.de/en/web/research/projects-and-publications/publications/publication/10337/)]

Queiroz, Rodrigo, Thorsten Berger, and Krzysztof Czarnecki. [2019].
**“GeoScenario : An Open DSL for Autonomous Driving Scenario Representation”
[[pdf](http://www.cse.chalmers.se/~bergert/paper/2019-iv-geoscenario.pdf)]

Rehder, Tobias et al. [2019].
**“Lane Change Intention Awareness for Assisted and Automated Driving on Highways”**
[[html](https://www.researchgate.net/publication/331913422_Lane_Change_Intention_Awareness_for_Assisted_and_Automated_Driving_on_Highways)]

Schörner, Philip, T Lars, Jens Doll, and J Z Marius. [2019].
**“Predictive Trajectory Planning in Situations with Hidden Road Users Using Partially Observable Markov Decision Processes”**
[[pdf](https://www.ifr.ing.tu-bs.de/static/files/forschung/papers/download_pdf.php?id=739)]

Schratter, Markus, Maxime Bouton, Mykel J. Kochenderfer, and Daniel Watzenig. [2019].
**“Pedestrian Collision Avoidance System for Scenarios with Occlusions”**
[[pdf](https://arxiv.org/abs/1904.11566)]

Schulz, Jens et al. [2019].
**“Learning Interaction-Aware Probabilistic Driver Behavior Models from Urban Scenarios”**
[-]

Si, Wenwen, and Changliu Liu. [2019].
**“AGen : Adaptable Generative Prediction Networks for Autonomous Driving”**
[[html](https://www.ri.cmu.edu/publications/agen-adaptable-generative-prediction-networks-for-autonomous-driving/)]

Sun, Liting, Wei Zhan, Ching-Yao Chan, and Masayoshi Tomizuka. [2019].
**“Behavior Planning of Autonomous Cars with Social Perception”**
[[pdf](https://arxiv.org/abs/1905.00988)]

Sunberg, Zachary, and Mykel Kochenderfer. [2017].
**“Online Algorithms for POMDPs with Continuous State, Action, and Observation Spaces”**
[[pdf](https://arxiv.org/abs/1709.06196)]

Ulbrich, Simon et al. [2015].
**“Defining and Substantiating the Terms Scene , Situation , and Scenario for Automated Driving”**
[[pdf](https://www.ifr.ing.tu-bs.de/static/files/forschung/papers/download_pdf.php?id=859)]

Ure, N Kemal, M Ugur Yavas, Ali Alizadeh, and Can Kurtulus. [2019].
**“Enhancing Situational Awareness and Performance of Adaptive Cruise Control through Model Predictive Control and Deep Reinforcement Learning”**
[-]

Zernetsch, Stefan et al. [2019].
**“Trajectory Forecasts with Uncertainties of Vulnerable Road Users by Means of Neural Networks**
[-]