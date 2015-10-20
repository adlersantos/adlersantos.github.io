---
title:  "Split-Testing and Statistics: <br /> What Makes a Winner?"
date:   2015-10-11
---

So you've deployed tons of split-tests (more popularly called "A/B testing") across your site. Data has been collected, but the verdict is still out. For some of the tests, significant gaps in the performance of the variations makes it decisively simple to declare winning variations. While for other tests, the results are not so far apart, and you scratch your head as you become confused on what works and what doesn't.

What do you do when the differences are not so significant? When it's not obvious whether or not a split-test scenario has a winner, statistics will save the day! I'll show you how in this article.

# Three Hypothetical Scenarios

Suppose you have a pair of promos (Promo A and Promo B) for new signups, and you've done a split-testing experiment to determine which one converts visitors to signups better. Let's assume three scenarios below, where every promo has an equal chance of getting shown. Let's set the number of people who viewed each promo to 1,000 `[1]`.

**Scenario X**

- Promo A: 200 out of 1000 people who saw the promo signed up
- Promo B: 15 out of 1000 people who saw the promo signed up

<br />
**Scenario Y**

- Promo A: 300 out of 1000 people who saw the promo signed up
- Promo B: 285 out of 1000 people who saw the promo signed up

<br />
**Scenario Z**

- Promo A: 300 out of 1000 people who saw the promo signed up
- Promo B: 260 out of 1000 people who saw the promo signed up

<br />
Because 200 signups is dramatically bigger than 15 signups, we can be pretty confident to declare Promo A as the winner in Scenario X. Let's skip that for now and turn our attention to Scenarios Y and Z, where the numbers are pretty close to each other.

# Hypothesis Testing using Statistics

For Scenarios Y and Z, it seems that Promo A performs only slightly better than Promo B. How can we determine if the difference is significant enough for us to declare Promo A as the winner?

**Probability and Standard Deviation**

One variable that we will need is the probability $p$ that a visitor signs up when shown a promo. This is just the number of people who signed up ($n$) divided by the number of people who saw the promo ($N$). We will use subscripts on $p$ to indicate what promo we're assigning the probability to, e.g. $p_A$ for Promo A. The probability of a visitor to signup when she sees Promo A is as follows:

$$
p_A = \frac{n_A}{N_A}
$$

For Promo B, just change the subscript from A to B:

$$
p_B = \frac{n_B}{N_B}
$$

We'll also need the standard deviation for a certain promo. The standard deviations for promos A and B are given by

$$
\begin{align}
\sigma_A = \sqrt{\frac{p_A(1 - p_A)}{N_A}} \\
\sigma_B = \sqrt{\frac{p_B(1 - p_B)}{N_B}}
\end{align}
$$

**The Null Hypothesis**

In statistics, there's a concept called the _null hypothesis_. I found it useful to think of the null hypothesis as a statement that's assumed true until proven otherwise.

> **Null hypothesis for split-testing: There is no winning variation. All variants are equally effective.**

In our case, the null hypothesis for every split-testing scenario is to assume that there is no winner, i.e. Promo A and Promo B are equally effective. Keep this in mind as we go along. Now let's move on to discuss two more statistical variables: the _z-statistic_ and the _two-tailed p-value_.

**The z-statistic and Cumulative Distribution Function**

For each scenario above, we need to calculate a variable called the z-statistic. It can be computed as follows:

$$ z = \frac{p_B - p_A}{\sqrt{\sigma_A^2 + \sigma_B^2}} $$

Don't bother if $z$ is positive or negative. What we are interested in is its closeness to zero. The closer the value of $z$ is to zero, the more likely our "no winner" assumption holds true for a specific scenario. It is important to note that $z$ is not a probability.

Finally, we will need the cumulative distribution function (CDF) for the normal distribution. It depends on a pre-defined mean (Greek letter mu) and standard deviation, more on this later. The CDF is defined as

$$
\text{cdf}(z, \mu, \sigma) = \frac{1}{2} \times
\begin{cases}
1 + \text{erf}(\frac{z - \mu}{\sqrt{2}\sigma}) &\mbox{if } z \geq \mu \\
1 - \text{erf}(\frac{z - \mu}{\sqrt{2}\sigma}) &\mbox{if } z < \mu
\end{cases}
$$

where the error function (ERF) is defined as

$$
\text{erf}(x) = \frac{2}{\sqrt{\pi}} \int_0^x e^{-t^2} dt
$$

The CDF essentially gives the normal probability above or below a certain value of $z$.

# The Two-Tailed p-value: Is There a Winner?

Let's go back to the problem of declaring a winner for any of the split-testing scenarios Y and Z. The z-statistic, manifested as a probability, is the probability that the "no winner" hypothesis is true. This is in the Bayesian sense, where the probability is a _degree-of-belief_ that there is no winner given an outcome from the experiment. We can compute this by simply multiplying the CDF by two. This is the definition of the _two-tailed p-value_, which we define as $p'$ since it's a probability value `[3]`:

