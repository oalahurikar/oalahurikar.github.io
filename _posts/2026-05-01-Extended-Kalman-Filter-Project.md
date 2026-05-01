---
layout: post
title: "Tracking a Pedestrian From Two Liars"
subtitle: "Sensor fusion with an Extended Kalman Filter"
date: 2026-05-01
tags:
  - ai
  - project
  - self-driving
  - kalman-filter
  - sensor-fusion
  - controls
---

> **Code:** [github.com/oalahurikar/Extended-Kalman-Filter-Project](https://github.com/oalahurikar/Extended-Kalman-Filter-Project)

## The Problem

A pedestrian walks in front of a car. Two sensors watch them: a **lidar** that says *"they are here, in (x, y)"*, and a **radar** that says *"they are this far away, at this angle, moving at this speed"*.

Both sensors are noisy. Both lie a little. Lidar gives clean position but tells you nothing about speed. Radar gives speed but its position is fuzzy. Take either one alone and you'll either lose track of where the pedestrian is, or fail to predict where they're about to be.

The car needs **one answer**: position *and* velocity, accurate enough to brake in time.

## The Idea

You don't pick a winner. You **fuse** them.

A Kalman filter is the math equivalent of weighing two opinions by how much you trust each one. It keeps a running estimate of the pedestrian's state — *and* an estimate of how confident it is in that state. Every new measurement nudges the estimate by an amount proportional to *(sensor reliability) ÷ (current confidence)*.

Trust the sensor more when your own estimate is uncertain. Trust your estimate more when the sensor is noisy. The filter does this calculation automatically, every time a new measurement arrives.

## The Twist — Radar Speaks a Different Language

Lidar gives `(x, y)` — same coordinates as the state. Easy.

Radar gives `(range, bearing, range-rate)` — polar coordinates. The relationship between *what radar measures* and *what the filter tracks* is **nonlinear** (involves `√`, `atan`, and division). A standard Kalman filter can't handle that.

That's where **Extended** comes in. At each radar update, compute a **Jacobian** — the linear approximation of the nonlinear measurement function around the current estimate. Use that to do one step of the Kalman update. Re-linearize on the next measurement. Done.

## A Design Choice Worth Calling Out

The initial state covariance:

```
position uncertainty  = 1
velocity uncertainty  = 1000
```

Translation: *"I'm confident about where the pedestrian is right now (the first measurement told me). I have no idea how fast they're moving. Don't anchor on velocity — let the data teach you."*

Within a handful of measurements, the filter has learned the velocity from successive position deltas, and the 1000s collapse to something reasonable. The trick is letting the filter know what it doesn't know.

## The Stack

C++ with Eigen for linear algebra. State is `[px, py, vx, vy]`. Process noise tuned at 9 m²/s⁴ per axis (constant-velocity model with acceleration as noise). Timestamps arrive in microseconds — divide by 1e6 before use, or the filter dies silently.

## The Result

Udacity's rubric required RMSE under `[0.11, 0.11, 0.52, 0.52]` for `[px, py, vx, vy]`. The filter cleared it comfortably across the test dataset.

## What I Took Away

The math is beautiful, but the real insight is philosophical: **certainty has to be earned, not assumed**. Initialize position with confidence because you measured it. Initialize velocity with humility because you didn't. The filter only works when its prior beliefs match what it actually knows — and the same is probably true of most things that try to predict the future.
