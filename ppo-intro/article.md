> This article is a conceptual introduction to Proximal Policy Optimization (PPO), one of the most widely used reinforcement learning algorithms. It is not a guide on implementation. The goal is to understand what PPO is, why it exists, and how its key ideas fit together.

```video
src: https://www.youtube.com/watch?v=eJgHia54Yrg&t=2s
caption: Our original video on PPO, where we trained an agent to survive a zombie apocalypse
```

Reinforcement learning is a way of teaching a computer to make decisions by letting it try things and learn from the results. Rather than being given a set of correct answers, the agent figures out what to do on its own through experience.

PPO, which stands for :vocab[Proximal Policy Optimization]{definition="A reinforcement learning algorithm that trains an agent by updating its decision-making strategy in small, controlled steps to avoid erasing what it has already learned."}, is one of the most popular algorithms for doing this. It is widely used because it is stable, reliable, and works well across a huge range of problems.

## The Basic Setup

Before getting into PPO itself, it helps to understand the core pieces of any reinforcement learning problem.

An :vocab[agent]{definition="The learner or decision-maker in a reinforcement learning system. The agent observes the world, takes actions, and learns from feedback."} is the thing doing the learning. It exists inside an :vocab[environment]{definition="The world the agent operates in. The environment responds to the agent's actions and gives back new information and rewards."}, which is everything around it that it can interact with.

At any given moment, the agent observes the current :vocab[state]{definition="A snapshot of the situation the agent finds itself in. For example, its position, what is nearby, and how much health it has."} of the environment. Based on that state, it chooses an action. The environment then responds with a new state and a :vocab[reward]{definition="A numerical signal that tells the agent whether its last action was good or bad. Positive rewards encourage a behavior, negative rewards discourage it."}.

The agent's goal is to take actions that lead to the highest total reward over time.

```quickcheck
q: What is the purpose of a reward in reinforcement learning?
options:
  - To describe the current state of the environment
  - To tell the agent whether its action was good or bad
  - To set the value of K
  - To define the number of training steps
answer: 1
explanation: Rewards are the feedback signal the agent uses to learn. A positive reward means the action was helpful, a negative reward means it was not.
```

## The Policy

The agent makes decisions using something called a :vocab[policy]{definition="The strategy the agent uses to decide what action to take in any given state. It maps states to actions."}.

Think of a policy as a rulebook. Given the current state of the world, the policy says what the agent should do. In PPO, and most modern reinforcement learning, the policy is represented by a :vocab[neural network]{definition="A type of machine learning model loosely inspired by the brain. It takes in numbers representing the current state and outputs a decision or probability."}.

The neural network takes in numbers that describe the current state, and it outputs either a specific action or a set of probabilities for each possible action. The agent then picks based on those probabilities.

```image
src: policy-network.png
alt: A diagram of a neural network. On the left, input nodes labeled with state information like position and nearby threats. On the right, output nodes labeled with possible actions like move up, move down, move left, move right. Arrows connect through hidden layers in the middle.
caption: The policy network takes in state information and outputs probabilities for each possible action.
fit: contain
```

Training the agent means updating the weights inside the neural network so that good actions become more likely over time.

## Why Training is Tricky

Updating the policy sounds simple enough, but there is a real risk: if you update the policy too aggressively after a batch of experience, you can accidentally make it much worse and undo everything the agent has learned.

The agent is learning from its own behavior. If the policy changes too much in one step, the experience it just collected is no longer a reliable guide, and the whole training process can collapse.

```quickcheck
q: What is the danger of updating a policy too aggressively?
options:
  - The agent collects too many rewards
  - The training becomes faster than expected
  - The policy can get much worse and erase previous learning
  - The neural network grows too large
answer: 2
explanation: Large policy updates can destabilize training. If the policy changes too much at once, earlier experience stops being relevant and the agent can forget what it already learned.
```

## How PPO Solves This

PPO's core idea is simple: update the policy, but not too much at once.

It does this using a technique called :vocab[clipping]{definition="A mathematical constraint in PPO that limits how much the policy is allowed to change in a single update step, keeping the new policy close to the old one."}. When PPO calculates how much to update the network, it clips the update if it tries to go too far. The policy is only allowed to move within a small range of where it already is.

