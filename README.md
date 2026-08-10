# TLMS-using-deep-RL
hey all..!
this repo is all about optimizing the traffic flow by ppo(an policy based method) escpecially in the peek hours of the day..
main features:
1) single agent based (upon using multiple instances , throughput improves).
2) prioritizing the emergency vechiles.

DEMO :
### PPO Agent (Left) vs Static Fixed-Time Traffic Signal (Right)

<img width="800" height="450" alt="ay41g0" src="https://github.com/user-attachments/assets/2210cfd0-3219-45f9-90b9-10df9b42136a" />




# Comparison Table 

| Metric                 | Static Fixed-Time Signal Control | PPO-Based Adaptive Signal Control |   Improvement |
| ---------------------- | -------------------------------: | --------------------------------: | ------------: |
| Vehicle trips          |                            1,400 |                             1,400 |          Same |
| Simulation horizon     |                          3,800 s |                           3,800 s |          Same |
| Average trip time      |                         173.66 s |                      **152.43 s** | **12.224% ↓** |
| Total trip time        |                     243,124.00 s |                  **213,404.52 s** | **11.34% ↓** |
| Average waiting time   |                          63.27 s |                       **53.71 s** |  **~14% ↓**   |
| Total waiting time     |                      88,578.00 s |                   **76,169.08 s** |  **~14% ↓**   | 


# RL-Based Adaptive Traffic Signal Controller

A reinforcement learning–based traffic signal controller that dynamically adjusts signal phase durations according to real-time traffic conditions simulated in **SUMO**.

## Problem

Fixed-time traffic signals use predefined timings regardless of traffic demand. This can cause unnecessary vehicle waiting, long queues, and inefficient use of green phases.

This project uses **PPO (Proximal Policy Optimization)** to learn a traffic-control policy that adapts signal timing based on the current state of an intersection.

## Architecture

```text
SUMO Traffic Simulation
        │
      TraCI
        │
        ▼
   MySumoEnv
        │
        ▼
 Traffic State
        │
        ▼
 Curiosity + Exploration
        │
        ▼
      PPO Agent
        │
        ▼
  Signal Phase Duration
        │
        ▼
   SUMO Simulation
        │
        └────── feedback ──────►
```

## State

The agent observes normalized:

* Queue length
* Waiting time
* Throughput
* Travel time
* Current traffic-light phase

## Action

The PPO policy outputs values in `[-1, 1]`, which are scaled into signal phase durations between **10 and 60 seconds**.

A yellow phase is inserted between green phases.

## Reward

The reward combines:

* lower queue length
* lower waiting time
* higher throughput
* lower travel time
* emergency-vehicle handling

An additional curiosity reward encourages exploration of novel/unfamiliar states.

## RL Pipeline

* **SUMO** — traffic simulation
* **TraCI** — Python ↔ SUMO communication
* **Gymnasium** — custom RL environment
* **Stable-Baselines3** — PPO implementation
* **PyTorch** — neural-network computation
* **TensorBoard** — training monitoring

## Training

The current implementation trains a **single PPO agent on a SUMO intersection environment**.

The learned policy is intended to be reusable: after training, instances of the same policy can independently control multiple intersections with compatible state/action spaces.

The current design does **not** implement multi-agent coordination between intersections.



## Project Structure

The notebook contains:

```text
                    ┌──────────────────┐
                    │   SUMO Simulator │
                    │ Vehicles/Traffic │
                    └────────┬─────────┘
                             │
                           TraCI
                             │
                             ▼
                    ┌──────────────────┐
                    │   MySumoEnv      │
                    │  Gymnasium Env   │
                    └────────┬─────────┘
                             │
                       Collect State
                             │
                             ▼
          ┌────────────────────────────────────┐
          │ State                              │
          │ • Queue length                     │
          │ • Waiting time                     │
          │ • Throughput                       │
          │ • Travel time                      │
          │ • Current signal phase (one-hot)   │
          └────────────────┬───────────────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │ Exploration Layer   │
                │ Dynamic Action Noise│
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │    PPO MLP Policy   │
                │     RL Agent        │
                └──────────┬──────────┘
                           │
                  Continuous Action
                       [-1, 1]
                           │
                           ▼
                ┌─────────────────────┐
                │ Action Processing   │
                │ Scale → 10–60 sec   │
                │ + Yellow Phase      │
                │ + Emergency Check   │
                └──────────┬──────────┘
                           │
                           ▼
                    Traffic Signal
                           │
                           ▼
                         SUMO
                           │
                    Simulation Step
                           │
                           ▼
                    New Traffic State
                           │
                           ├──────────────► Reward
                           │                  │
                           │                  ▼
                           │          Traffic Reward
                           │          + Curiosity Reward
                           │
                           ▼                  │
                    PPO Learning ◄────────────┘
                           │
                           └────── repeat
```

