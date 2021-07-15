---
title: 'How to Write a Modular Form In Terms of a Set of Generators?'
date: 2021-07-14
permalink: /posts/2021/06/relation/
tags:
  - gsoc
  - modform
  - math
category:
  - gsoc
---

In this post, I will explain an algorithmic way to write any modular form $$f \in \mathcal{M}_*(\Gamma)$$ as a polynomial in the generators of the graded ring $$\mathcal{M}_*(\Gamma)$$ ($$\Gamma = \Gamma_0(N), \Gamma_1(N)$$ or $$\mathrm{SL}_2(\mathbb{Z})$$).

First it is important to mention that for any congruence subgroup $$\Gamma \leq \mathrm{SL}_2(\mathbb{Z})$$, the graded ring of modular forms $$\mathcal{M}_*(\Gamma)$$ is finitely generated (but not necessarily freely). This is not trivial and it was proven in the paper *Les schémas de modules de courbes elliptiques* [[DR73](https://www.springer.com/gp/book/9783540065586), Thm 3.4]. In fact, they prove that it is finitely generated as a $$\mathbb{Z}$$-algebra.

Let's start with an (easy) example. Consider the weight 12 normalized Eisenstein serie for $$\mathrm{SL}_2(\mathbb{Z})$$ denoted $$E_{12}$$ (by "normalized" I mean that the first Fourier coefficient is $$1$$). This modular form lives in the graded ring of modular forms for the full modular group:

$$
E_{12} \in \mathcal{M}_*(\mathrm{SL}_2(\mathbb{Z})) \cong \mathbb{C}[E_4, E_6].
$$

Thus, there exist a unique polynomial $$P(X, Y) \in \mathbb{C}[X, Y]$$ such that

$$
\label{poly}
E_{12} = P(E_4, E_6)
$$

Next, we claim that $$P$$ must be homogeneous of weight $$12$$ in $$E_4$$ and $$E_6$$. In other words, if $$X = x^4$$ and $$Y = y^6$$ then $$P(x^4, y^6)$$ must be of degree $$12$$ and respect the following relation: $$P((\lambda x)^4, (\lambda y)^6) = \lambda^{12}P(x^4, y^6)$$ for every $$\lambda\in \mathbb{C}$$. This is simply because $$E_{12}$$ is of weight $$12$$ and the product of two modular forms of weight $$k_1$$ and $$k_2$$ respectively is a modular form of weight $$k_1 + k_2$$. Since the only homogeneous monomials of weight $$12$$ are $$E_4^3$$ and $$E_6^2$$ the problem reduces to finding two complex numbers $$a$$ and $$b$$ such that

$$
\label{E12_pol}
E_{12} = a E_4^3 + b E_6^2.
$$

To determine $$a$$ and $$b$$, we will need a tool called the *Sturm bound* of a modular forms space. This bound tell us that a modular form is determined only by a finite number of coefficients in its $$q$$-expansion. Here's the general statement:

## Theorem.
Let $$f\in \mathcal{M}_k(\mathrm{SL}_2(\mathbb{Z}))$$ with $$q$$-expansion $$\sum_{n\geq 0} a_n q^n$$. Suppose that $$a_i = 0$$ for $$0 \leq i \leq \lfloor k/12 \rfloor$$. Then $$f = 0$$. The number $$\mathrm{SB}_k := \lfloor k/12 \rfloor$$ is called the *Sturm bound* of $$\mathcal{M}_k(\mathrm{SL}_2(\mathbb{Z}))$$

This theorem is a corollary of the valence formula. Moreover, a more general version for congruences subgroups also exist (see for example this [page](http://www.lmfdb.org/knowledge/show/cmf.sturm_bound))

Using the Sturm bound, we can now solve our initial problem by following these steps:

1. Compute the sturm bound of $$\mathcal{M}_{12}(\mathrm{SL}_2(\mathbb{Z}))$$:
$$
~~ \mathrm{SB}_{12} = \lfloor 12/12 \rfloor = 1
$$
2. Compute the $$q$$-expansion of $$E_{12}$$, $$E_4^3$$ and $$E_6^2$$ up to precision $$q^{\mathrm{SB}_{12}+1}$$:

   $$
   E_{12} =  1 + \frac{65520}{691}q + O(q^2);
   $$

   $$
   E_4^3 = 1 + 720q + O(q^2);
   $$

   $$
   E_6^2 = 1 - 1008q + O(q^2).
   $$
3. Solve the resulting linear system given by $$ C_{E_{12}} = a C_{E_{4}^3} + b C_{E_6^2}$$ where $$C_{E_k^n}$$ is the vector formed by the coefficients of $$E_k^n$$ up to precision $$q^{\mathrm{SB}_{12}+1}$$:

   $$
   \begin{pmatrix}
   1 \\
   \frac{65520}{691}
   \end{pmatrix}
   =
   \begin{pmatrix}
   1 & 1 \\
   720 & -1008
   \end{pmatrix}
   \begin{pmatrix}
   a \\
   b
   \end{pmatrix}
   $$

The unique solution to this linear system is given by $$a = 441/691$$ and $$b = 250/691$$. Therefore, the solution to our initial problem is:

$$
E_{12} = \frac{441}{691}E_{4}^3 + \frac{250}{691} E_{6}^{2}
$$

The same approach can be used for any given modular forms $$f\in \mathcal{M}_k$$. An advantage of this method is that it is computational in the sense that it can be implemented into a computer program.

## Sagemath Implementation

The algorithm showcased in the example above was implemented into SageMath. The idea was to create two new methods: `to_polynomial` and `from_polynomial`. The first method takes a `GradedModularFormElement` and returns its polynomial form:

{% highlight text %}
sage: M = ModularFormsRing(1)
sage: e12 = EisensteinForms(1, 12).0
sage: E12 = M(e12)  # the form must be of type GradedModularFormElement
sage: E12.to_polynomial('E4, E6')
441/691*E4^3 + 250/691*E6^2
sage: f = ModularForms(1, 40).0
sage: M(f).to_polynomial()
463/5308416*E4^10 + 1811/5308416*E4^7*E6^2 - 1939/5308416*E4^4*E6^4 - 335/5308416*E4*E6^6
{% endhighlight %}

The second method takes a polynomial and returns the associated `GradedModularFormElement`:

{% highlight text %}
sage: M = ModularFormsRing(1)
sage: P.<E4, E6> = M.polynomial_ring()
sage: M.from_polynomial(E4)
1 + 240*q + 2160*q^2 + 6720*q^3 + 17520*q^4 + 30240*q^5 + O(q^6)
sage: M.from_polynomial(E6)
1 - 504*q - 16632*q^2 - 122976*q^3 - 532728*q^4 - 1575504*q^5 + O(q^6)
sage: M.from_polynomial(441/691*E4^3 + 250/691*E6^2)
1 + 65520/691*q + 134250480/691*q^2 + 11606736960/691*q^3 + 274945048560/691*q^4 + 3199218815520/691*q^5 + O(q^6)
sage: M.from_polynomial(441/691*E4^3 + 250/691*E6^2).to_polynomial('E4, E6')
441/691*E4^3 + 250/691*E6^2
{% endhighlight %}

The implementation also works for congruence subgroups as SageMath knows how to compute the generators for these rings.

{% highlight text %}
sage: M = ModularFormsRing(Gamma0(6))
sage: M.gen_forms()
[1 + 24*q^3 + O(q^6),
 q + 5*q^3 - 2*q^4 + 6*q^5 + O(q^6),
 q^2 - 2*q^3 + 3*q^4 + O(q^6)]
sage: P.<g1, g2, g3> = M.polynomial_ring()
sage: M.from_polynomial(g1)
1 + 24*q^3 + O(q^6)
sage: M.from_polynomial(g2)
q + 5*q^3 - 2*q^4 + 6*q^5 + O(q^6)
sage: M.from_polynomial(g3)
q^2 - 2*q^3 + 3*q^4 + O(q^6)
sage: f = ModularForms(Gamma0(6), 8).0
sage: M(f).to_polynomial('g')
g0^3*g1 - 5*g0^2*g1*g2 - 80*g0^2*g2^2 - 286*g0*g1*g2^2 - 324*g0*g2^3 + 2664*g1*g2^3 + 6648*g2^4
{% endhighlight %}

It is important to note here that, in the case where the group is not $$\mathrm{SL}_2(\mathbb{Z})$$, then there might be some relations between the generators. So the methods `to_polynomial` and `from_polynomial` are not necessarily the inverses of each other. This is illustrated by this example:

{% highlight text %}
sage: M = ModularFormsRing(Gamma0(6))
sage: P.<g0, g1, g2> = M.polynomial_ring()
sage: M.from_polynomial(g1^2).to_polynomial()
g0*g2 + 2*g1*g2 + 11*g2^2
{% endhighlight %}

## Source Code

Here is the SageMath implemenation of the method `to_polynomial`:

{% highlight python %}
    def _homogeneous_to_polynomial(self, names, gens):
        r"""
        If ``self`` is a homogeneous form, return a polynomial `P(x_0,..., x_n)` corresponding to ``self``.
        Each variable `x_i` of the returned polynomial correspond to a generator `g_i` of the
        list ``gens`` (following the order of the list)

        INPUT:

        - ``names`` -- a list or tuple of names (strings), or a comma separated string;
        - ``gens`` -- (list) a list of generator of ``self``.

        OUTPUT: A polynomial in the variables ``names``
        """
        M = self.parent()
        k = self.weight() #only if self is homogeneous
        poly_parent = M.polynomial_ring(names, gens)
        monomials = M._monomials_of_weight(k, gens, poly_parent)

        # initialize the matrix of coefficients
        matrix_data = []
        for f in monomials.values():
            matrix_data.append(f[k].coefficients(range(0,f[k].parent().sturm_bound())))
        mat = Matrix(matrix_data).transpose()

        # initialize the column vector of the coefficients of self
        coef_self = vector(self[k].coefficients(range(0, self[k].parent().sturm_bound()))).column()

        # solve the linear system: mat * X = coef_self
        soln = mat.solve_right(coef_self)

        # initialize the polynomial associated to self
        itr = 0
        poly = 0
        for p in monomials.keys():
            poly += soln[itr, 0]*p
            itr += 1
        return poly

    def to_polynomial(self, names='x', gens=None):
        r"""
        Return a polynomial `P(x_0,..., x_n)` such that `P(g_0,..., g_n)` is equal to ``self``
        where `g_0, ..., g_n` is a list of generators of the parent.

        INPUT:

        - ``names`` -- a list or tuple of names (strings), or a comma separated string. Correspond
          to the names of the variables;
        - ``gens`` -- (default: None) a list of generator of the parent of ``self``. If set to ``None``,
          the list returned by :meth:`~sage.modular.modform.find_generator.ModularFormsRing.gen_forms`
          is used instead

        OUTPUT: A polynomial in the variables ``names``
        """
        M = self.parent()
        if gens is None:
            gens = M.gen_forms()

        # sum the polynomial of each homogeneous part
        return sum(M(self[k])._homogeneous_to_polynomial(names, gens) for k in self.weights_list())
{% endhighlight %}
