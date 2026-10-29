# 🤖 OptiSweep AI: Autonomous Task Scheduling & Grid Navigation

![Python](https://img.shields.io/badge/Python-3.8%2B-blue?style=for-the-badge&logo=python)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=for-the-badge&logo=jupyter)
![Matplotlib](https://img.shields.io/badge/Visualizer-Matplotlib-11557c?style=for-the-badge)
![Status](https://img.shields.io/badge/Build-Passing-brightgreen?style=for-the-badge)

> **What happens when an autonomous robot has a list of places to visit, strict rules on *when* to visit them, and walls blocking the way?**  
> **OptiSweep AI** is a from-scratch Python simulation built to explore how classical Artificial Intelligence solves this exact problem by splitting it into two distinct "brains": a **Constraint Satisfaction (CSP) Scheduler** and a **Graph-Search Navigator**.

---

## 💡 Motivation: Why We Built This

When learning about autonomous agents and warehouse/service robotics, one of the coolest architectural patterns is **decoupling *what* to do from *how* to get there**:

1. **High-Level Task Planning ("In what order should I work?"):** Real-world tasks rarely happen in a random order. Certain zones have priority deadlines, while others depend on prior tasks being finished first. Trying every permutation by brute force scales terribly ($O(N!)$). We wanted to implement a **Backtracking CSP Solver** with smart heuristics to prune impossible schedules instantly.
2. **Low-Level Motion Planning ("How do I move without hitting walls?"):** Once a valid schedule exists, the agent needs to physically navigate a 2D grid from its charging dock, visit each target in the exact scheduled sequence, and return home safely. We built both **Uninformed (BFS)** and **Informed (A\*)** search engines to visualize and measure how much computation a good heuristic actually saves.

---

## 🏗️ System Architecture

The simulation operates as a two-stage pipeline driven entirely by external configuration files—no hardcoded maps or schedules:

```text
  ┌──────────────────────┐               ┌──────────────────────┐
  │     Schedule.txt     │               │   Environment.txt    │
  │ (Deadlines & Orders) │               │ (Grid, Walls, Tasks) │
  └──────────┬───────────┘               └──────────┬───────────┘
             │                                      │
             ▼                                      ▼
  ┌──────────────────────┐               ┌──────────────────────┐
  │   BRAIN 1: CSP       │               │  BRAIN 2: Navigator  │
  │  • MRV + Degree      │  Ordered      │  • A* (Manhattan)    │
  │  • LCV Ordering      │ ───────────►  │  • Breadth-First     │
  │  • Forward Checking  │  Task Queue   │  • Obstacle Avoidance│
  └──────────────────────┘               └──────────┬───────────┘
                                                    │
                                                    ▼
                                         ┌──────────────────────┐
                                         │  Simulation Engine   │
                                         │  • Full Trajectory   │
                                         │  • Cost & Node Stats │
                                         │  • 2D Grid Plots     │
                                         └──────────────────────┘
