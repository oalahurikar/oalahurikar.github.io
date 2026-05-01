---
layout: post
title: "Teaching a Car to Think 750 ms Ahead"
subtitle: "Model Predictive Control under 100 ms of latency"
date: 2026-05-01
tags:
  - ai
  - project
  - self-driving
  - mpc
  - controls
---

> **Code:** [github.com/oalahurikar/CarND-MPC-Project](https://github.com/oalahurikar/CarND-MPC-Project)

## The Problem

Drive a simulated car around a track at 60 mph. Sounds simple — until the simulator adds a cruel twist: every steering command you send is delayed by **100 ms** before the car actually responds. By the time your control lands, the world has moved on.

A reactive controller — "I see error, I correct error" — fails. It chases ghosts. The car wobbles, oversteers, and eventually leaves the road.

## The Idea

Stop reacting. Start **predicting**.

Instead of asking *"what should I do right now?"*, the car asks *"what should I do over the next 750 ms?"* — solves for the entire steering and throttle plan, executes only the first step, then re-plans on the next tick.

This is **Model Predictive Control**. The car carries a tiny physics model of itself in its head and runs an optimizer 20 times a second to find the smoothest path forward.

## Why It Works Under Latency

The horizon is **750 ms**. The latency is **100 ms**. The plan is already looking 7× past the delay window — by the time the command lands, the car is still doing exactly what it intended.

The trick that holds it all together: a single weight in the cost function. Penalize *change* in steering 600× harder than anything else. The optimizer stops jerking the wheel and starts drawing smooth arcs. Smooth plans survive delay; jerky ones don't.

## The Stack

C++ with Ipopt (solver), CppAD (auto-diff), Eigen (linear algebra), uWebSockets (simulator bridge). 118-variable nonlinear program, solved every 50 ms.

## The Result

Full track. Curves and straights. 60 mph. 100 ms latency. No off-road excursions.

## What I Took Away

The real lesson wasn't the math — it was that **smoothness is a control strategy**. One weight, tuned an order of magnitude higher than the rest, did more for stability than any clever state estimation would have. Sometimes the best way to handle a hard problem is to make the answer boring.
