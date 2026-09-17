# ZoumMax

**ZoumMax** is a Monte Carlo Tree Search variant designed for **simultaneous-move, multi-agent decision-making problems**, particularly in robotics and other environments where several independent agents must choose actions at the same time.

Instead of constructing a single search tree over the full joint action space, ZoumMax maintains a **decoupled search tree for each agent** and explores them in synchronized depth.

The goal is to reduce the combinatorial explosion that appears in conventional joint-action MCTS when the number of agents increases.

## Motivation

Consider a system with:

* \(N\) agents
* \(b\) possible actions per agent

A traditional joint-action search may need to consider up to:

$$
b^N
$$

joint actions at a single decision step.

For multi-robot systems, this can quickly become computationally expensive.

ZoumMax approaches the problem differently by maintaining independent search structures for each agent while keeping their simulated decisions synchronized.

Conceptually:

```text
Traditional Joint MCTS

                State
                  |
      -------------------------
      |       |       |       |
     a1a1    a1a2    a2a1    ...
     
Branching grows approximately as b^N
```

ZoumMax:

```text
Agent 1 Tree        Agent 2 Tree        Agent N Tree

    S                   S                   S
   /|\                 /|\                 /|\
  A B C               A B C               A B C
   |                   |                   |
 depth 1             depth 1             depth 1
   |                   |                   |
 depth 2             depth 2             depth 2

             synchronized descent
```

Depth \(k\) in every tree represents the same simulated decision step.

## Core Idea

ZoumMax uses:

* one MCTS tree per agent
* synchronized tree traversal
* simultaneous action selection
* depth-bounded simulations
* per-agent statistics
* joint environment transitions
* reward propagation back into the corresponding agent trees

At every simulated timestep, each agent chooses an action from its own search tree.

The selected actions are combined into a joint action:

$$
A_t = (a_t^1, a_t^2, ..., a_t^N)
$$

The environment then evaluates the resulting transition:

$$
S_{t+1} = T(S_t, A_t)
$$

Each agent receives its corresponding reward:

$$
R_t = (r_t^1, r_t^2, ..., r_t^N)
$$

These rewards are then backpropagated through the individual search trees.

## High-Level Algorithm

```text
initialize one search tree for each agent

while computation budget remains:

    state = current environment state

    for depth = 0 ... max_depth:

        for each agent:
            select an action using its own tree policy

        combine selected actions

        apply joint action to the environment

        observe:
            next state
            rewards for every agent

        expand agent trees when necessary

    evaluate resulting simulation

    for each agent:
        backpropagate its reward through its tree
```

## Why Decouple the Trees?

The main idea is to avoid explicitly representing every possible joint action as an independent branch.

Traditional joint-action branching can grow approximately as:

$$
O(b^N)
$$

whereas the decoupled representation operates over approximately:

$$
O(Nb)
$$

local action choices at each synchronized level.

This does not mean the interaction between agents disappears.

Agents still interact through the **shared environment transition**, but their search statistics are represented separately.

## Target Applications

ZoumMax is intended for environments such as:

* multi-robot coordination
* autonomous robot fleets
* simultaneous navigation
* decentralized decision-making
* cooperative robotics
* competitive multi-agent systems
* general-sum games
* multi-agent simulation
* real-time control systems

## Example

Suppose four robots each have five possible actions.

A full joint-action representation may contain:

$$
5^4 = 625
$$

possible combinations at a single state.

With a decoupled representation, each robot maintains five local action branches:

$$
4 \times 5 = 20
$$

local branches across the four trees.

The joint interaction is evaluated when the independently selected actions are executed together in the simulated environment.

## Name

The name **ZoumMax** comes from two ideas.

First, it combines my name, **Zouhair**, with the naming convention of algorithms such as **Minimax**.

Second, the name reflects the algorithm's behavior: it attempts to **zoom deeply into individual agents' decisions** while maintaining synchronized reasoning across the multi-agent system.

## Research Article

A technical introduction to ZoumMax was published by **The AI Journal**:

### ZoumMax: Rethinking Monte Carlo Tree Search for Simultaneous Multi-Agent Robotics

https://aijourn.com/zoummax-rethinking-monte-carlo-tree-search-for-simultaneous-multi-agent-robotics/

The article discusses the motivation behind the algorithm, simultaneous multi-agent decision making, and the limitations of conventional joint-action MCTS approaches.

## Research Status

ZoumMax is currently an experimental research algorithm.

Current and future work includes:

* benchmarking against standard UCT/MCTS
* comparison with joint-action MCTS
* comparison with existing decoupled MCTS approaches
* scalability tests with increasing numbers of agents
* branching-factor experiments
* computational latency measurements
* memory-usage analysis
* ablation studies
* multi-robot simulation experiments
* real-world robotic control experiments

## Planned Evaluation

Important metrics include:

| Metric           | Description                           |
| ---------------- | ------------------------------------- |
| Decision latency | Time required to choose actions       |
| Nodes explored   | Number of search nodes evaluated      |
| Memory usage     | Search-tree memory consumption        |
| Agent count      | Scalability as \(N\) increases        |
| Branching factor | Scalability as action spaces increase |
| Reward           | Performance achieved by each agent    |
| Success rate     | Task completion rate                  |
| Search depth     | Effective planning horizon            |

## Implementation

The repository will contain the reference implementation of ZoumMax together with experimental environments and benchmarks.

Planned structure:

```text
zoumax/
├── src/
│   ├── agent.py
│   ├── tree.py
│   ├── node.py
│   ├── selection.py
│   ├── simulation.py
│   └── zoumax.py
│
├── environments/
│   ├── multi_robot/
│   └── games/
│
├── experiments/
│   ├── scalability/
│   ├── benchmarks/
│   └── ablations/
│
├── tests/
│
├── examples/
│
└── README.md
```

## Citation

If you use ZoumMax in research, experiments, or derivative work, please cite the project and associated publication.

```bibtex
@misc{ouddach2026zoumax,
  author = {Zouhair Ouddach},
  title = {ZoumMax: Rethinking Monte Carlo Tree Search for Simultaneous Multi-Agent Robotics},
  year = {2026},
  url = {https://aijourn.com/zoummax-rethinking-monte-carlo-tree-search-for-simultaneous-multi-agent-robotics/}
}
```

An academic preprint and formal citation will be added as the research progresses.

## Author

**Zouhair Ouddach**

Software Engineer and creator of ZoumMax.

## License

This project is licensed under the Apache License 2.0.

Copyright © 2026 Zouhair Ouddach

See the [LICENSE](LICENSE) file for details.
