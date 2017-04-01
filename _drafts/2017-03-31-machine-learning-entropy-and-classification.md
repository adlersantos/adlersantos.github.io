---
title:  "Machine Learning: <br> Entropy and Classification"
date:   2017-03-31
---

## A Simple Classification Example 

Let's say we have a dataset with features P, Q, R, and a binary target variable $Z$:

| id | Feature $P$ | Feature $Q$ | Feature $R$ | Target Variable $Z$ |
|----|-----------|-----------|-----------|-----------------|
| 1 | a | c | e | class A |
| 2 | b | d | e | class A |
| 3 | b | d | f | class B |
| 4 | a | d | e | class A |
| 5 | a | c | f | class B |
| 6 | b | d | e | class B |

The goal is to find the feature that best predicts the value of $Z$.

By looking at the example, we see that Feature $R$ will have the highest "predictive power" in determining $Z$:

- 3 out of 4 instances which have $e$ as its Feature $R$ (ids: 1, 2, 4, 6) correspond to a target variable of $A$.
- All of the instances which have $f$ as its Feature $R$ (ids: 3, 5) correspond to a target variable of $B$.

<br />
The rest of features X and Y are not as accurate as Feature Z in predicting the target. Thus, we call Feature Z as the most _informative attribute_.

## Entropy

It's easy to see the most informative attribute in the example above. But for larger, more complex datasets, it's virtually impossible to count and match all the features by hand with the target variable's value for all instances. We need a way to measure the informativeness of a feature when it comes to classifying the dataset properly among the target variables. This can be done with the use of _entropy_.

Entropy is a term that's usually used in statistical physics as a measure of how disordered a system is. Machine learning's use of entropy isn't far from this concept of disorderedness. In ML, a set of instances is said to be disordered when there is a considerable mix of target classes that the instances belong to. This means that the more mixed the segment is with respect to the classifications (i.e., target variables), the higher the entropy.

Here's a visual breakdown of how well the features above classify the dataset into their target classes.



Classifying datasets with categorical attributes is all about segmenting data in a way that the resulting subsets have the lowest entropy, without penalizing the model's performance when it generalizes to unknown data (i.e. prevent the model from overfitting).

The entropy $S$ of a set with a binary target with values $A$ and $B$ is defined as

$$
S = \sum_{i \in \{A,B\}} p_i \text{log}(p_i)
$$

where $p_i$ is the proportion of the ith target variable in the set.

Generally,

$$
S = \sum_{i \in V_t} p_i \text{log}(p_i)
$$

where $V_t$ is the set of unique values of the target variable.

It's easy to see that the number of elements in $V_t$ is equal to the number of terms in the summation.

## Information Gain

The informativeness of a feature can be measured using the concept of information gain, which can be defined using entropy. Consider a dataset which we call the _parent_, and we'd like to know how well a certain feature classifies the elements contained in the parent. 

We define the information gain $G_f$ of a feature $f$ on a parent set as

$$
G_f = S_p - \sum_{j \in V_f} P_j S_j
$$

where $V_f$ is the set of unique values of the considered feature, $S_p$ is the entropy of the parent dataset, $P_j$ is the proportion of instances belonging to the jth value of the considered feature, and $S_j$ is the entropy of the set whose elements have the jth value of the feature.

Let's apply the definition of $G$ to our specific example above. The information gain when we use Feature Q, whose values are $c$ and $d$ is

$$
G_q = S_p - \sum_{j \in \{c, d\}} P_j S_j
$$


# Go back to simple example and apply IG

# Use a bigger example and apply IG to determine the most informative attribute

Example on higly booked properties (it's booked at least one night a week on average) vs. low booked ones.

| Rental Type | Neighborhood Type | Access to Bus/Train | Internet Access | Highly Booked? |
|-------------|----------------------|---------------------|-----------------|----------------|
| condo | urban core | good | yes | yes |
| home | residential vicinity | good | yes | no |
| apartment | urban core | good | yes | yes |
| home | rural | bad | yes | no |
| condo | urban core | good | yes | yes |
| condo | urban core | good | yes | yes |
| apartment | residential vicinity | good | no | yes |
| home | residential vicinity | good | yes | no |

# What about numeric features?

Broken down categorically into intervals. For example, age can be broken down into ages 1-4, 5-8, 9-12, and so on.

# Example with a numeric feature
