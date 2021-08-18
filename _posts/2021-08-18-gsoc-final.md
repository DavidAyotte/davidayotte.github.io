---
title: 'Google Summer of Code 2021 summary'
date: 2021-08-18
permalink: /posts/2021/08/gsoc-final/
tags:
  - gsoc
category:
  - gsoc
author_profile: false
---

In this post, I will give an overview of my work for the SageMath open-source mathematical software. If you are new to this blog, I suggest that you start reading from the [first post](/posts/2021/05/gsoc2021/).

I will list here a brief overview of the work I did and explain the new features that will be available in SageMath in a future release of the software (probably SageMath 9.5). A more detailed list can be found [here](https://trac.sagemath.org/ticket/31560).

## Work done during the summer

* Ticket [#31559](https://trac.sagemath.org/ticket/31559): The class `ModularFormsRing` now manipulates formal object.

    The old implementation of the ring of modular forms was a little bit outdated. Now, the elements of this ring in SageMath are instances of the class `GradedModularFormElement` which inherit from the class `Element`:

    {% highlight text %}
    sage: M = ModularFormsRing(1)
    sage: f = M.0; f
    1 + 240*q + 2160*q^2 + 6720*q^3 + 17520*q^4 + 30240*q^5 + O(q^6)
    sage: type(f)
    <class 'sage.modular.modform.ring.ModularFormsRing_with_category.element_class'>
    sage: f.parent()
    Ring of Modular Forms for Modular Group SL(2,Z) over Rational Field
    {% endhighlight %}

* Ticket [#32168](https://trac.sagemath.org/ticket/32168): Fixed conversion between modular forms spaces.

    It is well known that a modular form $$f$$ of weight $$k$$, level $$N$$ and nebentypus $$\chi$$ is modular over $$\Gamma_1(N)$$. However, it was not always possible to converse a modular form from a space to a bigger space:

    {% highlight text %}
    sage: chi = DirichletGroup(13).0
    sage: Mchi = ModularForms(chi, 5); Mchi
    Modular Forms space of dimension 6, character [zeta12] and weight 5 over Cyclotomic Field of order 12 and degree 4
    sage: M = ModularForms(Gamma1(13), 5, CyclotomicField(12)); M
    Modular Forms space of dimension 34 for Congruence Subgroup Gamma1(13) of weight 5 over Cyclotomic Field of order 12 and degree 4
    sage: f = Mchi.0; f
    q + (-3*zeta12^3 - zeta12^2 + zeta12 + 3)*q^5 + O(q^6)
    sage: M(f) # bug
    Traceback (most recent call last):
    ...
    TypeError: entries must be a list of length 34
    {% endhighlight %}

This bug is now fixed:

    {% highlight text %}
    sage: f = Mchi.0; f
    q + (-3*zeta12^3 - zeta12^2 + zeta12 + 3)*q^5 + O(q^6)
    sage: f.parent()
    Modular Forms space of dimension 6, character [zeta12] and weight 5 over Cyclotomic Field of order 12 and degree 4
    sage: f = M(f); f
    q + (-3*zeta12^3 - zeta12^2 + zeta12 + 3)*q^5 + O(q^6)
    sage: f.parent() # bug fixed
    Modular Forms space of dimension 34 for Congruence Subgroup Gamma1(13) of weight 5 over Cyclotomic Field of order 12 and degree 4
    {% endhighlight %}

* Ticket [#32135](https://trac.sagemath.org/ticket/32135): implemented `to_polynomial` and `from_polynomial` for the ring of modular forms (see [this blog post](/posts/2021/06/relation/) for more info).

* Ticket [#31512](https://trac.sagemath.org/ticket/31512): implemented the ring of quasimodular forms.

    A new parent class was implemented, named `QuasiModularForms`. This class is similar to the class `ModularFormsRing`. See this [blog post](/posts/2021/05/quasimodform/) for more info about quasimodular forms.

    {% highlight text %}
    sage: QM = QuasiModularForms(1); QM
    Ring of Quasimodular Forms for Modular Group SL(2,Z) over Rational Field
    sage: E2, E4, E6 = QM.gens()
    sage: E2
    1 - 24*q - 72*q^2 - 96*q^3 - 168*q^4 - 144*q^5 + O(q^6)
    sage: E4
    1 + 240*q + 2160*q^2 + 6720*q^3 + 17520*q^4 + 30240*q^5 + O(q^6)
    sage: E6
    1 - 504*q - 16632*q^2 - 122976*q^3 - 532728*q^4 - 1575504*q^5 + O(q^6)
    sage: f = E2 * E4 + E6; f
    2 - 288*q - 20304*q^2 - 185472*q^3 - 855216*q^4 - 2697408*q^5 + O(q^6)
    sage: f.is_modular_form()
    False
    {% endhighlight %}

* Ticket [#32336](https://trac.sagemath.org/ticket/32336): implemented `to_polynomial` and `from_polynomial` for quasimodular forms.

    These two methods are similar to the ones implemented for the ring of modular forms.

    {% highlight text %}
    sage: QM = QuasiModularForms(1)
    sage: f = QM.0 + QM.1
    sage: f.to_polynomial()
    E2 + E4
    sage: P.<E2, E4, E6> = QQ[]
    sage: g = QM.from_polynomial(E2 + E4); g
    2 + 216*q + 2088*q^2 + 6624*q^3 + 17352*q^4 + 30096*q^5 + O(q^6)
    sage: f == g
    True
    {% endhighlight %}

* Ticket [#32343](https://trac.sagemath.org/ticket/32343): implemented the Serre derivative of modular forms.

    The Serre derivative of a modular form is an operator that sends a weight $$k$$ modular form to a weight $$k+2$$ modular form. It is defined by $$ f \mapsto q\frac{df}{dq} - \frac{k}{12}E_2 f$$.

    {% highlight text %}
    sage: M = ModularForms(Gamma1(7), 6); M
    Modular Forms space of dimension 13 for Congruence Subgroup Gamma1(7) of weight 6 over Rational Field
    sage: f = M.0; f
    q + O(q^6)
    sage: f.weight()
    6
    sage: F = f.serre_derivative(); F
    1/2*q + 12*q^2 + 36*q^3 + 48*q^4 + 84*q^5 + O(q^6)
    sage: F.weight()
    8
    sage: F.parent()
    Modular Forms space of dimension 17 for Congruence Subgroup Gamma1(7) of weight 8 over Rational Field
    {% endhighlight %}

* Ticket [#32357](https://trac.sagemath.org/ticket/32357): implemented derivative of quasimodular forms and graded modular forms.

    The derivative of a modular form $$f \mapsto q\tfrac{df}{dq}$$ is not necessarily a modular form. However, it is a quasimodular form. Using the Serre derivative, it was possible to implement this derivative of a graded modular form and a quasiform:

    {% highlight text %}
    sage: QM = QuasiModularForms(1)
    sage: E2, E4, E6 = QM.gens()
    sage: dE2 = E2.derivative(); dE2
    -24*q - 144*q^2 - 288*q^3 - 672*q^4 - 720*q^5 + O(q^6)
    sage: dE2 == (E2^2 - E4)/12 # Ramanujan identity
    True
    sage: dE4 = E4.derivative(); dE4
    240*q + 4320*q^2 + 20160*q^3 + 70080*q^4 + 151200*q^5 + O(q^6)
    sage: dE4 == (E2 * E4 - E6)/3 # Ramanujan identity
    True
    sage: dE6 = E6.derivative(); dE6
    -504*q - 33264*q^2 - 368928*q^3 - 2130912*q^4 - 7877520*q^5 + O(q^6)
    sage: dE6 == (E2 * E6 - E4^2)/2 # Ramanujan identity
    True
    {% endhighlight %}

* Ticket [32366](https://trac.sagemath.org/ticket/32366): implement the q-bracket.

    This ticket is still a work in progress, but its goal is to implement the q-bracket using the tools provided in the paper of Zagier: [Partitions, quasimodular forms and the Bloch-Okounkov theorem](https://people.mpim-bonn.mpg.de/zagier/files/doi/10.1007/s11139-015-9730-8/bloch-okounkov.pdf). More details about this $$q$$-bracket is given in [this post](/posts/2021/08/okounkov/).

More details about this project can be found in the task ticket [#31560](https://trac.sagemath.org/ticket/31560). This ticket list all the work done so far, and what is to be done in the future.

## Reviewed tickets

In addition to these new feature, I also reviewed some tickets. Reviewing tickets is a really important part of SageMath development as everything must be peer reviewed before being officially included in the software. In other words, no reviewers $$=$$ no new feature/bug fix. Here's the list of tickets I reviewed during the summer:

* [#30775](https://trac.sagemath.org/ticket/30775)
* [#31414](https://trac.sagemath.org/ticket/31414)
* [#31775](https://trac.sagemath.org/ticket/31775)
* [#32165](https://trac.sagemath.org/ticket/32165)
* [#32193](https://trac.sagemath.org/ticket/32193)
* [#32195](https://trac.sagemath.org/ticket/32195)
* [#32316](https://trac.sagemath.org/ticket/32316)
* [#32350](https://trac.sagemath.org/ticket/32350)

## Last words

The GSoC 2021 program is already finished and it was an enriching and captivating experience. I perfected my Python programming skills while working on a subject that I'm passionated about: modular forms. Moreover, I thoroughly enjoyed working in collaboration with my mentor as I learned a lot from him. Thank you for reading this blog. If you have any questions or suggestions do not hesitate to contact me via email.
