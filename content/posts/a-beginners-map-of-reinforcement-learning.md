---
title: "A Beginner's Map of Reinforcement Learning"
date: 2026-08-20
draft: false
tags: ["reinforcement-learning", "machine-learning", "notes"]
categories: ["Reinforcement Learning"]
summary: "From Bellman equations to PPO and TD3 — the concepts, the key insights, and the algorithm landscape, organized the way I learned them."
ShowToc: true
TocOpen: false
---

When I started learning reinforcement learning (RL), the hardest part was not any single algorithm — it was keeping the whole picture in my head. Every algorithm seemed to answer a slightly different question, and the textbooks introduced them in an order that only made sense in hindsight.

This post is the map I wish I had on day one. It has three parts:

1. **Core concepts** — the vocabulary everything else is built on.
2. **Key insights** — a few connections that made things click for me.
3. **The algorithm landscape** — model-based vs. model-free methods, from value iteration all the way to PPO and TD3.

A quick informal setup before we begin: in RL, an **agent** observes a **state** *s*, chooses an **action** *a* according to a **policy** π, receives a **reward** *r* from the **environment**, and lands in a new state *s′*. The goal is to find a policy that maximizes the long-run (discounted) sum of rewards, called the **return**. Everything below is about making that precise and learnable.

---

## Part 1: Core Concepts

### State transitions

After the agent takes an action, the next state is determined by the environment — and the environment may be random. This means the state transition can be **stochastic**: the same action in the same state may lead to different next states. (The environment can also be deterministic, in which case transitions are deterministic too.)

A standard assumption in RL is that the state transition function is **stationary**: it does not change over time *t*. The world's rules stay fixed while the agent learns.

### The Bellman equation

The **Bellman equation (BE)** connects the values of different states: each state's value depends on the values of other states, giving a *recursive relationship among state values*.

Two versions matter:

- **BE** — the recursive relationship between state values under a *given* policy.
- **Bellman optimality equation (BOE)** — the same recursion, but under the *optimal* policy. The BOE is simply the Bellman equation evaluated at the optimal policy.

Inside both sits the discount factor **γ**, which controls how *near-sighted or far-sighted* the agent is: small γ means it mostly cares about immediate rewards; large γ means it values the distant future.

Two facts about the BOE worth remembering:

- The solution for the **state values is unique**, but the **optimal policy is not necessarily unique** — different policies can achieve the same optimal values.
- **Value iteration** and **policy iteration** are two classic ways to solve it (more on these in Part 3).

### Value functions

Value functions are the currency of RL. There are three you must keep apart.

**Action-value function Q(s, a).** This is a *conditional expectation*: by taking an expectation, it eliminates all the uncertainty from time *t+1* onward. What remains are exactly three influences:

- the current state *s_t*,
- the current action *a_t*,
- the **policy** π — and because Q depends on the policy, it can be used to *evaluate* a policy.

**Optimal action-value function Q\*(s, a).** Think of Q\* as a *prophet*. It builds on Q but removes the influence of the policy by assuming the best possible policy is followed from here on. Because of that:

- it can be used directly to *guide the agent's actions* — to pick the best state–action pair;
- given *s_t* and *a_t*, **no matter what policy π you follow afterward, the expected return U_t can never exceed Q\***. It is the ceiling.

**State-value function V(s).** Take Q(s, a) and average out the action (an expectation over actions under the policy). The action's uncertainty disappears, and V depends only on the policy and the current state.

A clean way to keep them apart: **V evaluates a state; Q evaluates a (state, action) pair.** Both are weighted averages — the same idea of expectation, applied at different points.

Finally, there are two routes to obtaining action values: solving the **BOE** (which requires a model of the environment), or **estimating them from data** (which does not). This split foreshadows the model-based vs. model-free divide in Part 3.

### Policies

A policy is what the agent uses to choose actions. It can be sliced three different ways.

**Deterministic vs. stochastic.**

