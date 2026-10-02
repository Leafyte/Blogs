# How We Went From "I Think RL Is Cool" to a Research Paper (And How You Can Too)

A few months ago, my teammates and I at VVCE Mysuru had no research experience beyond reading papers and building side projects. Now we have a paper on deep reinforcement learning for game NPCs. This post is the honest version of how we did it: no magic, just a process you can copy.

[Research paper link](https://ieeexplore.ieee.org/document/11497042)

## Step 1: Start with a problem that annoys you

We didn't start with "let's write a paper." We started with a question: *why are NPCs in games so predictable?*

Most games use finite state machines (FSMs): patrol, pursue, attack, retreat. They're cheap and reliable, but once you learn the pattern, you can exploit it forever. That annoyance became our research question: **can a learned NPC (via deep RL) be less predictable and more engaging than a scripted one, and how do we even measure that?**

Tip: a good research question is narrow enough to test and annoying enough that you'll stay interested for months.

## Step 2: Read before you build (but read with a purpose)

We didn't read randomly. We split the reading into buckets:

- **Foundations:** Sutton & Barto for MDPs, returns, and the Bellman equation.
- **Algorithms:** the DQN paper, the PPO paper, SAC, TD3.
- **Game AI milestones:** Atari, AlphaGo, AlphaStar, OpenAI Five.
- **Evaluation:** how people judge game AI beyond "did it win?"

For every paper, we wrote three lines: *what problem it solves, what it assumes, where it breaks.* That habit made the literature review almost write itself.

The big gap we found: most RL work measures **reward and win rate**, but a game NPC that wins 100% of the time is a terrible NPC. Players want challenge, variety, and fairness. That gap became our contribution.

## Step 3: Define your contribution in one sentence

Ours was: *a player-centric evaluation framework (strategy entropy, engagement duration, win-rate balance) plus a PPO case study showing it works against an FSM baseline.*

If you can't say your contribution in one sentence, you don't have one yet. Keep narrowing until you can.

## Step 4: Build a small, controlled experiment

You don't need a AAA game or a GPU cluster. We built a simple environment:

- 50×50 grid combat arena
- 11×11 partial observability (the agent can't see everything, like real games)
- 8 actions: move in four directions, attack, dodge, defend, feint
- Episodes end on death or after 500 steps

The agent: **PPO via Stable Baselines3**, two 128-unit tanh MLPs, 4 parallel environments, 100k timesteps. Key hyperparameters: learning rate 3e-4, clip range 0.2, GAE λ = 0.95, entropy coefficient 0.01.

The baseline: a rule-based FSM (patrol, pursue, attack, retreat).

Lesson: **small and reproducible beats big and impressive.** Anyone can rerun our setup on a laptop.

## Step 5: Design your reward function like it's your hypothesis

This was the most important part. We shaped the reward to push behavior in the direction of our metrics:

- Dodge and feint bonuses → encourage **diversity**
- Small living penalty → discourage passive stalling, encourage **engagement**
- Health-difference bonus → encourage **balance**

Reward design *is* your research idea in code. Change it and you change the behavior.

## Step 6: Choose metrics before you see results

We picked three metrics up front:

1. **Strategy entropy:** how varied is the NPC's behavior?
2. **Engagement duration:** how long do fights last?
3. **Win-rate balance:** is the NPC fair (near 50%) instead of unbeatable or useless?

And we combined them into one score:

**Engagement Index = w1·H(π) + w2·T_engage + w3·(1 − |WR − 0.5|)**

Choosing metrics *before* looking at results keeps you honest. Otherwise it's too tempting to pick whichever number looks best.

## Step 7: Run ablations (this is what makes it research)

Anyone can say "my method works." Ablations show *why*. We turned things off one at a time:

- No entropy regularization → strategic diversity dropped sharply
- No time penalty → the agent learned to stall
- GAE λ = 0 → much higher variance
- Single environment instead of parallel → training was far slower

Each ablation tells the reader which part of your system actually matters.

## Step 8: Evaluate against a baseline, with statistics

We ran 100 evaluation episodes of PPO vs FSM and compared:

| Metric | PPO | FSM |
|---|---|---|
| Strategy entropy | 2.1 ± 0.3 | 0.8 ± 0.2 |
| Engagement (min) | 7.3 ± 1.2 | 3.1 ± 0.9 |
| Win rate (%) | 49.2 ± 4.1 | 68.9 ± 5.6 |

We also ran a small survey with 35 students. They rated the PPO NPC as more unpredictable, more fun, and more motivating to keep playing.

Numbers alone don't convince people. Numbers plus humans do.

## Step 9: Write the paper in this order

Don't write top to bottom. We wrote in this order:

1. Methodology and results (you already know what you did)
2. Theoretical foundations
3. Related work / applications overview
4. Evaluation framework
5. Conclusion and future directions
6. Introduction and abstract **last**, because only now do you know what you're actually claiming

Use the IEEE template from day one. Fighting formatting at the end is miserable.

## Things I'd do differently (learn from our mistakes)

Being honest is part of doing research, so here's what I'd tighten up:

- **Include training curves.** We skipped them to focus on final behavior, but reviewers like seeing reward and entropy over time. It proves training was stable.
- **Keep the setup description consistent.** Describe the action space and who the opponent is (scripted FSM vs human player) the same way everywhere. Small inconsistencies make readers doubt the rest.
- **Be precise with statistics.** State exactly which test you ran and what was paired with what. Comparing two different agents' episodes isn't the same as a true paired design.
- **Cite your own results as yours.** If your ablation numbers come from your experiments, don't attach citations to other papers for them. Cite others for background, and report your own findings as your own.
- **Scale the survey.** 35 students is a good start; more participants and a mix of gamers and non-gamers would be stronger.

## The short version

1. Pick a problem that annoys you.
2. Read with a purpose and find the gap.
3. State your contribution in one sentence.
4. Build a small, reproducible experiment.
5. Treat reward design as your hypothesis.
6. Pick metrics before results.
7. Run ablations.
8. Compare to a baseline with proper statistics, and ask real humans.
9. Write the intro last.
10. Be honest about limitations.

Research isn't about being a genius. It's about asking a clear question, running a clean experiment, and being honest about what you found. If a group of 4th-semester CSE students can do it, so can you.

*Built by Karthik M at VVCE, Mysuru. Questions? Reach out on LinkedIn or GitHub (leafyte).*
