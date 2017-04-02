---
title:  "Machine Learning: <br> Entropy and Classification"
date:   2017-04-02
---

## A Simple Classification Example 

Let's say we have a dataset with categorical features $P$, $Q$, $R$, and a binary target variable $Z$:

| id | Feature $P$ | Feature $Q$ | Feature $R$ | Target Variable $Z$ |
|----|-----------|-----------|-----------|-----------------|
| 1 | a | c | e | $G$ |
| 2 | b | d | e | $G$ |
| 3 | b | d | f | $H$ |
| 4 | a | d | e | $G$ |
| 5 | a | c | f | $H$ |
| 6 | b | d | f | $H$ |

The goal is to find the feature that best predicts the value of $Z$.

After a bit of close inspection, we see that Feature $R$ will have the highest "predictive power" in determining $Z$:

- All instances with Feature $R$ equal to `e` (ids 1, 2, 4, 6) belong to class $G$.
- All instances with Feature $R$ equal to `f` (ids 3, 5) belong to class $H$.

<br />
The other features $P$ and $Q$ are not as accurate as $R$ in predicting the target. We call Feature $R$ as the _most informative attribute_.

## Entropy

It might seem an easy task to determine the most informative attribute in the dataset above by hand. But for larger, more complex datasets, it's impractical to count and match all the features with target classes for all samples. We need a way to measure the informativeness of a feature, that is, how it effectively classifies the dataset. Fortunately, we have _entropy_ to help us do that.

Entropy is a term used in statistical physics as a measure of how disordered a system is. Machine learninGs use of entropy isn't far from this concept of disorderedness. In ML, a set of instances is said to be disordered when there's a considerable mix of target classes that the instances belong to. This means that the more mixed the segment is with respect to the classifications (i.e., target variables), the higher the entropy.

Here's a visual breakdown of how well each feature classifies the dataset into target classes.



The entropy $S$ of a set with a binary target with values $G$ and $H$ is defined as

$$
S = -\sum_{i \in \{G,H\}} p_i \log(p_i)
$$

where $p_i$ is the proportion of the ith target variable in the set. Entropy always ranges between 0 (minimum disorder) and 1 (maximum disorder).

As an exercise, let's calculate $S$ for the whole dataset:

$$
\begin{align}
S &= -\sum_{i \in \{G,H\}} p_i \ \log(p_i) \\
&= -p_{G} \log(p_{G}) - p_{H} \log(p_{H}) \\
&= -(0.5)\log(0.5) - (0.5)\log(0.5) \\
&= 1
\end{align}
$$

This tells us that a set whose samples are equally divided among the target's values is maximally disordered.

If we have two or more target classes, the equation above generalizes as follows:

$$
S = -\sum_{i \in V_t} p_i \log(p_i)
$$

where $V_t$ is the set of unique values of the target variable.

It's easy to see that the number of elements in $V_t$ is equal to the number of terms in the summation.

Classifying datasets with categorical attributes is all about segmenting data in a way that the resulting subsets have lower entropy than the whole dataset, all without penalizing the model's performance when it's time to classify unknown data (i.e., prevent the model from overfitting).

## Information Gain

The informativeness of a feature can be measured using the concept of information gain, which can be defined using entropy. Consider a dataset which we call the _parent_, and we'd like to know how well a certain feature classifies the elements contained in the parent. 

We define the information gain $I_G$ of a feature $G$ on some parent dataset as

$$
I_G = S_p - \sum_{j \in V_f} P_j S_j
$$

where $V_G$ is the set of unique values of feature $G$, $S_p$ is the entropy of the parent set, $P_j$ is the proportion of instances belonging to the $j$th value of the considered feature, and $S_j$ is the entropy of the set whose elements have the $j$th value of the feature.

Now let's apply the definition of information gain $I$ for every feature in our dataset above. 

### Information Gain $I_P$

The information gain for Feature $P$, whose values are the set {$a, b$}, is

$$
I_P = S_p - \sum_{j \in \{a, b\}} P_j S_j
$$

$P_a$ and $P_b$ in the summation are the proportion of instances where Feature $Q$ has values $a$ and $b$, respectively.

$$
P_a = \frac{3}{6} = 0.5 \\
P_b = \frac{3}{6} = 0.5
$$

Now $S_a$ and $S_b$ are the entropies of the subsets whose instances have Feature $Q$ equal to $a$ and $b$, respectively.

$$
\begin{align}
S_a &= -\sum_{i \in \{G,H\}} p_{a,i} \ \log(p_{a,i}) \\
&= -p_{G} \log(p_{G}) - p_{H} \log(p_{H}) \\
&= -\frac{2}{3} \log\left(\frac{2}{3}\right) - \frac{1}{3} \log \left(\frac{1}{3}\right) \\
&= 0.918 \\
\\
S_b &= -\sum_{i \in \{G,H\}} p_{b,i} \ \log(p_{b,i}) \\
&= -p_{G} \log(p_{G}) - p_{H} \log(p_{H}) \\
&= -\frac{1}{3} \log\left(\frac{1}{3}\right) - \frac{2}{3} \log \left(\frac{2}{3}\right) \\
&= 0.918
\end{align}
$$

Thus, the information gain for Feature $P$ is

$$
\begin{align}
I_P &= 1 - [(0.5)(0.918) + (0.5)(0.918)] \\
&= 0.541
\end{align}
$$

### Information Gain $I_Q$

Given the following values,

$$
\begin{align}
P_c &= \frac{2}{6} \\
P_d &= \frac{4}{6} \\
S_c &= 1 \\
S_d &= 1 
\end{align}
$$

we see that the value of $I_Q$ is zero.

This means that there is no information gained when using Feature $Q$ to classify the dataset.

### Information Gain $I_R$

Given the following values,

$$
\begin{align}
P_e &= \frac{3}{6} \\
P_f &= \frac{3}{6} \\
S_e &= 0 \\
S_f &= 0
\end{align}
$$

we see that the value of $I_R$ is 1, the highest information gain among all the features.

Using the definitions and tools outlined above, we can now formally state why Feature $R$ is the most informative attribute. It's because it has the highest information gain among all the features in the dataset.

## What about numeric features?

We may begin to wonder about regression problems, where the target variable is a numeric one instead of a categorical one. How do we calculate for the most informative attribute in this case?

Information gain still makes sense here. But it must represent a numerical feature's "closeness" to the target variable. Instead of using entropy as a measure of disorder (or order) with regards to the categorical target values, we use _variance_ as a measure of closeness to the target value.

A feature will have a high variance if its values do not behave as the target values behave: when the feature increases (decreases) in value, the target must also increase (decrease) in value. In other words, the counterpart of information gain in regression problems is _correlation_.

Here's a table outlining which quantities are analogous to each other between classification and regression problems.

|                | Measure of Orderness | Measure of Informativeness |
|----------------|----------------------|----------------------------|
| Classification | Entropy              | Information Gain           |
| Regression     | Variance             | Correlation                |

Hope this was insightful! $_\square$
