---
title: 'The Algebraic Structure of the Space of Quasimodular Forms'
date: 2021-06-02
permalink: /posts/2021/06/algstr/
tags:
  - gsoc
  - modform
  - math
category:
  - gsoc
author_profile: false
---

This post is a direct follow up of the [last post](/posts/2021/05/quasimodform/). In particular, I want to explain what is the algebraic structure of the space of quasimodular forms and how it will be used in our SageMath implementation.

Before this, let me recall the structure of the classical modular forms. We will denote by $$\mathcal{M}_k$$ the space of all modular forms of weight $$k$$ for $$\mathrm{SL}_2(\mathbb{Z})$$. Observe that this space is in fact a $$\mathbb{C}$$-vector space. Indeed, for any $$f,g\in \mathcal{M}_k$$ and $$\lambda\in\mathbb{C}$$ we will have $$f + \lambda g \in \mathcal{M}_k$$ (it is in fact finite dimensional over $$\mathbb{C}$$, but this is non-trivial). Moreover, we observe that if $$f\in \mathcal{M}_{k_1}$$ and $$g\in \mathcal{M}_{k_2}$$ for $$k_1, k_2 \in \mathbb{Z}$$ then $$fg\in \mathcal{M}_{k_1 + k_2}$$. Thus the set of *all* modular forms (whitout any fixed weight), denoted $$\mathcal{M}_*$$, is in fact a graded ring:

$$
\mathcal{M}_* = \bigoplus_{k\geq 0} \mathcal{M}_k,\quad \mathcal{M}_{k_1}\mathcal{M}_{k_2} \subseteq \mathcal{M}_{k_1 + k_2}
$$

It turns out that this ring has a simple structure which is very convenient for computations:

## Proposition.

We have the isomorphism of ring $$\mathcal{M}_* \cong \mathbb{C}[G_4, G_6]$$, where $$G_4$$ and $$G_6$$ are the weight 4 and weight 6 Eisenstein series respectively. $$\diamond$$

A similar relation can be found for the space of quasimodular forms. Let's write some notations:

* $$\widetilde{\mathcal{M}}_k^{\leq p} :=$$ set of quasimodular forms of weight $$k$$ and depth $$\leq p$$ for $$\mathrm{SL}_2(\mathbb{Z})$$;

* $$\widetilde{\mathcal{M}}_k := \bigcup_{p\geq 0} \widetilde{\mathcal{M}}_k^{\leq p} =$$ set of quasimodular forms of weight $$k$$ for $$\mathrm{SL}_2(\mathbb{Z})$$;

* $$\widetilde{\mathcal{M}}_* := \bigoplus_{k\geq 0} \widetilde{\mathcal{M}}_k =$$ graded ring of quasimodular forms for $$\mathrm{SL}_2(\mathbb{Z})$$.

An general element of a quasimodular form space will be called a *quasiform*.

## Proposition.

We have the following isomorphism of ring:

$$
\widetilde{\mathcal{M}}_* \cong \mathbb{C}[G_2, G_4, G_6].
$$

In particular, we have $$\widetilde{\mathcal{M}}_* \cong \mathcal{M}_*[G_2]$$. $$\diamond$$

This isomorphism will be the main point for our implementation of the graded ring of quasimodular forms into SageMath. Indeed, a general quasimodular form $$F\in \widetilde{\mathcal{M}}_*$$ will be implemented as a polynomial in $$G_2$$ with coefficient in $$\mathcal{M}_*$$:

$$
\label{eq:pol_G2}
F = f_0 + f_1 G_2 + f_2 G_2^2 + \ldots f_r G_2^r,\quad f_i \in \mathcal{M}_*, r\geq 0.
$$

Hence, to encode a quasiform in SageMath it *should* be enough to simply specify a list modular forms:

{% highlight ruby python %}
[ f_0, f_1, f_2, ..., f_r ]
{% endhighlight %}

where each f_i correspond to a coefficient of (\ref{eq:pol_G2}). A useful feature of SageMath is the fact that the classical modular forms are already implemented. Thus, during the implementation of the quasimodular forms, it would be all to our advantage to *reuse* what has already been implemented.

In addition to the implementation of classical modular forms into SageMath, there is also the software PARI/GP that can perform computation with modular forms. [PARI/GP](https://pari.math.u-bordeaux.fr/) is also a free mathematical software, but it is more centered around number theory. What is more important to us is the fact that PARI/GP is included in SageMath! This means that when using SageMath, it is possible to explicitly call PARI/GP in order to perform a certain computation. Therefore, we will have access to both of the implementations which will be very useful.

In a future post, I will explain some features that are specific to SageMath and the ones that are specific to PARI/GP.
