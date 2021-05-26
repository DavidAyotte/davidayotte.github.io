---
title: 'A quick introduction to the theory of quasimodular forms'
date: 2021-05-26
permalink: /posts/2021/05/quasimodform/
tags:
  - gsoc
  - modform
  - math
---

<!-- ---
layout: post
title: What is a quasimodular form?
date: 2021-05-26
description: A quick introduction to the theory of quasimodular forms
--- -->

In this post, I shall explain what is a quasimodular forms, which are the main mathematical objects for this GSoC project.

Basic knowledge of classical modular forms will be assumed throughougt this text, but let's recall the definition. First we define an action of the group of matrices with coefficient in $$\mathbb{Z}$$, denoted $$\mathrm{SL}_2(\mathbb{Z})$$, on the upper half plane $$\mathbb{H} := \lbrace z \in \mathbb{C} : \mathrm{im}(z)>0 \rbrace$$ by

$$
\begin{pmatrix}
a & b \\
c & d
\end{pmatrix}
z
:=
\frac{az+b}{cz+d}
$$

One can indeed check that this defines a left action. Using this action, we can define a right action on the set of holomorphic functions 

#### Definition.