$$
p'(z, \mu, \sigma) = 2 \times \text{cdf}(z, \mu, \sigma)
$$

Another way to interpret $p'$ is that it gives us the probability of seeing such a large difference in a split-testing scenario if there was indeed no winner. The closer $p'$ is to zero, the greater the probability that there's a winning variant.

Let's calculate the two-tailed p-values of Scenarios Y and Z. Since Promo A has better conversion, calculating $p'$ will help us declare with confidence whether or not Promo A is a winner. For simplicity, we can assume a mean of zero and a standard deviation of one `[4]`. This means all we have to do is to calculate $z$ for every scenario, and use it to calculate $p'$.

For Scenario Y ($n_A = 300$, $n_B = 285$), let's calculate $z_Y$ and use it to compute for $p'_Y$.

$$
\begin{align}
z_Y &= -0.737 \\
p'_Y(z_Y, 0, 1) &= 2 \times \text{cdf}(-0.737, 0, 1) \\
&= 0.461
\end{align}
$$

There's a 46.1% probability that the difference between 300 signups and 285 signups is insignificant. Since there's only about a 50/50 chance that Promo A is the winner when we see values like in Scenario Y, it's not very reasonable to declare Promo A the winner for this scenario . The null hypothesis holds and we can safely conclude that there's no winner in this scenario.

How about for Scenario Z ($n_A = 300$, $n_B = 265$)? The values are slightly farther from each other than in Scenario Y. Can we say that the null hypothesis still holds? Let statistics guide us by calculating $z_Z$ and $p'_Z$ for this scenario:

$$
\begin{align}
z_Z &= -1.994 \\
p'_Z(z_Z, 0, 1) &= 2 \times \text{cdf}(-1.994, 0, 1) \\
&= 0.046
\end{align}
$$

If we assume that Promos A and B are equally effective, the probability of getting 300 signups vs 260 signups is just 4.6%. This tells us that it's highly unlikely for Promo A and Promo B to have equal performance. We now have a result! To increase conversion to signup, remove Promo B and just show Promo A to every visitor.

In summary, a two-tailed p-value that's less than 0.05 allows us to declare a winner. The winning variation will be whichever converts better in the split-test experiment. In the case of Scenario Z, we can say that Promo A is the winner with 95% confidence.

[For some math/physics textbook pun, I leave it as an exercise for the reader to show that Scenario X (200 signups vs 15 signups) has a two-tailed p-value that's almost zero, i.e. there is almost zero probability that the null hypothesis is true, which implies that Promo A is a clear winner.]

# Two-tailed p-value in Ruby and Python

For your own use, I've coded $p'$ in Ruby and Python. All you need are the number of people who converted and the number of impressions for each of the two variations in the experiment (labeled A and B below).

**Ruby**

{% highlight ruby %}
def two_tailed_p_value(n_A, nv_A, n_B, nv_B, mean=0, std=1)
  p_A = 1.0 * n_A / nv_A
  p_B = 1.0 * n_B / nv_B

  sigma_A = Math.sqrt(p_A * (1 - p_A) / nv_A)
  sigma_B = Math.sqrt(p_B * (1 - p_B) / nv_B)

  z = (p_B - p_A) / Math.sqrt(sigma_A**2 + sigma_B**2)

  erf_coefficient = (z < mean) ? -1 : 1
  erf_arg = (z - mean) / (Math.sqrt(2) * std)

  1 + erf_coefficient * Math.erf(erf_arg)
end
{% endhighlight %}

**Python**

{% highlight python %}
import math
from __future__ import division

def two_tailed_p_value(n_A, N_A, n_B, N_B, mean=0, std=1):
    p_A = n_A / n_A
    p_B = n_B / n_B

    sigma_A = math.sqrt(p_A * (1 - p_A) / N_A)
    sigma_B = math.sqrt(p_B * (1 - p_B) / N_B)

    z = (p_B - p_A) / math.sqrt(sigma_A**2 + sigma_B**2)

    erf_coefficient = -1 if z < mean else 1
    erf_arg = (z - mean) / (math.sqrt(2) * std)

    return 1 + erf_coefficient * math.erf(erf_arg)
end
{% endhighlight %}

### Footnotes

**`[1]`** To make the statistical analysis work, it is required that the "promo viewed" events must be independent. Every person visiting the site must only see one type of promo. There are rare cases when the same person gets to view the two promos, say by using different browsers, devices, or locations. We assume these cases to have a negligible effect on our analysis.

**`[2]`** We don't have to calculate the $erf$ integral ourselves. There are computational libraries that can do this such as Python's `math.erf` or Ruby's `Math.erf` function.

**`[3]`** Also called the "two-sided p-value".

**`[4]`** This can easily be done via mean normalization. For samples that are normally distributed as in our split-testing case, we normalize using the [standard score](https://en.wikipedia.org/wiki/Standard_score).
