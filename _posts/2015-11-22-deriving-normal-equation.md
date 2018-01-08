---
layout: post
title:  "Deriving the Normal Equation"
date:   2015-11-22
---

Consider a linear model

{% math %}
X\vec{\theta} = \vec{y}
{% endmath %}

where

{% math %}
X = \left( \begin{array}{ccc}
x_{1,1} & \dots & x_{1,n} \\
\vdots & \ddots & \vdots \\
x_{m,1} & \dots & x_{m,n} \end{array} \right)
{% endmath %}

is a matrix of real numbers with {%m%}m{%em%} as the number of samples (or rows), and {%m%}n{%em%} is the number of features (or columns),

{% math %}
\vec{\theta} = \left( \begin{array}{c}
\theta_1 \\
\vdots \\
\theta_m
\end{array} \right)
{% endmath %}

is a matrix (also called a _vector_) of coefficients {%m%}\theta_i{%em%}, and

{% math %}
\vec{y} = \left( \begin{array}{c}
y_1 \\
\vdots \\
y_m
\end{array} \right)
{% endmath %}

is a matrix of target variables {%m%}y_i{%em%} per ith sample. <!--more-->

The normal equation is a matrix equation that allows you to solve {%m%}\vec{\theta}{%em%} above. By doing so, you can construct a linear regression model that allows you to predict the value of the target variable for any new samples. It is given by

{% math %}
\vec{\theta} = (X^T X)^{-1} X^T \vec{y}.
{% endmath %}

The goal of the normal equation is to provide you with {%m%}\vec{\theta}{%em%} so that the cost function

{% math %}
J(\vec{\theta}) = \frac{1}{2}(X\vec{\theta} - \vec{y})^T(X\vec{\theta} - \vec{y})
{% endmath %}

is minimized.

You can find derivations of the normal equation on the internet, but most of them include a lot of handwaving and skip seemingly trivial, but actually tedious and crucial steps. I will derive it in this article.

## Minimizing {%m%}J(\vec{\theta}){%em%} Using Vector Calculus

Recall back in calculus that to minimize a function, you simply take its derivative and set it to zero. We will do the same for the cost function. But first, we need to borrow some concepts from vector calculus.

The derivative of a vector is called a gradient, and is denoted by the symbol {%m%}\nabla{%em%}. We subscript the symbol {%m%}\nabla{%em%} with some variable to denote that we're taking a vector's derivative with respect to that variable. Thus, the derivative with respect to {%m%}\theta{%em%} is simply {%m%}\nabla_\theta{%em%}.

Minimizing the cost function means taking its derivative with respect to {%m%}\theta{%em%} and setting the result to zero:

{% math %}
\nabla_\theta J(\vec{\theta}) = 0.
{% endmath %}

We will use this equation to derive the normal equation.

## Some Useful Theorems

In order to proceed with the derivation, we're gonna need the help of some theorems. You can find these as well in any proper vector calculus textbook. Let {%m%}A{%em%}, {%m%}B{%em%}, and {%m%}C{%em%} be matrices. The two theorems we will use are the following:

{% math %}
\begin{align}
\nabla_{A^T} f(A) &= (\nabla_A f(A))^T \\
\nabla_A \text{tr}(ABA^TC) &= CAB + C^TAB^T
\end{align}
{% endmath %}

where the _trace_ {%m%}\text{tr}A{%em%} of a matrix {%m%}A{%em%} is just the sum of its diagonal elements.

If we set the matrix {%m%}C{%em%} as the identity matrix in the second theorem above, we simply get

{% math %}
\nabla_A \text{tr}(ABA^T) = AB + AB^T.
{% endmath %}

By setting {%m%}D = ABA^T{%em%}, we get

{% math %}
\text{tr}(ABA^T) = \text{tr}(D) = f(D)
{% endmath %}

due to the fact that the trace of a matrix is a function {%m%}f{%em%} that maps from {%m%}\mathbb{R}^{m \times n}{%em%} to {%m%}\mathbb{R}{%em%}. Taking the transpose of the last equation and noting that {%m%}(AB)^T = B^T A^T{%em%}, we get

{% math %}
\begin{align}
[ \nabla_A f(D) &= AB + AB^T ]^T \\
(\nabla_{A} f(D))^T &= B^T A^T + BA^T
\end{align}
{% endmath %}

But the left-hand side of the equation is just the first theorem. Thus, we have

{% math %}
\nabla_{A^T} f(D) = B^T A^T + BA^T
{% endmath %}

We can finally proceed with our derivation.

## All Together Now

Let's take the gradient of {%m%}J{%em%} with respect to {%m%}\theta{%em%}

{% math %}
\begin{align}
\nabla_\theta J(\vec{\theta}) &= \nabla_\theta \left(\frac{1}{2}(X\vec{\theta} - \vec{y})^T(X\vec{\theta} - \vec{y})\right) \\
&= \frac{1}{2} \nabla_\theta \left( \vec{\theta}^T X^T X \vec{\theta} - \vec{\theta}^T X^T \vec{y} - \vec{y}^T X \vec{\theta} + \vec{y}^T \vec{y} \right)
\end{align}
{% endmath %}

The term {%m%}\vec{y}^T \vec{y}{%em%} on the right side is a real number. This indicates that the whole term in the parentheses is some function that maps to a real number. If we use the fact that the trace of a real number is just itself, and that {%m%}\text{tr}(A) = \text{tr}(A^T){%em%}, we get

{% math %}
\begin{align}
\nabla_\theta J(\vec{\theta}) &= \frac{1}{2} \nabla_\theta \text{tr} \left( \vec{\theta}^T X^T X \vec{\theta} - \vec{\theta}^T X^T \vec{y} - \vec{y}^T X \vec{\theta} + \vec{y}^T \vec{y} \right) \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - \text{tr}(\vec{y}^T X \vec{\theta}) - \text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right] \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - \text{tr}(\vec{y}^T X \vec{\theta})^T - \text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right] \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - \text{tr}(\vec{\theta}^T X^T \vec{y}) - \text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right] \\
&= \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - 2\text{tr}(\vec{\theta}^T X^T \vec{y}) + \text{tr}(\vec{y}^T \vec{y}) \right].
\end{align}
{% endmath %}

The term {%m%}\text{tr}(\vec{y}^T \vec{y}){%em%} has vanished because it's treated as a constant when we're taking the gradient with respect to {%m%}\theta{%em%}.

We are now left with

{% math %}
\nabla_\theta J(\vec{\theta}) = \frac{1}{2} \nabla_\theta \left[ \text{tr}(\vec{\theta}^T X^T X \vec{\theta}) - 2\text{tr}(\vec{\theta}^T X^T \vec{y}) \right].
{% endmath %}

If we use our derived theorem above ({%m%}\nabla_{A^T} f(D) = B^T A^T + BA^T{%em%}) and substitute as follows,

{% math %}
\begin{align}
A^T &= \vec{\theta} \\
B &= X^TX
\end{align}
{% endmath %}

we should get

{% math %}
\begin{align}
\nabla_\theta J(\vec{\theta}) &= \frac{1}{2} \left(X^T X \vec{\theta} + X^T X \vec{\theta} - 2 X^T \vec{y} \right) \\
&= X^T X \vec{\theta} - X^T \vec{y}
\end{align}
{% endmath %}

Since {%m%}\nabla_\theta J(\vec{\theta}) = 0{%em%}, we finally have

{% math %}
X^T X \vec{\theta} - X^T \vec{y} = 0
{% endmath %}

which is the normal equation. How cool is that! :)




