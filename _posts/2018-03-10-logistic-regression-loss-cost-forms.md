---
layout: full-width
title:  "How the loss and cost functions got their forms in logistic regression"
date:   2018-03-10
---

In logistic regression, the loss function {%m%}\mathcal{L}(\hat{y}, y){%em%} and the cost function J take the forms

{% math %}
\begin{align}
\mathcal{L}(\hat{y}, y) &= - \left(y \log (\hat{y}) + (1 - y) \log (1 - \hat{y}) \right) \\
J &= \frac{1}{m} \sum_{i=1}^m \mathcal{L}\left(\hat{y}^{(i)}, y^{(i)}\right)
\end{align}
{% endmath %}

where {%m%}\hat{y} = \sigma(W^\text{T} X + b){%em%} is the sigmoid of the linear superposition of the features represented in matrix form, with {%m%}W{%em%} as the vector of the feature weights, and {%m%}b{%em%} as the intercept term. The sigmoid of some function {%m%}z{%em%} is defined as

{% math %}
\sigma(z) = \frac{1}{1 + e^{-z}}.
{% endmath %}

To understand why {%m%}\mathcal{L}{%em%} and {%m%}J{%em%} take such forms, first note that {%m%}\hat{y}{%em%} is the probability of the binary classification variable {%m%}y{%em%} to be equal to a positive example ({%m%}y = 1{%em%}). <!--more--> This means for a single example {%m%}x{%em%}, {%m%}\hat{y}{%em%} takes the form {%m%}\hat{y} = p(y|x){%em%} if {%m%}y=1{%em%}. The other condition is when {%m%}y = 0{%em%}, where we want {%m%}\hat{y}{%em%} to also be equal to zero. To satisfy both of these conditions, we define {%m%}p(y|x){%em%} as:

{% math %}
p(y|x) = 
\begin{cases}
    \hat{y} & (\text{if } y = 1)\\
    1 - \hat{y} & (\text{if } y = 0)
\end{cases}
{% endmath %}

Another way to write {%m%}p(y|x){%em%} is as follows:

{% math %}
p(y|x) = \hat{y}^{y} (1 - \hat{y})^{(1 - y)}
{% endmath %}

You can check this for yourself using {%m%}y = 1{%em%} or {%m%}y = 0{%em%}.

Now, let's consider the idea of applying a logarithm on probabilities. The probability defined above for a single example is simple enough, but if you'd like to do maximum likelihood estimation for a set of examples, then we have to consider some "overall probability" value. In probability theory, when two events {%m%}A{%em%} and {%m%}B{%em%} are independent, then the probability of them both occurring (their intersection) is given by

{% math %}
P(A \cup B) = P(A) \cdot P(B)
{% endmath %}

In most cases, it's safe to assume that every example in your training set occurred independently of each other. Thus the probability {%m%}P_{train}{%em%} for a training set of {%m%}m{%em%} samples is

{% math %}
\begin{align}
P_{train} &= P(y^{(1)}|x^{(1)}) \cdot P(y^{(2)}|x^{(2)}) \ldots P(y^{(m)}|x^{(m)}) \\
         &= \prod_{i=1}^m p(y^{(i)})
\end{align}
{% endmath %}

We can use probabilities outrightly, but using the logarithm of probabilities has many practical advantages. One advantage is that multiplication is more computationally expensive than addition. We'll see shortly how applying the log translates the above from a multiplication problem to an addition problem. Also, computers have limited floating point accuracy, thus when a computer multiplies a very large set of probabilities (recall that they're valued from zero to one), you may end up with a value very close or equal to zero, which is far from ideal. You will not encounter this weird behaviour if you apply the logarithm.

Let's apply the logarithm to {%m%}p(y|x){%em%}. Using the fact that {%m%}\log(ab) = \log(a) + \log(b){%em%} and {%m%}\log(a^b) = b \log(a){%em%}, we get

{% math %}
\begin{align}
\log p(y|x) &= \log\left(\hat{y}^y (1 - \hat{y})^{(1 - y)}\right) \\ 
           &= \log\left(\hat{y}^y\right) + \log\left((1 - \hat{y})^{(1 - y)}\right) \\
           &= y \log \hat{y} + (1 - y) \log(1 - \hat{y})
\end{align}
{% endmath %}

This is similar in form to the loss function of logistic regression for a single example, except for the minus sign. Logistic regression includes the minus sign in order for the cost function to be minimized.

How about for {%m%}P_{train}{%em%}? By applying the multiplication rule for probabilities, it turns out that {%m%}\log(P_{train}){%em%} is just the sum of the loss functions of every training example:

{% math %}
\begin{align}
\log\left(P_{train}\right) &= \log\left(\prod_{i=1}^m p(y^{(i)})\right) \\
              &= \log\left(p(y^{(1)})\right) + \log\left(p(y^{(2)})\right) + \cdots + \log\left(p(y^{(m)})\right) \\
              &= \sum_{i=1}^m \log\left(p(y^{(i)})\right) \\
              &= \sum_{i=1}^m y^{(i)} \log \hat{y}^{(i)} + (1 - y^{(i)}) \log(1 - \hat{y}^{(i)}) 
\end{align}
{% endmath %}

Looks familiar? Recall from above that the cost function {%m%}J{%em%} has the form

{% math %}
\begin{align}
J &= \frac{1}{m} \sum_{i=1}^m - \left( y^{(i)} \log \hat{y}^{(i)} + (1 - y^{(i)}) \log(1 - \hat{y}^{(i)}) \right) \\
  &= \frac{1}{m} \sum_{i=1}^m \mathcal{L}\left(\hat{y}^{(i)}, y^{(i)}\right)
\end{align}
{% endmath %}

Thus, {%m%}\log\left(P_{train}\right){%em%} is the cost function {%m%}J{%em%} without the negative sign and without the scaling factor {%m%}(1/m){%em%}:

{% math %}
\begin{align}
\log\left(P_{train}\right) &= -\sum_{i=1}^m \mathcal{L}\left(\hat{y}^{(i)}, y^{(i)}\right) \\
                           &= - m J

\end{align}
{% endmath %}

The factor {%m%}(1/m){%em%} found in {%m%}J{%em%} is for scaling purposes, and to allow us to treat the cost function as the average of the loss functions for all {%m%}m{%em%} training examples.

