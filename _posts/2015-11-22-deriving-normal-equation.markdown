---
title:  "Deriving the Normal Equation"
date:   2015-11-22
---

Consider a linear model

$$
X\vec{\theta} = \vec{y}
$$

where

$$
X = \left( \begin{array}{ccc}
x_{1,1} & \dots & x_{1,n} \\
\vdots & \ddots & \vdots \\
x_{m,1} & \dots & x_{m,n} \end{array} \right)
$$

is a matrix of real numbers where $m$ is the number of samples (or rows), and $n$ is the number of features (or columns),

$$
\vec{\theta} = \left( \begin{array}{c}
\theta_1 \\
\vdots \\
\theta_m
\end{array} \right)
$$

is a matrix (also called a _vector_) of coefficients $\theta_i$, and

$$
\vec{y} = \left( \begin{array}{c}
y_1 \\
\vdots \\
y_m
\end{array} \right)
$$

is a matrix of target variables $y_i$ per ith sample.

The normal equation is a matrix equation that allows you to solve $\vec{\theta}$ above. By doing so, you can construct a linear regression model that allows you to predict the value of the target variable for any new samples. It is given by

$$
\vec{\theta} = (X^T X)^{-1} X^T \vec{y}.
$$

The goal of the normal equation is to provide you with $\vec{\theta}$ so that the cost function

$$
J(\vec{\theta}) = \frac{1}{2}(X\vec{\theta} - \vec{y})^T(X\vec{\theta} - \vec{y})
$$

is minimized.

You can find derivations of the normal equation on the internet, but most of them include a lot of handwaving and skip seemingly trivial, but actually tedious and crucial steps. I will derive it in this article.

# Minimizing $J(\vec{\theta})$ Using Vector Calculus

Recall back in calculus that to minimize a function, you simply take its derivative and set it to zero. We will do the same for the cost function. But first, we need to borrow some concepts from vector calculus.

The derivative of a vector is called a gradient, and is denoted by the symbol $\nabla$. We subscript the symbol $\nabla$ with some variable to denote that we're taking a vector's derivative with respect to that variable. Thus, the derivative with respect to $\theta$ is simply $\nabla_\theta$.

Minimizing the cost function means taking its derivative with respect to $\theta$ and setting the result to zero:

$$
\nabla_\theta J(\vec{\theta}) = 0.
$$

We will use this equation to derive the normal equation.

# Some Useful Theorems

In order to proceed with the derivation, we're gonna need the help of some theorems. You can find these as well in any proper vector calculus textbook. Let $A$, $B$, and $C$ be matrices. The two theorems we will use are the following:

$$
\begin{align}
\nabla_{A^T} f(A) &= (\nabla_A f(A))^T \\
\nabla_A \text{tr}(ABA^TC) &= CAB + C^TAB^T
\end{align}
$$

where the _trace_ $\text{tr}A$ of a matrix $A$ is just the sum of its diagonal elements.

If we set the matrix $C$ as the identity matrix in the second theorem above, we simply get

$$
\nabla_A \text{tr}(ABA^T) = AB + AB^T.
$$

By setting $D = ABA^T$, we get

$$
\text{tr}(ABA^T) = \text{tr}(D) = f(D)
$$

due to the fact that the trace of a matrix is a function $f$ that maps from $\mathbb{R}^{m \times n}$ to $\mathbb{R}$. Taking the transpose of the last equation and noting that $(AB)^T = B^T A^T$, we get

$$
\begin{align}
[ \nabla_A f(D) &= AB + AB^T ]^T \\
(\nabla_{A} f(D))^T &= B^T A^T + BA^T
\end{align}
$$

But the left-hand side of the equation is just the first theorem. Thus, we have

$$
\nabla_{A^T} f(D) = B^T A^T + BA^T
$$

We can finally proceed with our derivation.

# All Together Now

Let's take the gradient of $J$ with respect to $\theta$

$$
\begin{align}
\nabla_\theta J(\vec{\theta}) &= \nabla_\theta \left(\frac{1}{2}(X\vec{\theta} - \vec{y})^T(X\vec{\theta} - \vec{y})\right) \\
&= \frac{1}{2} \nabla_\theta \left( \vec{\theta}^T X^T X \vec{\theta} - \vec{\theta}^T X^T \vec{y} - \vec{y}^T X \vec{\theta} + \vec{y}^T \vec{y} \right)
\end{align}
$$

The term $\vec{y}^T \vec{y}$ on the right side is a real number. This indicates that the whole term in the parentheses is some function that maps to a real number. If we use the fact that the trace of a real number is just itself, and that $\text{tr}(A) = \text{tr}(A^T)$, we get

$$
\begin{align}
\nabla_\theta J(\vec{\theta}) &= \frac{1}{2} \nabla_\theta \text{tr} \left( \vec{\theta}^T X^T X \vec{\theta} - \vec{\theta}^T X^T \vec{y} - \vec{y}^T X \vec{\theta} + \vec{y}^T \vec{y} \right) \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - \text{tr}(\vec{y}^T X \vec{\theta}) - \text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right] \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - \text{tr}(\vec{y}^T X \vec{\theta})^T - \text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right] \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - \text{tr}(\vec{\theta}^T X^T \vec{y}) - \text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right] \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - 2\text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right].
\end{align}
$$

The term $\text{tr}(\vec{y}^T \vec{y})$ has vanished because it's treated as a constant when we're taking the gradient with respect to $\theta$. Thus, we are now left with

$$
\nabla_\theta J(\vec{\theta}) = \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - 2\text{tr}(\vec{\theta}^T X^T \vec{y}) \right].
$$

If use the derived theorem above $\nabla_{A^T} f(D) = B^T A^T + BA^T$ and substitute as follows,

$$
\begin{align}
A^T &= \vec{\theta} \\
B &= X^TX
\end{align}
$$

we should get

$$
\begin{align}
\nabla_\theta J(\vec{\theta}) &= \frac{1}{2} \left(X^T X \vec{\theta} + X^T X \vec{\theta} - 2 X^T \vec{y} \right) \\
&= X^T X \vec{\theta} - X^T \vec{y}
\end{align}
$$

Since $\nabla_\theta J(\vec{\theta}) = 0$, we finally have

$$
X^T X \vec{\theta} - X^T \vec{y} = 0
$$

which is the normal equation. How cool is that! :)




