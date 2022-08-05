---
permalink: /research.html
title: "My research"
excerpt: "About my research"
author_profile: true
---

<!-- Currently, my Ph. D. research is on the arithmetic aspects of Drinfeld modular forms. Let $$A = \mathbb{F}_q[T]$$ and $$K = \mathrm{Frac}(A)$$. Let $$K_{\infty}$$ be the completion of $$K$$ at $$1/T$$ and we let $$\mathbb{C}_{\infty}$$ be the completion of an algebraic closure of $$K_{\infty}$$. A *Drinfeld modular forms* is a rigid analytic function defined over the Drinfeld upper half plane $$f:\Omega \rightarrow C$$ such that

$$
f\left( \frac{az+b}{cz+d} \right) = (\mathrm{det}\gamma)^{-m}(cz+d)^k f(z)
$$

where $$a,b,c,d\in A = \mathbb{F}_q[T], ~ ad-bc \neq 0$$, $$k$$ and $$m$$ are two integers. There is also a holomorphic condition at infinity similar to the classical case, that is $$f$$ must satisfy an expansion of the form

$$
f(z) = \sum_{n\geq 0} c_n t(z)^n,
$$
where $$t(z)$$ is some function playing the role of parameter at infinity.

Computationnally speaking, -->

I love mathematics with a hint of computer experiments. In this page, I explain briefly some projects I worked on in the past.

Currently, my Ph. D. research is on the arithmetic aspects of Drinfeld modules and Drinfeld modular forms. These objects are analogues of elliptic curves and classical modular forms respectively but for global field of finite characteristic. I'm currently developing a SageMath package named [drinfeld_modules](https://github.com/DavidAyotte/drinfeld_modules) allowing computations with these objects.

During the summer 2021, I participated in the program [Google Summer of Code](https://summerofcode.withgoogle.com/) where I worked for an open source organization called [SageMath](https://www.sagemath.org/). SageMath is a mathematical software built on top of multiple already existing open-source computing software (such as NumPy, SciPi, matplotlib, etc). The goal of my project was to implement the graded ring of quasimodular forms into the software. I blogged about my journey here on my website (see the section [GSoC](/gsoc-blog/)).

In the past, for my [master thesis](http://hdl.handle.net/20.500.11794/37368), I worked on modular forms and their links with mysterious algebraic objects, such as the class number of a number field. In particular, I studied dihedral congruences for the coefficients of modular forms and wrote some PARI/gp scripts for my experiments ([scripts](https://github.com/DavidAyotte/sym2-dihedral)).

## Publication

* Ayotte, D., Lei, A. & Rondy-Turcotte, JC. On the parity of supersingular Weil polynomials. Arch. Math. 106, 345–353 (2016).
