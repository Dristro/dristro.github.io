---
layout: post
title: "PureTorch: A Deep Learning Framework from Scratch"
date: 2025-04-25
categories: [deep-learning, puretorch]
excerpt: "A breakdown of how I’m building PureTorch — a deep learning framework from scratch using a compute graph-based autograd system."
---

Over the last few months, I've been working on a small but ambitious project: **PureTorch** — a minimal deep learning framework written from scratch in Python. The goal? To better understand how frameworks like PyTorch work under the hood.

This post covers the motivation behind PureTorch, the architectural evolution from hardcoded gradients to a dynamic computation graph, and key insights I gained along the way.

> **🔍 What is PureTorch?**  
> A minimal deep learning framework built in Python, designed to help you understand how autograd and backpropagation work under the hood. Think of it as your own micrograd, with evolving features and growing insight.


---

## Motivation

While I’ve used PyTorch extensively, I often felt disconnected from what happened internally — especially when calling `.backward()`. I knew *what* it did — but not *how*.

So I started small.

The first iteration of PureTorch involved manually implementing layers and writing the backward passes for each one. This meant explicitly defining the gradient equations for operations like matrix multiplication, activation functions, and so on.

It worked, but it quickly became tedious and rigid. Adding a new operation meant writing custom derivative logic. Debugging gradient flows was difficult and error-prone. The codebase also wasn’t scalable, especially as network complexity increased.

I needed a better abstraction — one that aligned with how real-world frameworks track computation and automate differentiation.

---

## Toward a Better Abstraction

The breakthrough came while researching how to structure an automatic differentiation system. I already understood that PyTorch builds neural networks as dynamic computation graphs — but I wasn’t sure how to implement something similar in code.

That’s when I came across [Karpathy’s micrograd](https://github.com/karpathy/micrograd), a beautifully minimal implementation of reverse-mode automatic differentiation. It demonstrated exactly what I needed: a practical and elegant way to construct and traverse a computation graph using overloaded operators and local gradients.

With that insight, I was able to integrate an autograd system into PureTorch — one that built the computation graph during the forward pass, and handled backpropagation automatically using graph traversal and local gradients.

---

## Architecture Overview

### PureTorch: the start (v0)
The first version of PureTorch was fully manual — each layer had its own `.forward()` and `.backward()` methods, and gradients were calculated explicitly for each operation. For example, the Linear layer computed:

```python
dx = dout @ W.T  
dW = x.T @ dout  
db = sum(dout)
```

This approach worked, but had obvious limits. It was verbose, error-prone, and hard to scale. Every new operation or layer meant rewriting gradient logic from scratch.

As I researched how to make this cleaner and closer to how real frameworks work, I focused on **how PyTorch builds and traverses computation graphs**. I understood the concept — dynamic graph construction and reverse-mode differentiation — but wasn’t sure how to structure it in code.

That’s when I came across [Karpathy’s micrograd](https://github.com/karpathy/micrograd), which demonstrated a minimal way to create and traverse a computation graph. It clicked — the idea of overloading operations and storing parent references made the whole graph-based approach suddenly feel approachable.

With that insight, I was able to restructure PureTorch around a `Tensor` class that could implicitly build the graph during the forward pass, and then use `.backward()` to walk it in reverse.

### PureTorch Now (v1)
**From Manual Gradients to a Compute Graph**

With that clarity, I rewrote the core of PureTorch using a `Tensor` class. Now, instead of manually chaining gradient logic, operations like `+`, `*`, and `@` return new `Tensor` objects that implicitly build a computation graph.

Each node in the graph stores:
- A reference to its parent(s)
- The operation that created it
- A method to compute the local gradient
- The accumulated `.grad` value

During the forward pass, the graph is constructed automatically. When `.backward()` is called on the final loss tensor, it walks the graph in reverse, computing gradients as it goes — much like PyTorch’s autograd.

Example usage:
```python
z = x @ W + b
a = relu(z)
loss = mse(a, target)
loss.backward()
```

Each value (`z`, `a`, `loss`) is part of the graph, and gradients are backpropagated without any manual wiring.

This change made PureTorch significantly more modular and extensible — and much closer to the kind of framework I initially set out to build.

---

## Current Status
PureTorch is still a work in progress. The core autograd system is functional but not fully optimized. Broadcasting support is partial, and some operations don't yet handle gradient accumulation correctly. There’s no GPU support, no optimizers module, and the training interface is still evolving.

That said, the current version can train small MLPs on toy datasets, and it has served its core purpose — to demystify what happens behind high-level APIs.

## Key Takeaways
Reverse-mode autograd is surprisingly intuitive once you think of operations as nodes in a graph.

Writing Tensor and operation overloads helps develop a deeper understanding of PyTorch's design.

Bugs in gradient propagation often come from incorrect assumptions about shapes or graph traversal — logging gradients step-by-step is invaluable.

Even a partial implementation gives you enough insight to teach others and experiment freely.

## What's Next
- Add gradient checking utilities to validate gradients numerically.
- Finish implementing broadcasting and reduce operations (e.g., sum, mean).
- Build a simple training loop for MNIST or TinyImageNet.
- Experiment with adding optimizers and a basic module API (à la nn.Module).

**Have ideas, feedback, or contributions?**  
Feel free to open an issue or pull request on the [GitHub repo](https://github.com/Dristro/PureTorch).

---

## Final Thoughts
PureTorch isn't meant to compete with real-world frameworks. It’s messy, unfinished, and full of gaps. But it’s mine — and it taught me more about deep learning internals than any textbook could.

If you’re a student, researcher, or just curious — I highly recommend building your own framework. Start with micrograd. Write bad code. Break things. And learn by rebuilding the tools you use every day.

So don’t just use the tools — **try rebuilding them**. You’ll be surprised how much you learn when you build deep learning from the ground up.

---

## Part of the PureTorch Series

This post kicks off the **PureTorch** series — a hands-on, from-scratch deep learning framework built in Python to demystify autograd, computation graphs, and backpropagation.

It’s also part of a larger upcoming series: **From Zero to Deep Learning Internals**, where I break down everything from basic neural nets to writing your own training loop.

Coming soon:
- "What Even *Is* a Neural Net?"
- "Backprop by Hand"
- "Building Your Own Autograd Engine"
- "Transformers from First Principles"

Follow along to build your understanding from the ground up — and see how deep learning really works under the hood.

---

If you found this helpful or interesting, let me know!  
You can [reach out](mailto:dhruvn853@gmail.com), or follow my [GitHub](https://github.com/Dristro) and [LinkedIn](https://www.linkedin.com/in/dhruvn7) for updates.