This means every training step improves things gradually rather than risking a dramatic change that breaks what is already working.

```image
src: clipping.png
alt: A graph showing the PPO objective function. There are two lines, one clipped and one unclipped. The clipped line flattens out once it reaches the allowed boundary, while the unclipped line keeps rising. A shaded region shows the allowed range of update sizes.
caption: Clipping puts a ceiling on how much a single update can change the policy, keeping training stable.
fit: contain
```

This is the "proximal" in Proximal Policy Optimization. The word proximal means close or nearby. PPO keeps the new policy close to the old one at every step.

## Exploration vs Exploitation

A well-trained agent should mostly do things it knows work well. But if it only ever repeats the same moves, it might miss better strategies it has never tried.

This tension is called the :vocab[exploration vs exploitation tradeoff]{definition="The balance between trying new actions to discover better strategies (exploration) and sticking with actions that are already known to work (exploitation)."}. Too much exploitation and the agent gets stuck. Too much exploration and it never settles on anything reliable.

PPO handles this by adding an :vocab[entropy bonus]{definition="A small reward added during training to encourage the agent to keep some randomness in its decisions, preventing it from committing too early to a narrow set of actions."} to the training objective. Entropy is a measure of randomness. By rewarding a bit of randomness, PPO nudges the agent to keep exploring rather than always doing the same thing.


```quickcheck
q: What does the entropy bonus in PPO encourage the agent to do?
options:
  - Update the policy more aggressively
  - Ignore rewards during early training
  - Keep some randomness in its decisions so it keeps exploring
  - Reduce the size of the neural network
answer: 2
explanation: The entropy bonus rewards the agent for maintaining variety in its actions, which prevents it from locking in on a suboptimal strategy too early.
```

## Valuing Future Rewards

Not all rewards are equal. A reward received right now is more reliable than one that might come later after many more steps. PPO uses a :vocab[discount factor]{definition="A number between 0 and 1 that reduces the value of future rewards. A reward 10 steps away is worth less than the same reward right now."} to reflect this.

A discount factor close to 1 means the agent cares a lot about the future. A discount factor closer to 0 means it focuses mostly on immediate rewards. Most PPO setups use a value somewhere around 0.99, meaning the agent plans ahead but slightly prioritizes what is closer in time.

## Measuring How Good an Action Was

To update the policy, PPO needs to know not just whether the agent got a reward, but how much better or worse a specific action was compared to what the agent would have done on average. This is measured using something called the :vocab[advantage]{definition="A measure of how much better or worse an action turned out to be compared to what was expected. A positive advantage means the action did better than average."}.

Calculating the advantage cleanly is harder than it sounds because rewards are noisy and delayed. PPO uses a technique called :vocab[Generalized Advantage Estimation]{definition="A method for calculating the advantage that balances accuracy and noise. It smooths out the feedback signal so the agent can learn more reliably."} (GAE) to produce a cleaner, more stable signal.

```image
src: advantage.png
alt: A timeline of an agent taking actions and receiving rewards. Some actions have a positive advantage shown in green, others a negative advantage shown in red. A baseline reward level is shown as a horizontal dotted line for comparison.
caption: The advantage tells the agent which of its actions were better than expected and which were worse.
fit: contain
```

## Putting It All Together

PPO combines all of these ideas into a single training loop:

1. The agent interacts with the environment for a while, collecting experience.
2. PPO calculates the advantage of each action taken.
3. It updates the policy to make good actions more likely, using clipping to keep the update small.
4. The entropy bonus keeps some randomness to encourage exploration.
5. The whole process repeats from step one with the updated policy.

Over many thousands or millions of steps, this loop produces an agent that has learned a reliable strategy through nothing but trial, feedback, and careful updating.

## Conclusion

Proximal Policy Optimization works by putting guardrails on the learning process. The agent is free to learn from experience, but PPO makes sure each update is small enough not to undo previous progress. Clipping keeps the policy stable, the entropy bonus keeps the agent curious, discounting helps it think ahead, and GAE makes the feedback signal cleaner.

PPO is not the only reinforcement learning algorithm, but it is one of the most widely used because this combination of ideas makes it robust and practical across a wide range of tasks.

> We are working on an in-depth explanation about PPO where implementation will be covered!
