---
layout: post
title: "When Your Noise Isn't Gaussian"
subtitle: "An Unscented Kalman Filter, and the lesson it taught me about honesty"
date: 2026-05-01
tags:
  - controls
  - sensor-fusion
  - kalman-filter
  - ukf
  - signal-processing
---

Years ago, early in my career, I was working on torque measurements for a dynamometer test stand — the kind of rig that spins drivetrain components at controlled speeds to characterize their losses. The job sounded simple: take a torque reading at one end of a shaft, take another at the wheel, subtract them, and you've got the friction loss inside the bearings.

The data refused to cooperate.

## The Noise Lied in Two Ways at Once

Sensor noise I expected. Every torque transducer has it; you average and move on. But when I plotted the loss histogram, it wasn't a clean bell curve. It was **multi-modal** — distinct humps at specific RPM ranges where the bearing dynamics misbehaved. Misalignment, imbalance, overturning moment — each one carved its own bump into the distribution.

A standard Kalman filter assumes Gaussian noise. Mine wasn't even close.

## Why the Obvious Upgrade Didn't Help

The textbook answer when your *process* is nonlinear is the **Extended Kalman Filter**: linearize around the current estimate, do the standard update. Easy.

Except an EKF needs a clean Jacobian — the partial derivatives of the nonlinear function. And bearing friction, with its mess of mechanical sources, doesn't hand one over. You can compute *something*, but linearizing around the wrong operating point makes the filter confidently wrong, which is worse than being noisy.

## The Right Tool: Don't Linearize, Sample

The **Unscented Kalman Filter** flips the problem. Instead of approximating the nonlinear function with a derivative, pick a small set of carefully chosen points around your current estimate — **sigma points** — push each one through the actual nonlinear function, then recompute the mean and covariance from the transformed cloud.

No derivatives. No linearization error. The filter learns the shape of the nonlinearity by sampling it.

For a one-dimensional state, that's three points: the mean and one on either side. Cheap. The math is from Julier and Uhlmann; the intuition is "if you can't compute the slope, just look at where things land."

## The Result, and the Honest Asterisk

I derived the process model from first principles — Newton's second law for rotating bodies, balancing measured torque against inertial effects and bearing losses — fed it into the UKF, and the output distribution tightened up nicely. The multi-modal mess collapsed toward something Gaussian-ish with materially smaller variance.

Here's where I have to be honest with myself, fifteen years later:

**Any smoother reduces variance on noisy data.** A moving average would have done it. A low-pass filter would have done it. The interesting question — *did the median estimate converge to the true bearing loss?* — I never definitively answered, because I didn't have an independent ground truth to check against. "Tighter σ" is not the same thing as "correct."

That's the trap of clever filters: they produce confident-looking output, and confidence can hide the fact that you never validated against truth.

## What I'd Do Differently Today

Three things, and they're worth saying out loud:

**Benchmark against a particle filter.** When the data is genuinely non-Gaussian and multi-modal, a particle filter doesn't force everything back into a Gaussian box. It costs more compute, but it tells you a more honest story about what the distribution actually looks like.

**Tune sigma-point spread by sensitivity analysis, not by tutorial.** I picked the UKF parameters from a textbook example. They worked. But "worked" without a sensitivity sweep is just "didn't obviously fail" — different territory from "optimal."

**Validate against an independent signal.** A coast-down test, a synthetic dataset with known truth, anything. Without it, the filter's confidence interval is a story the filter is telling itself.

## The Takeaway

The math of the UKF is beautiful, and choosing it over an EKF for a problem with intractable Jacobians was the right call. But the deeper lesson wasn't the algorithm — it was about **being suspicious of my own outputs**. A filter that produces a tighter distribution feels like progress. Sometimes it is. Sometimes it's just a smoother making peace with noise it never understood.

The hardest discipline in engineering isn't picking the right tool. It's resisting the relief you feel when the tool gives you a clean-looking answer.