- A **deterministic policy** maps each state to exactly one action. The classic example is the **greedy policy**: always pick the action with the largest action value.
- A **stochastic policy** assigns probabilities to actions. A **soft policy** like **ε-greedy** gives the greedy action most of the probability but leaves some probability for the others, so non-optimal actions can still be selected. This is how RL balances **exploitation** (use what you know) against **exploration** (try what you don't).
- A deterministic policy is just a special case of a stochastic one: all probability concentrated on a single action.

**On-policy vs. off-policy.**

- **On-policy**: the policy that collects data (behavior policy) and the policy being improved (target policy) are *the same*. Examples: SARSA, REINFORCE, A2C.
- **Off-policy**: they are *different*. The exploration must come from the behavior policy — if the acting policy were purely deterministic, nothing new would ever be tried. Off-policy methods use the behavior policy to collect experience into a **replay buffer** and perform **experience replay** to update the target policy.

**Behavior policy vs. target policy.**

- The **behavior policy** controls the agent's interaction with the environment and collects experience tuples (observation, action, reward). Crucially, the policy is *not updated during collection* — so all collected experience comes from a stale, old policy. This is why such data can only be reused when the quantity being learned *does not depend on the policy* — for instance the optimal value in off-policy methods like Q-learning and DPG.
- The **target policy** is the policy we ultimately want: the optimal one that will actually control the agent.

### Episodes and epochs

Two words that sound similar but belong to different worlds:

- An **episode** is one full run of the agent from start to termination. All the states, actions, and rewards observed along the way form a **trajectory**.
- An **epoch** is a supervised-learning term: one pass of the training data through forward computation and backpropagation (each data point used once).

### MDP: the formal frame

A **Markov Decision Process (MDP)** is usually the four-tuple (S, A, p, r): the state space, the action space, the state transition function, and the reward function. Sometimes it is written as a five-tuple (S, A, p, r, γ) including the discount factor.

The **Markov property** is the key assumption: *the next state depends only on the current state and action, not on the past.* The present is a sufficient summary of history.

### Bootstrapping

**Bootstrapping** means *using one estimate to update another estimate of the same kind* — for example, updating a value estimate using another value estimate rather than waiting for real outcomes.

Its signature trade-off: **low variance and fast convergence**, at the cost of bias. Monte Carlo methods (coming in Part 3) have exactly the opposite profile — this contrast is one of the most useful axes for comparing algorithms.

### Experience replay

Experience is stored in a **replay buffer** and used to train the agent repeatedly.

- The buffer capacity is a **hyperparameter** to tune. In practice, the agent keeps interacting with the environment and only starts updating parameters once enough experience has accumulated.
- Sampling **randomly** from the buffer makes the drawn experiences approximately independent — this **breaks the correlation** between consecutive transitions and leads to better training.
- Note the two different quantities at play: the number of *samples* is limited by how much real interaction you can afford, while the number of *updates* is limited by compute. The former is the scarcer, more important resource.

**Prioritized experience replay** goes one step further: experiences are sampled with *non-uniform* probabilities (important ones more often), and the learning rate is adjusted to compensate — samples drawn with high probability get a smaller learning rate. This uses data more efficiently, but costs more computation and converges more slowly per step.

### Importance sampling

Importance sampling is the technique that lets you estimate an expectation under one distribution using samples drawn from *another* distribution, by reweighting each sample. It is the mathematical bridge that turns on-policy methods into off-policy ones — we will meet it properly in the A2C and PPO sections below.

---

## Part 2: Key Insights

These are the connections that, for me, turned a pile of definitions into an actual picture.

### Randomness in RL has exactly two sources

1. **Action randomness** — the policy makes stochastic decisions.
2. **State randomness** — the state transition function. Even after the agent has committed to action *a* in state *s*, the environment may still send it to different next states *s′*; the next state is a random sample.

What about the reward? The reward *r* is fully determined by the current *s* and *a* — but because *s* and *a* are themselves random, the reward inherits their uncertainty.

Almost every "why is there an expectation here?" question in RL traces back to one of these two sources.

### Q-learning vs. Sarsa

The essential difference is: **what expression is each one estimating, and does that expression depend on the policy?** Sarsa estimates the action value *of the current policy* (so it must stay on-policy); Q-learning estimates the *optimal* action value directly (so it is free to be off-policy). Keep this question in mind and the two algorithms never blur together again.

### TD errors: from one step to Monte Carlo

Temporal-difference (TD) learning updates a prediction by comparing it against a slightly *better-informed* prediction.

How to understand the "step"? In **one-step TD**, you take one real step, observe one real reward, and form a new prediction that is *partly grounded in fact*. The TD error is the difference between this updated prediction and the prediction from the previous time step — and you update toward it.

This generalizes naturally: walk **n steps** forward, accumulate the discounted rewards along the way, then add the remaining value estimate — that's **n-step TD**. Push n to infinity and you walk the trajectory *all the way to the end*: that is **Monte Carlo (MC)**, which completes the entire journey and needs no value estimate at all.

The trade-off along this spectrum: **MC has high variance and converges slowly, but it is unbiased.** One-step TD sits at the other extreme (low variance, fast, but biased through bootstrapping).

### The critic in actor-critic vs. DQN

They look like the same network — both take a state and output action values — but they estimate different things:

- **DQN** approximates the **optimal** action-value function, which is independent of any particular policy. That is why DQN can be **off-policy**.
- The **value network (critic)** in actor-critic estimates the value **under the current policy** — not the optimal value. Because it is tied to the policy, it can only be **on-policy**.

---

## Part 3: The Algorithm Landscape

The single cleanest split in RL: **model-based methods have an explicit model of the environment; model-free methods do not.**

### Model-based methods

When the transition and reward model is known, the BOE can be solved directly.

**Value iteration** — starts from an initial *value*. Each round alternates:

- *policy update*: pick the greedy policy for the current values;
- *value update*: compute V_{k+1} from the Bellman equation — exactly **one step** of the recursion, no inner loop.

**Policy iteration** — starts from an initial *policy*. Each round alternates:

- *policy evaluation (PE)*: solve for the value of the current policy. This is itself an **iteration nested inside the outer iteration**, run (in principle) infinitely many steps until V converges. A subtle point: the intermediate quantities in this inner loop are just iterates converging toward the state value, not state values themselves — so the inner recursion is "based on" the Bellman equation rather than being the Bellman equation itself.
- *policy improvement*: make the policy greedy with respect to the evaluated values.

**Truncated policy iteration** — the compromise: run the inner value-solving iteration for only a finite number of steps *j*. Value iteration (j = 1) and policy iteration (j = ∞) become the two extremes of the same algorithm family.

### Model-free methods

No model? Then estimate values from data. There are two big families — estimate values and derive a policy (**value-based**), or optimize the policy directly (**policy-based**) — plus Monte Carlo, which underlies both.

---

#### Monte Carlo methods

The core idea: **MC approximates expectations by averaging samples.** Run episodes, average the observed returns.

**MC-basic.** Take policy iteration and replace its policy-evaluation step: instead of first solving for V and then converting to action values (that conversion needs a model!), *directly* estimate the action value of each (s, a) by averaging returns of episodes. Because the algorithm is policy iteration with one step swapped out, its **convergence is inherited** from policy iteration.

**MC exploring starts.** To estimate every (s, a), every (s, a) must appear in the data — so this variant requires that *every state–action pair can be the start of an episode*. A strong and often impractical requirement.

**MC ε-greedy.** Replace the greedy policy with an ε-greedy one. Three points to internalize:

1. It **trades optimality for exploration**: giving non-optimal actions some probability keeps exploration alive, at the cost of the policy no longer being exactly optimal.
2. It is a **balance between the previous two algorithms**. The final policy it produces may coincide with the greedy one, but measured by state values, optimality decreases (larger V means better optimality).
3. When episodes are **long enough**, every state–action pair gets visited *without* requiring exploring starts — which is exactly the computational saving that makes the method practical.

In practice, keep **ε small** so the learned policy stays *consistent* — i.e., it matches the policy you would get under the optimal (greedy) setting, even though the ε-greedy method itself is not exactly optimal.

---

#### Value-based learning: temporal difference

**The Robbins–Monro (RM) algorithm** is the mathematical engine underneath TD: it solves for the root of an equation *without knowing the model*, using noisy samples. Stochastic gradient descent (SGD) is a special case of RM. The chain GD → batch GD → SGD is a chain of increasing randomness: the more samples per update, the less randomness, and vice versa.

TD methods come in a family of variants:

**Variant 1 — TD for state values.** Estimates the state value *under a given policy*. It can only do policy evaluation — it cannot estimate action values or find the optimal policy by itself. The **TD error** contains a difference between predictions at *two adjacent time frames* — that is where the "temporal" in temporal-difference comes from.

**Variant 2 — TD as RM on the Bellman equation.** The same algorithm, understood as Robbins–Monro applied to the Bellman expectation equation: V_π(s) = E[R + γ·V_π(S′) | S = s] for each state s.

**Variant 3 — Sarsa.** On-policy TD for *action* values. Its update uses the quintuple **(s_t, a_t, r_t, s_{t+1}, a_{t+1})** — which spells "Sarsa". Because a_{t+1} is drawn from the current policy, Sarsa's estimate *depends on the current policy*. Plugging it into the usual loop — use Sarsa for policy evaluation (updating action values), then do policy improvement — yields the full control algorithm. The deep-learning version replaces the table with a network: the TD target defines a loss function, and the gradient of that loss updates the network.

**Variant 4 — Expected Sarsa.** Replace the sampled next action with an expectation over the policy's action distribution — less randomness in the target.

**Variant 5 — n-step Sarsa.** Walk n steps before bootstrapping: a blend of MC and Sarsa, interpolating between the two ends of the spectrum from Part 2.

Each variant corresponds to a Bellman-style equation it is implicitly solving — lining them up against their equations is a genuinely useful exercise.

---

#### Q-learning and DQN

**Q-learning** targets the **optimal action value directly** — not the value of any particular policy. Because the target does not depend on the policy, Q-learning can be run **off-policy** (or on-policy).

**The overestimation problem.** Q-learning systematically overestimates values, for two compounding reasons:

1. **Bootstrapping propagates bias**: an inflated estimate gets baked into the next target, and the error spreads.
2. **The max operator** in the target introduces overestimation that is *non-uniform across actions* — which is what makes it harmful for action selection.

Two main remedies:

- **DQN with a target network.** Keep a separate, slowly-updated copy of the network to compute targets; both the *selection* of the maximizing action and its *evaluation* use the target network. But since the target network's parameters are still derived from the DQN itself, bootstrapping is only mitigated, not fully severed.
- **Double Q-learning.** Split the two roles: **select** the action with the DQN (parameters w), but **evaluate** it with the target network (parameters w⁻). Why does this reduce overestimation? The action a⁻ chosen by the target network is *the* maximizer of Q under w⁻. The action a\* chosen by the DQN, whatever it is, can score **no higher than that maximum** when evaluated under w⁻. So the resulting target y_t is systematically smaller — a built-in correction pushing against overestimation.

**DQN itself, briefly.** The network Q(s, a; w) takes a state and outputs one value per action. Because the output layer enumerates actions, DQN only works for **discrete action spaces**. Training uses TD: the essence of TD is the *correction between the previous prediction and the next one* — and the new prediction is more trustworthy because it contains one step of real, observed reward.

Two architectural tricks that improve training:

- **Dueling network.** Decompose the action value into a state-value part and an advantage part, each with its own network stream. Intuitively, the network can learn *which states are valuable or worthless* without having to learn the effect of every action in every state.
- **Noisy nets.** Add Gaussian noise to the network's *parameters* — each weight is replaced by a learned mean and standard deviation, so the parameter count doubles. A noisy DQN is inherently stochastic, which **encourages exploration** (playing the same role as ε-greedy), and learning under noise also makes the model more **robust to perturbations**.

---

#### Policy-based learning

Instead of learning values and deriving a policy, learn the **policy network** directly: input a state, output a probability distribution over actions. (The encoder depends on whether the state is a 1-D vector or a 2-D image.)

**The objective function.** The state value V_π(s) is influenced by two things: the state and the policy — a good state and a good policy both make it large. But the objective for *policy* learning must isolate the policy, so we **take an expectation over states** to remove the state's influence: J(θ) = E_S[V_π(S)]. This is an extremely common trick in RL: *eliminate a variable's influence by taking an expectation over it* — and MC can always simulate that expectation with samples.

**The policy gradient theorem** gives the gradient of J(θ) in a form that can be estimated from samples (the proof is in Section 7.3.1 of Shusen Wang's book, referenced at the end). In practice, the gradient's conditional expectations are approximated with Monte Carlo.

**REINFORCE.** The most direct policy-gradient method: perform **gradient ascent** — step in the direction that increases the return. Approximate the action value in the gradient with the *observed return u_t* of the trajectory.

Is REINFORCE on-policy? Yes — and here is the way to see it: although parameters keep changing, each action *a* and each return *u_t* were generated *under the current policy before the update*; after the update, a fresh round is collected. As long as those two stay matched, it is on-policy. The consequence: **every collected trajectory is used exactly once** — reuse it after the policy changes and the method is no longer on-policy.

**Actor-critic.** Add a **value network (critic)** with the same structure as DQN: input state, output action values. But remember the Part 2 insight: DQN estimates the *optimal* value function (policy-independent, hence off-policy), while the critic estimates the value *under the given policy* (hence on-policy, and not optimal). The critic's actual job: **estimate the value (the return) from real-time rewards**, so the actor gets feedback without waiting for episodes to end.

**Baselines.** Subtracting a baseline *b* from the action value in the policy gradient **does not change the gradient**, as long as b is independent of the action A (because the gradient takes an expectation over A). But it can dramatically reduce variance. Two algorithms build on this:

- **REINFORCE with baseline.** Q is still simulated by MC (the observed return), and the baseline is a learned state value V. Careful: *this is not actor-critic*. Q comes from MC, not from a value network; and the V network, though a value network, plays the role of a **baseline**, not a critic.
- **A2C (advantage actor-critic).** The agent is controlled by the policy network π, which interacts with the environment and collects states, actions, rewards. The policy network (the **actor**) takes action a_t based on s_t. The value network (the **critic**) computes the TD error δ_t from s_t, s_{t+1}, and r_t. The actor then uses δ_t to judge how good its action was and improves its "acting" (the parameters θ) accordingly.

  A2C comes in two flavors:
  - **On-policy A2C**: the baseline (zero in plain QAC) is approximated by V_π(s). The algorithm balances exploration and exploitation — a balance you can see reflected in the numerator and denominator of the step size.
  - **Off-policy A2C**: uses **importance sampling** to bridge the gap between the behavior policy and the target policy, allowing data collected by one to train the other.

**PPO (Proximal Policy Optimization).** The core move: take the policy gradient **from on-policy to off-policy**, for one very practical reason — **to reuse collected data for multiple training updates**. Three problems arise on the way, each with its fix:

1. *Different policies produce different action distributions*, so actions sampled from the old policy have the wrong "importance" for the new one. **Fix: importance sampling** — reweight each sample by the ratio of the two policies' probabilities.
2. *Are the expectations under the two policies really the same?* With f(x) sampled under p(x) and the reweighted version under q(x), the math shows both give **unbiased estimates** of the same expectation. Better yet, with a well-chosen sampling distribution, importance sampling can even *reduce variance*: outcomes that are rare but strongly affect the expectation can be made more likely to be sampled.
3. *How do we make sure the two policies don't drift too far apart?* (If they do, the importance weights blow up.) Two fixes: a **KL-divergence penalty term** in the objective, or **clipping** the importance ratio.

  Relation to TRPO: TRPO also constructs a surrogate objective, but its "stay close" constraint is *external* (a hard constraint on the optimization); PPO instead **writes the constraint directly into the objective**. Implementation-wise, PPO runs two roles: one actor **collects data** and produces trajectories; the other is **trained** on that data with gradient ascent.

**TRPO (Trust Region Policy Optimization).** The trust-region idea comes from numerical optimization, where it is used for non-convex problems. In a neighborhood of the current parameters θ_now, we can *trust* a surrogate function L(θ | θ_now) to stand in for the true objective J(θ). Two key points: the surrogate is close to J only **locally**, near θ_now — not globally; and because L is *constructed by us* (e.g., via MC estimates), its properties are well understood, which is exactly what makes maximizing it tractable. The loop is: **approximate** (build the surrogate near the current point) → **maximize** (solve the constrained maximization with a numerical method) → repeat until the algorithm **converges**.

  The trust region itself can take two shapes: a **ball** (simple, easy to solve, mediocre performance) or a **KL-divergence region** (better matched to probability distributions).

  TRPO's two core ingredients: an **equivalent form of the objective function** that exposes the policy ratio, and the approximation of Q by **u_t, the observed MC return of the trajectory**. Its tunable hyperparameters: the trust-region **radius Δ**, and the **learning rate** of the numerical solver used for the constrained maximization.

**Entropy regularization.** Entropy measures the *uncertainty* of a probability distribution. The motivation: we don't want the policy's output probabilities to become frozen and collapse onto a few actions — that kills exploration. So we ask that the entropy of the policy's output distribution *not be too small*, by adding entropy to the objective as a **regularization term**. The caveat: encouraging exploration by increasing decision uncertainty has a real risk — **very bad actions may also keep nonzero probability**.

---

#### From discrete to continuous control

Everything above with a discrete action head breaks down when actions are continuous (torques, steering angles). Two families handle this.

**DPG (Deterministic Policy Gradient).** Built on actor-critic, with one fundamental change: the policy network **outputs the action directly**, rather than a probability distribution over actions — the policy goes from stochastic to **deterministic**. The value network's role: *during training*, the critic evaluates the actor and guides its learning; *after training*, the critic is thrown away and the actor performs alone.

DPG inherits the **overestimation problem**, again from two sources: the **maximization** in the target, and **bootstrapping** propagating the bias. Two families of remedies:

- **Target networks** — partial mitigation, same caveat as in DQN.
- **Clipped double Q-learning** — because the TD target takes a **min** over two target critics, it counteracts overestimation more effectively than plain target networks.

Two further refinements make this practical:

1. **Add truncated-normal noise to the target action.** Noise smooths the value estimate, and truncating the distribution prevents the noise from ever being too large.
2. **Delay the policy and target-network updates.** The core idea: *while the critic is still poor, don't rush to let it coach the actor.* Traditional actor-critic updates policy, value, and target networks every round. It works better to update the **value network every round**, but the **policy network and the three target networks only every k rounds** — k is a hyperparameter to tune.

Put clipped double Q-learning together with these two tricks and you get **TD3 — Twin Delayed Deep Deterministic Policy Gradient**.

**Stochastic Gaussian policies.** The alternative: keep the policy stochastic, but make it a continuous distribution.

- **1-D action space**: assume the action follows a Gaussian; learn the **mean and variance**, then sample the action from that Gaussian.
- **n-D action space**: use an **auxiliary network** that works together with the policy network to produce the parameters of the multivariate distribution.

Both networks are trained with the **policy gradient**, and the Q inside the gradient can be estimated in either of the two ways we already know: **REINFORCE** (Monte Carlo returns) or **actor-critic** (a learned critic).

---

## Closing thoughts

If you zoom all the way out, the map looks like this:

- **Values** answer "how good?" — for a state (V), for a state–action pair (Q), or in the best possible world (Q\*).
- **Bellman equations** tie those values together recursively; with a model you solve them (value/policy iteration), without one you estimate them from data (MC, TD, Q-learning).
- **Policies** can be learned indirectly from values (value-based) or directly by gradient ascent (policy-based), with actor-critic combining both.
- Almost every "advanced" algorithm — double DQN, A2C, PPO, TD3 — is a targeted fix for one specific pathology: overestimation, variance, data reuse, or premature determinism.

Keep asking two questions about any algorithm you meet: *what expectation is it estimating?* and *does that quantity depend on the policy that collected the data?* Those two questions alone will place it correctly on this map.

**Sources & further reading.** These notes were distilled mainly from Shusen Wang's *Deep Reinforcement Learning* (the openly available PDF; the policy gradient theorem proof is in §7.3.1) and Shiyu Zhao's *Mathematical Foundations of Reinforcement Learning* course, whose treatment of the BOE, value/policy iteration, and the MC family shaped Part 3. Any misunderstandings are, of course, my own.
