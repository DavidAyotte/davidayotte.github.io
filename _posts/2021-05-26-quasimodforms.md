---
title: 'A quick introduction to the theory of quasimodular forms'
date: 2021-05-26
permalink: /posts/2021/05/quasimodform/
tags:
  - gsoc
  - modform
  - math
---

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

## Definition.
A *weak modular form of weight $$k$$ for $$\mathrm{SL}_2(\mathbb{Z})$$* is an holomorphic function $$f:\mathbb{H} \rightarrow \mathbb{C}$$ such that

$$
\label{eq_mod_form}
f\left( \frac{az+b}{cz+d}\right) = (cz+d)^k f(z)
$$

for every $$z\in \mathbb{H}$$ and every $$\bigl(\begin{smallmatrix} a & b\\ c & d \end{smallmatrix}\bigr) \in \mathrm{SL}_2(\mathbb{Z})$$. This invariance property is called the *modularity condition*. $$\diamond$$

If we define the operator $$f\mid_k\gamma(z) := (cz+d)^{-k} f(\gamma z) $$, then the notion of being weakly holomorphic is the same as being $$\mathrm{SL}_2(\mathbb{Z})$$-invariant under the $$\mid_k$$ operator.

By equation (\ref{eq_mod_form}), we deduce that $$f$$ is a periodic function $$f(z+1) = f(z)$$ (check the modularity condition with the matrix $$\bigl(\begin{smallmatrix} 1 & 1\\ 0 & 1 \end{smallmatrix}\bigr)$$. Thus, $$f$$ possesses a Fourier expansion:

$$
\label{eq_fourier}
f(z) = \sum_{n=-\infty}^{\infty} a_n(f) q^n, \qquad q:=e^{2i\pi z}.
$$

## Definition.
A *modular form of weight $$k$$ for $$\mathrm{SL}_2(\mathbb{Z})$$* is a weak modular form such that $$ a_n(f) = 0$$ for every $$ n<0 $$. Furthermore, if $$a_0(f) = 0$$, then $$f$$ is called a *cusp form*. The expansion (\ref{eq_fourier}) of a modular form is called its *$$q$$-expansion*. $$\diamond$$

The main example one encounter when studying modular forms is the *weight-$$k$$ Eisenstein series* which is defined by:

$$
\label{eq_eis_ser}
G_{k}(z) := \sum_{\substack{ c,d\in \mathbb{Z} \\ (c,d) \neq (0,0) }} \frac{1}{(cz +d)^{k}}.
$$

It turns out that if $$k\geq 4$$ is even, then $$G_k$$ is absolutely and uniformely convergent for $$z\in \mathbb{H}$$ and defines a modular form with $$q$$-expansion

$$
G_k(z) = 2\zeta(k) + 2\frac{(2\pi i)^k}{(k-1)!} \sum_{n=1}^{\infty} \sigma_{k-1}(n)q^n
$$

where $$\zeta(s) := \sum_{n=1}^{\infty}\tfrac{1}{n^s}$$ is the *Riemann Zeta function* and $$\sigma_k(n):=\sum_{d\mid n}d^k$$. From here, one question arises: what happens if we take $$k=2$$? The answer is that we would loose the absolute convergence of (\ref{eq_eis_ser}). However, we can still choose a specific arrangement of the sum and define

$$
G_2(z) := \sum_{c\in \mathbb{Z}} \sum_{d\in \mathbb{Z}_c} \frac{1}{(cz+d)^2}
$$

where

$$
\mathbb{Z}_c = \begin{cases}
\mathbb{Z}\setminus \lbrace 0 \rbrace, & \text{ if }c = 0\\
\mathbb{Z}, & \text{ otherwise.}

\end{cases}
$$

Using this definition we it is possible to show that

$$
G_2(z) = 2\zeta(2) - 8 \pi^2 \sum_{n=1}^{\infty} \sigma_1(n) q^n.
$$

Moreover, we have that

$$
G_2|_2\gamma(z) = G_2(z) - \frac{2\pi i c}{cz+d}
$$

and so we observe that $$G_2$$ is not quite a modular form but almost, it is so close of being a modular form that we call it a *quasimodular form*.