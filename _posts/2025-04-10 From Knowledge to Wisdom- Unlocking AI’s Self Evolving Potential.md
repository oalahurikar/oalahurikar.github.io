---
tags:
  - ai
comments: true
---
[[2024-09-26-AI-state-and-future]]

> What’s stopping AI from thinking like us—making cross‑domain connections, asking its own questions, and evolving its understanding despite having access to all the world’s knowledge?

**Today’s AI**
- A large language model is trained once to minimise a static loss function (next‑token prediction, RL‑HF reward, etc.). Once deployed, the weights are frozen; at inference time the model has **no internal drive to reduce fresh uncertainty, seek novelty, or set new goals**.
- Even agent wrappers such as AutoGPT eventually run into planning loops or stall because the underlying model is still “loss‑frozen.” 

**Brains**
- Dopamine, noradrenaline and other neuromodulators continuously tune plasticity and behaviour in response to novelty, reward prediction error, social signals, threat, fatigue, etc. 
- This cocktail creates _intrinsic motivation_ (curiosity, play, boredom‑avoidance) that pushes us to ask new questions and build ever‑richer world models. 

Without a comparable, always‑on intrinsic‑motivation system, current AI will not spontaneously “wonder” about things the way we do.

### **Continual, plastic learning vs. frozen weights**
Humans add synapses, prune others, and consolidate memories every night; the knowledge base is _never_ locked.

Most production LLMs cannot update their weights online (doing so would risk catastrophic forgetting and huge compute bills). They can keep a short‑term “scratchpad” (the context window) but lack a reliable, long‑term, editable memory. This makes cross‑domain connections fragile or transient. 

Research frontiers: vector‑database‑augmented models, retrieval‑augmented generation, and “self‑improving” techniques such as SeaLong show early steps toward safe online updates, but they are still laboratory‑grade.

### **World‑model depth and embodiment**
Humans build multi‑modal, grounded models of physics, social dynamics, and their own bodies. LLMs are largely trained on _text alone_, so their “world model” is a statistical shadow of lived reality.

Projects such as Yann LeCun’s **Joint Embedding Predictive Architecture ([JEPA](https://www.youtube.com/watch?v=ETZfkkv6V7Y))** aim to learn rich predictive representations from video, audio, proprioception and text together, but they are early‑stage.

### **Metacognition and self‑critique**
We notice when we’re confused and launch a deliberate search for better information. LLMs can simulate self‑reflection only when explicitly prompted (“think step‑by‑step”), and even then they can fall into “over‑thinking” loops that degrade accuracy. The Ember framework released this week tries to fix that by letting multiple specialised models check one another, but it is orchestration, not genuine metacognitive drive.

### **Energy, architecture and scale**
- **Brain:** ~20 W, 86 billion neurons, massive recurrence, sparse activation, local learning rules, lifelong plasticity.
- **LLM stack:** megawatts in training, billions of _parameters_ but little recurrence, dense matrix multiplies, back‑prop only during offline training.

The architectural gulf means we still lack an engineering recipe that combines the efficiency, adaptability, and self‑repair you get “for free” in biology.

## **What is** missing?

| **Layer**         | **Missing piece today**                | **Active research directions**                                            |
| ----------------- | -------------------------------------- | ------------------------------------------------------------------------- |
| **Motivation**    | Intrinsic curiosity, task generation   | RL‑based curiosity, self‑reward, empowerment                              |
| **Learning**      | Safe, online, non‑catastrophic updates | Parameter‑efficient finetuning, neural “patches,” memory‑augmented models |
| **Memory**        | Stable, writable long‑term store       | Hierarchical memory architectures, external vector DBs                    |
| **World model**   | Multimodal, causal grounding           | JEPA‑style predictive learning, robotics + language fusion                |
| **Metacognition** | Reliable self‑critique & goal repair   | Multi‑agent oversight, tool‑use planning, reflection chains               |
| **Hardware**      | Energy‑efficient, plastic substrates   | Neuromorphic chips, spiking NNs, analog compute                           |
When these layers mature and interlock, AI systems will be able to _set their own questions_, gather data to answer them, revise their internal models, and do so continually—something we call “autonomous, self‑evolving intelligence.” We are making measurable progress, but we are still assembling the motivational engine, the lifelong memory, and the robust world model that the human brain comes with by default.