---
date: '2025-03-30T21:58:26+08:00'
description: "Mamba-2 状态空间模型 原理"
draft: true
title: 'Mamba 2 状态空间模型1'
tags: [Chinese, AI]
categories: AI
---




[[Paper](https://arxiv.org/abs/2405.21060)]
[[Code](https://github.com/state-spaces/mamba)]

**原理部分** [[Mamba-2 状态空间模型1](/posts/mamba-2状态空间模型1)]


**术语约定**

在本文中，我们将使用以下符号来表示不同的变量和概念：

- **$h_t$**：表示在时间步$t$的隐藏状态，它是一个$\mathbf{N}$维向量。
- **$x_t$**：表示在时间步$t$的输入信号，它是一个标量。
- **$y_t$**：表示在时间步$t$的输出信号，它也是一个标量。
- **$A_t$**：表示状态转移矩阵，它决定了隐藏状态如何从时间步$t-1$转移到时间步$t$。
- **$B_t$**：表示输入控制矩阵，它决定了输入信号如何影响隐藏状态。
- **$C_t$**：表示输出观测矩阵，它决定了如何从隐藏状态计算出输出信号。
- **$\mathbf{N}$**：表示状态大小或状态维度，是一个独立的超参数。
- **$\mathrm{T}$**：表示序列的总长度或时间步的总数。




### The Linear (SSM) Mode

SSD与Mamba都采用相同的状态空间方程:

$$
\begin{aligned}
h_{t} &= A_t h_{t-1} + B_t x_t \\
y_t &= C_t^{\top} h_t
\end{aligned}
$$



To recap, a **structured state space model (SSM)**  $x \in \mathbb{R}^\mathtt{T} \to y \in \mathbb{R}^\mathtt{T}$.
Think of $x_t$ and $y_t$ as being scalars, and the hidden state $h_t$ as an $\mathtt{N}$-dimensional vector, where $\mathtt{N}$ is an independent hyperparameter called the *state size, state dimension, or state expansion factor*.







SSD的优势在于，它通过在训练和推理阶段都使用矩阵乘法来显著加速了训练过程。

The benefits of the SSD algorithm is that it preserves the same efficient FLOP counts as SSMs (compared to quadratic attention),
and also dramatically speeds up training compared to general state space models by utilizing matmuls.

|                         | Attention                | SSM             | SSD                |
| -------------           | -----------              | ----            | ---                |
| 状态维度              | $\mathrm{T}$             | $\mathbf{N}$    | $\mathbf{N}$       |
| 训练 FLOPs          | $\mathrm{T}^2\mathrm{N}$ | $\mathbf{TN^2}$ | $\mathbf{TN^2}$    |
| 推理 FLOPs         | $\mathrm{T}\mathrm{N}$   | $\mathbf{N^2}$  | $\mathbf{N^2}$     |
| 内存占用          | $\mathrm{T}^2$           | $\mathrm{TN}^2$ | $\mathbf{TN}$      |
| 是否采用矩阵乘法 | &check;                  | &cross;         | &check;            |
|                         |                          |                 |                    |



## Mamba-2架构

