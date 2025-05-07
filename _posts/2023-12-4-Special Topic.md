---
layout: post
title:  "Distinct Count"
date: 2023-12-4
categories: Course
---

This is a Special topic for EECS 376

# Distinct Count

## Background
Say, $\{a_1, \cdots a_M\}$ are a set of IPs. They are sent to a router, and now the router wants to figure out how many distinct IPs. Let $n$ be the number of distinct IPs. If using a hashmap, it may take $O(n)$ space to implement. 

We can allow $n$ within a range: 
$$ Pr[\hat{n} \in \{ (1-\epsilon)n, (1+\epsilon)n \}] < \delta $$

Call it the "$(\epsilon, \delta)$" guarantee. 

We are aiming for a space of $O(\epsilon^{-2} \log{\frac{1}{\delta}})$.

## Aim 
- Find an unbiased estimator (Random Hash Function).
- Reduce the variance (Apply Chebychev).
- Reduce the error probability (Apply Chernoff-Hoeffding).

In general, a Random Hash Function is like: $h(IP address) \rightarrow [0, 1]$. $h(a)$ is uniformly in the interval of [0, 1] and is independent. 

## Min Sketch Method
We are using the "Min sketch": $Y = min(h(a_1), \cdots, h(a_M))$. 

The reason is that $Y$ is good as it filters out duplicates of $a_i$. **The distribution of $Y$ is only affected by the number of distinct elements in $\{a_1, \cdots a_M\}$. **

### Intuitive Guess
This process is equivalent to getting the smallest among $n$ random values between $[0, 1]$.Intuitively, this result is $\frac{1}{n+1}$, because we can take $n = 1$ and the result is $\frac{1}{2}$. 

### Mathematical Proof
$$E(Y) = \int_{0}^{1} Pr(Y\geq x) \, dx = \int_{0}^{1} Pr(Y\geq x) \, dx= \int_{0}^{1} (1-x)^n \, dx$$

Therefore the expectation of $Y$, 
$$E(Y) = \int_{0}^{1} x^n \, dx = \frac{1}{n+1}$$

The estimation of $\hat{n}$: $\hat{n} = \frac{1}{Y} - 1$. 

But this expected value of $\hat{n}$ is infinite as $E\left[\frac{1}{Y}\right] \rightarrow \infty$. To prove this, we make a partition between $[0, 1]$

|  $\cdots$  |  $\frac{1}{8}$  |  $\frac{1}{4}$  | $\frac{1}{2}$  |

In reality, 
$$E\left[\frac{1}{Y}\right] \geq \frac{1}{2}\times 1 + \frac{1}{4}\times 2 + \frac{1}{8} \times 4 + \cdots$$

## Multiple Hash Functions

We use $k$ hash functions and take its mean to approximate this $Y$, i.e., $Z = \frac{Y_1 + Y_2 + \cdots Y_k}{k}$. 

The expectation of $Z$ is equal to that of $Y$, i.e, $E(Z) = \frac{1}{n+1}$, and its variance is reduced. 

### Variance of $Z$
Remember that,
$$Var(Y) = E(Y^2) - E(Y)^2$$
$$Var(X+Y) = Var(X) + Var(Y)$$
$$Var(c) = c^2 Var(X)$$

Therefore the variance of $Z$, 
$$Var(Z) = \frac{1}{k^2} \sum Var(Y_i) = \frac{1}{k} Var(Y_i)$$

Then we calculate the variance of $Y_i$, denote as $Y$.
$$E(Y^2) = \int_{0}^{1} Pr(Y^2\geq x) \, dx = \int_{0}^{1} Pr(Y\geq \sqrt{x}) \, dx= \int_{0}^{1} (1-\sqrt{x})^n \, dx$$

Let $u = 1 - \sqrt{x}$, then $x = (1-u)^2$, $dx = -2(1-u) du$.

$$E(Y^2) = \int_{1}^{0} -2(1-u)u^n \, du = 2\int_{0}^{1} (1-u)u^n \, du = \frac{2}{(n+1)(n+2)}$$

$$Var(Y) = E(Y^2) - E(Y)^2 = \frac{2}{(n+1)(n+2)} - \frac{1}{(n+1)^2} \leq \frac{1}{(n+1)^2}$$

Therefore we get the variance of $Z$, 
$$Var(Z) = \frac{Var(Y)}{k} \leq \frac{1}{k(n+1)^2}$$

### Chebychev Inequality
Using Chebychev, 
$$Pr(|Z - \frac{1}{n+1}| \geq \frac{\epsilon}{n+1}) \leq \frac{Var(Z)}{(\frac{\epsilon}{n+1})^2} \leq \frac{1}{k\epsilon^2}$$

Picking $\frac{1}{k\epsilon^2} \leq \frac{1}{3}$ (Randomly, just a small value), then $k = \frac{3}{\epsilon^2}$.

$Pr(\frac{1}{Z} - 1\textit{ is a good estimate}) \geq \frac{2}{3}$.

We can pick $k$ larger, but it is not practical, because we are paying for the accuracy, $k = \frac{\delta}{\epsilon^2}$. 

### Improvements
We take multiple $Z$ and get their median: 
$$W = median(Z_1, \cdots Z_k)$$
> median is more robust than simply $Z$

As long as good estimates is more than half, we are happy with that result. 

Define $I_i = 1$ when $\frac{1}{Z} - 1$ is a good estimator. 

$E[I_i] \geq \frac{2}{3}$, $X = I_1 + \cdots + I_l$, then $E(X) \geq \frac{2l}{3}$. 

$Pr(X \leq \frac{l}{2}) = Pr(\frac{X}{l} \leq \frac{2}{3} - \frac{1}{6}) \leq e^{-2 \frac{1}{6^2} l}$

This is a logarithmic approximation. 

The Space (in bits): $3 \epsilon^{-2} \log{\ln{\frac{1}{3}}} \cdot 64$bits.