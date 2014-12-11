PEP 457: Syntax For Positional-Only Parameters
===============

### by Zachary Doyle, Niklas Fejes, Samuel Carnes
### December 8th, 2014

## Introduction
This PEP (Python Enhancement Proposal) proposes a change in how the syntax for parameter passing is
defined, in a way such that documentation can be expressed more consistently, and such that
the language allows positional-only parameters.

One of the important ideas of the proposal is that:
> currently in the documentation there's no way to tell whether a function takes positional-only parameters. 

Note that while it is possible to simulate positional-only parameters, one has to implement the argument parsing and the function signature does not tell what arguments are accepted.

## Proposal info
* https://www.python.org/dev/peps/pep-0457/
* Last updated 2013-10-09
* Larry Hastings <larry at hastings.org>
* Status: draft

The original PEP was proposed at October 8 2013 by Larry Hasting and has been 

## Motivation
In Python there are different semantics for function parameters declaration. This can be confusing for the programmer, especially when the documentation specifies two or more different versions of the same function that takes different numbers of arguments. This can be confusing because the actual implementation must be made as one single function, so if the programmer looks at the source code for the function, the parameters will most likely not match the ones in the documentation.

To cite the PEP,
> * Documentation can clearly, unambiguously, and consistently express exactly how the arguments for a function will be interpreted.
* The syntax is reserved for future use, in case the community decides someday to add positional-only parameters to the language.
* Argument Clinic can use a variant of the syntax as part of its input when defining the arguments for built-in functions.


As an example, the `range()` function is documented with the signatures
```python
range(stop)
range(start, stop[, step])
```
which with the new syntax would be
```python
range([start,] stop, [step,] /)
```

One important aspect to note here is that this syntax is ambiguous, so the leftmost optional parameter (`[start,]`) would be preferred over the right optional parameter (`[step,]`). However, if we specify multiple arguments in the optional group we can create more complex parameter passings. An example of this is the `window.addch()` function which is currently documented with the two following function signatures:
```python
addch(ch[, attr])
addch(y, x, ch[, attr])
```
With PEP 457, the documentation for this function could be exactly the same as the function definiton, namely:
```python
def addch([y, x,] ch, [attr,] /):
```

As opposed to the `range()` function, this parameterization is not ambiguous, but it shows one of the biggest advantages of this syntax. The right optional argument group is chosen if 2 arguments are given; the groups are determined by the number of arguments and in case of ambiguity the leftmost argument is used.


## Positional only parameters
Currently the way parameter passing works (not including the `*args` and `**kwargs` syntax), the way keyword-mode is specified is:
```python
def name(positional_or_keyword_parameters, *, keyword_only_parameters):
```
This allows the user to specify all parameters as keywords, which might not always be optimal. In PEP 457 this issue is addressed by adding the `/` parameter separator, where the preceding parameter are positional only and can not be keyworded. The new syntax would be as follows.
```python
def name(positional_only_parameters, /, positional_or_keyword_parameters,
         *, keyword_only_parameters):
```

There are a few cases when positional only parameters are needed not only to ensure that the user puts the parameters in the order specified in the function definition. Most importantly, the optional parameter syntax requires the the parameters can not be keyworded. As an example, how would the code below be interpreted?
```python
def foo([a, b,] c, [d,]):
    ...
foo(1,2,3,b=4)
```

# References
1.  [PEP 457](https://www.python.org/dev/peps/pep-0457)
2.  [Python-ideas: keyword arguments everywhere (stdlib) - issue8706 (Guido van Rossum) ](https://mail.python.org/pipermail/python-ideas/2012-March/014364.html)
3.  [Python-ideas: keyword arguments everywhere (stdlib) - issue8706 (Guido van Rossum) ](https://mail.python.org/pipermail/python-ideas/2012-March/014378.html)
4.  [Python-ideas: keyword arguments everywhere (stdlib) - issue8706 (Guido van Rossum) ](https://mail.python.org/pipermail/python-ideas/2012-March/014417.html)
5.  [Python 2 documentation](https://docs.python.org/2/)
5.  [Python 3 documentation](https://docs.python.org/3/)
