---
title: 'The Graded Ring of Modular Forms in SageMath'
date: 2021-06-21
permalink: /posts/2021/06/modformsage/
tags:
  - gsoc
  - modform
  - math
category:
  - gsoc
---

The goal of this post is to go over some of the new features that are currently in developement for the graded ring of modular forms in SageMath.

Currently in SageMath (version 9.3), it is possible to create the graded ring of modular forms using the class [ModularFormRing](https://doc.sagemath.org/html/en/reference/modfrm/sage/modular/modform/find_generators.html):

{% highlight Text %}
sage: M = ModularFormsRing(1); M
Ring of modular forms for Modular Group SL(2,Z) with coefficients in Rational Field
{% endhighlight %}

However, this class does not follows the parent structure offered by SageMath. This feature is useful when implementing a new algebraic structure in SageMath and I will explain why here. Some knowledge of the python programming language will be assumed.

## Some mathematics

First of all, the graded ring of modular forms is the set of all modular forms for a given group. More precisely, it consist of the direct sum

$$
\mathcal{M}_*(\Gamma) := \bigoplus_{k\in \mathbb{Z}} \mathcal{M}_k(\Gamma),\quad \Gamma \leq \mathrm{SL}_2(\mathbb{Z}).
$$

Note that we have

$$
\mathcal{M}_{k_1}(\Gamma) \mathcal{M}_{k_2}(\Gamma) \subseteq \mathcal{M}_{k_1+k_2}(\Gamma), k_1, k_2\in \mathbb{Z}.
$$

We will view an element $$F$$ of $$\mathcal{M}_*(\Gamma)$$ as a sum

$$
F = f_0 + f_1 + f_2 + \ldots + f_r, \quad r\geq 0,
$$

where each $$f_k$$ is a weight $$k$$ modular forms. Such an element $$F$$ will be called a *graded modular form* (or sometimes simply a *graded form*). Note that it is not garanteed that a graded form is a modular form as it is not always invariant under the slash-$$k$$ operator. For example, take the function

$$
F := E_4 + E_6
$$

where $$E_4$$ and $$E_6$$ are the weight $$4$$ and $$6$$ normalized Eisenstein series. 

Given a graded form $$F = f_0 + f_1 + f_2 + \ldots + f_r$$, each modular forms $$f_k$$ will be called the *weight $$k$$ homogeneous component of $$F$$*.

## Some programming

In SageMath, there is a notion of *elements* and *parents*. In short, an *element* is a member contained in a structure called *parent*. An example of a parent in SageMath is the ring of integer $$\mathbb{Z}$$ with elements the integers. To implement a new parent structure in SageMath, one need to define a class that inherit of the subclass [Parent](https://doc.sagemath.org/html/en/reference/structure/sage/structure/parent.html). This was not the case for the initial implementation of the class ModularFormRing.

Moreover, when implementing a new structure, one needs to also define a new class representing the elements of our parent class. This class of elements must inherit of the subclass [Element](https://doc.sagemath.org/html/en/reference/structure/sage/structure/element.html). The base methods of this subclass provides useful features. For example, when implemented correctly, it is possible to define the classical operations (addition, multiplication,...) between our elements. 

Implementing this Parent/Element framework is exactly what we want to do here with the class `ModularFormsRing`.

### New features

It is now possible to work with a graded modular forms element:

{% highlight text %}
sage: M = ModularFormRing(1)
sage: e4 = ModularForms(1,4).0 #Weight 4 eisenstein serie
sage: e4
1 + 240*q + 2160*q^2 + 6720*q^3 + 17520*q^4 + 30240*q^5 + O(q^6)
sage: e4.parent()
Modular Forms space of dimension 1 for Modular Group SL(2,Z) of weight 4 over Rational Field
sage: E4 = M(e4); F
1 + 240*q + 2160*q^2 + 6720*q^3 + 17520*q^4 + 30240*q^5 + O(q^6)
sage: E4.parent()
Ring of modular forms for Modular Group SL(2,Z) with coefficients in Rational Field
{% endhighlight %}

We observe here that `E4` and `e4` are the same mathematical object but does not lives in the same parent. An interaction between these two parents can be implemented and this is what we call *coercion*. This will be explained a little bit later in the current post.

Here's an overview of what was done in order to make the above example works:

* Make the class `ModularFormRing` inherit from `Parent`;
* Define a specific element class (`GradedModularFormElement`);
* Define the `_element_constructor_` method;
* Define coercions between `ModularFormSpace` and `ModularFormRing`

Below is a summary of the class `ModularFormRing` with its `_element_constructor_` method.

{% highlight python %}
class ModularFormsRing(Parent):
    
    Element = GradedModularFormElement

    def __init__(self, group, base_ring=QQ):
        r"""
        The ring of modular forms (of weights 0 or at least 2) for a congruence
        subgroup of `{\rm SL}_2(\ZZ)`, with coefficients in a specified base ring.

        INPUT:

        - ``group`` -- a congruence subgroup of `{\rm SL}_2(\ZZ)`, or a
          positive integer `N` (interpreted as `\Gamma_0(N)`)

        - ``base_ring`` (ring, default: `\QQ`) -- a base ring, which should be
          `\QQ`, `\ZZ`, or the integers mod `p` for some prime `p`.

        [...]

        """
        if isinstance(group, (int, Integer)):
            group = Gamma0(group)
        elif not is_CongruenceSubgroup(group):
            raise ValueError("Group (=%s) should be a congruence subgroup" % group)

        if base_ring != ZZ and not base_ring.is_field() and not base_ring.is_finite():
            raise ValueError("Base ring (=%s) should be QQ, ZZ or a finite prime field" % base_ring)

        self.__group = group
        self.__base_ring = base_ring
        self.__cached_maxweight = ZZ(-1)
        self.__cached_gens = []
        self.__cached_cusp_maxweight = ZZ(-1)
        self.__cached_cusp_gens = []
        Parent.__init__(self, base=base_ring, category=GradedAlgebras(base_ring))

    [...]

    def _element_constructor_(self, f):
        if isinstance(f, GradedModularFormElement):
            mf_dict = f._dictionnary()
        if is_ModularFormElement(f):
            if f.group() == self.group() and self.base_ring().has_coerce_map_from(f.base_ring()):
                mf_dict = {f.weight():f}
            else:
                raise ValueError('The modular form must have the same group and the same base ring as self')
        if f in self.base_ring():
            mf_dict = {0:f}
        return GradedModularFormElement(self, mf_dict)
{% endhighlight %}

Here is a brief summary of the element class `GradedModularFormElement`:

{% highlight python %}
class GradedModularFormElement(Element):
    def __init__(self, parent, mf_dict):
        r"""
        An element of a graded ring of modular forms.

        INPUTS:

            - ``parents`` - an object of the class ModularFormsRing
            - ``mf_dict`` - a dictionary ``{k_1:f_1, k_2:f_2, ..., k_n:f_n}`` where `f_i` is a modular form of weight `k_i`
        
        OUTPUT:

        A GradedModularFormElement corresponding to `f_1 + f_2 + ... f_n`
        """
        self.__mf_dict = mf_dict
        Element.__init__(self, parent)
{% endhighlight %}

We have chosen here to represent a graded modular form as a dictionnary because this makes it easier to access the weight-$$k$$ component of the graded form (keys = weights, values = modular forms). 

Now that the Parent/Element framework is implemented, it is also desirable to define operation between two `GradedModularFormElement`. This is done by defining the three special methods: `_add_`, `_neg_` and `_mul_`.

{% highlight python %}
    def _add_(self, other):
        r"""
        Addition of two ``GradedModularFormElement``.
        """
        GM = self.__class__
        f_self = self._dictionnary()
        f_other = other._dictionnary()
        f_sum = { k : self.__getitem__(k) + other[k] for k in set(f_self) | set(f_other) }
        return GM(self.parent(), f_sum)

    def _neg_(self):
        r"""
        The negation of self.
        """
        GM = self.__class__
        minus_self = {k:-self._dictionnary()[k] for k in self._dictionnary()}
        return GM(self.parent(), minus_self)

    def _mul_(self, other):
        r"""
        Multiplication of two ``GradedModularFormElement``.
        """
        GM = self.__class__
        f_self = self._dictionnary()
        f_other = other._dictionnary()
        f_mul = {}
        for k_self in f_self.keys():
            for k_other in f_other.keys():
                f_mul[k_self + k_other] = f_self[k_self]*f_other[k_other]
        return GM(self.parent(), f_mul)
{% endhighlight %}

These three methods provides now the possibility of adding, substracting and multiplying two graded forms in SageMath:

{% highlight text %}
sage: M = ModularFormsRing(1)
sage: e4 = ModularForms(1,4).0 #weight 4 eisenstein serie
sage: e6 = ModularForms(1,6).0 #weight 6 eisenstein serie
sage: E4 = M(e4); #GradedModularFormElement
sage: E6 = M(e6); #GradedModularFormElement
sage: E4 + E6
2 - 264*q - 14472*q^2 - 116256*q^3 - 515208*q^4 - 1545264*q^5 + O(q^6)
sage:(E4 + E6).parent()
Ring of modular forms for Modular Group SL(2,Z) with coefficients in Rational Field
{% endhighlight %}

For the last part of this post, I will cover briefly the notion of "coercion" which is an important part of this implementation. Thanks to the coercion framework in SageMath, it is possible to implement some interactions between two different parents. For example, one could want to perform an operation between a `ModularFormElement` and a `GradedModularFormElement`.

{% highlight text %}
sage: M = ModularFormsRing(1)
sage: e4 = ModularForms(1,4).0 #ModularFormElement
sage: e6 = ModularForms(1,6).0 #ModularFormElement
sage: E4 = M(e4); #GradedModularFormElement
sage: E6 = M(e6); #GradedModularFormElement
sage: E4 + e6 + e4 + E6
4 - 528*q - 28944*q^2 - 232512*q^3 - 1030416*q^4 - 3090528*q^5 + O(q^6)
sage: (E4 + e6 + e4 + E6).parent()
Ring of modular forms for Modular Group SL(2,Z) with coefficients in Rational Field
{% endhighlight %}

To implement this useful feature it is sufficient to implement the method `_coerce_map_from_` to the class `ModularFormRing` and the coercion framework of SageMath will take care of the rest.

{% highlight python %}
    def _coerce_map_from_(self, M):
        if is_ModularFormsSpace(M):
            if M.group() == self.group() and self.has_coerce_map_from(M.base_ring()):
                return True
        if self.base_ring().has_coerce_map_from(M):
            return True
        return False
{% endhighlight %}

Finally, in the near future, I plan to implement the "pushout" of two different `ModularFormsSpace`. In other words, this functionality would add the possibility to sum two `ModularFormElement` of different weight and create directly a `GradedModularFormElement`. For example, we currently (v. 9.3) have the following behavior:

{% highlight text %}
sage: M = ModularFormsRing(1)
sage: e4 = ModularForms(1,4).0 #ModularFormElement
sage: e6 = ModularForms(1,6).0 #ModularFormElement
sage e4 + e6
Traceback (most recent call last)
...
TypeError: unsupported operand parent(s) for +: 'Modular Forms space of dimension 1 for Modular Group
SL(2,Z) of weight 4 over Rational Field' and 'Modular Forms space of dimension 1 for Modular Group
SL(2,Z) of weight 6 over Rational Field'
{% endhighlight %}

Instead of an error here, SageMath would return a `GradedModularFormElement`. This feature is still under developpement.

Thanks for reading!