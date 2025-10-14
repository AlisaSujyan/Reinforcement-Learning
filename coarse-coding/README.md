# Coarseness of Coarse Coding

### Project Overview

This project demonstrates the effect of **receptive field width** in **coarse coding** on the learning dynamics of linear function approximation.

The goal is to analyze how the **size of the receptive fields** (intervals) influences generalization and convergence when approximating a simple target function.



### Problem Description

We aim to learn a **1-dimensional square-wave function** using **linear function approximation** with **coarse coding** features.

* The target function ( U_t ) is defined as:
  $$
  U(x) =
  \begin{cases}
  1, & 0.5 < x < 1.5 \
  0, & \text{otherwise}
  \end{cases}
  $$
* The domain is the interval ([0, 2)).
* Because the task is 1-dimensional, receptive fields are **intervals** rather than circular regions.
* Learning is performed with three different receptive field widths: **narrow**, **medium**, and **broad**.
* The density of features is held constant (≈ 50 features) across all settings.
* Samples ((x, U(x))) are drawn uniformly at random from the domain.

The **step-size parameter** is scaled according to:
$$
\alpha = \frac{0.2}{n}
$$
where ( n ) is the number of features active at a given time.



### Algorithm: Linear Function Approximation with Coarse Coding

### Representation

Each receptive field is modeled as an **interval** ([l, r)).
A point (x) is represented by the set of active features whose intervals contain (x).

The approximate value function is computed as:
$$
\hat{v}(x) = \sum_{i \in \text{active}(x)} w_i
$$

### Learning Update

Given a sample ((x, y)) from the target function:
$$
\delta = y - \hat{v}(x)
$$
The weight update for each active feature (i) is:
$$
w_i \leftarrow w_i + \frac{\alpha}{n} , \delta
$$

This semi-gradient update encourages nearby states (overlapping features) to generalize during learning.


### Experiments

The experiment was repeated for **three receptive field widths**:

* **Narrow** intervals — limited overlap and local generalization.
* **Medium** intervals — balanced overlap.
* **Broad** intervals — wide overlap and smooth generalization.

In each case:

* 50 feature intervals were distributed evenly across the domain.
* Training samples were drawn uniformly at random.
* The same number of updates was performed per setting.



## Results

* With **broad features**, updates generalized widely, producing smooth approximations early in learning.
* With **narrow features**, learning was slower and more localized, resulting in a “bumpy” function early on.
* Despite large differences in early behavior, **the final learned function** in all cases closely approximated the true square wave.

These results demonstrate that **feature width strongly affects generalization speed**, but **only slightly affects final performance**.



## Summary of Findings

| Feature Width | Early Learning | Generalization | Final Accuracy |
| ------------- | -------------- | -------------- | -------------- |
| Narrow        | Local, uneven  | Weak           | High           |
| Medium        | Balanced       | Moderate       | High           |
| Broad         | Smooth, fast   | Strong         | High           |

