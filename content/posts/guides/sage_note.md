---
author: "mechfrog88"
title: "SageMath guide"
date: "2021-12-5"
description: "SageMath basic usage guide"
tags: ["guides"]
math: true
ShowBreadcrumbs: False
---

# SageMath Installation

Windows : https://www.sagemath.org/download-windows.html

Mac : https://www.sagemath.org/download-mac.html

Linux : https://www.sagemath.org/download-linux.html

As the whole package is around 10GB, it takes quite some time to install the whole package.

## Running Sage IDE

After the installation, run sagemath in terminal by typing `sage`

```console
$ sage
┌────────────────────────────────────────────────────────────────────┐
│ SageMath version 9.4, Release Date: 2021-08-22                     │
│ Using Python 3.9.5. Type "help()" for help.                        │
└────────────────────────────────────────────────────────────────────┘
sage:
```

## Color Scheme

You can change the color scheme by typing the command

```console
sage: %colors Linux
```

Valid schemes: `['NoColor', 'Linux', 'LightBG', 'Neutral', '']`

I find the `Linux` color scheme most suitable dark background terminal.

## Running Sage from file

You can run a sage file from the terminal with

`$ sage test.sage`

Or a python file with

In the python file, import sage by

```python
from sage.all import *
...
```

Then run it with this command

`$ sage -python test.py`

# SageMath syntax

Althought SageMath uses syntax very identical to python, there are still some subtle difference between them.

The official documentation often uses syntax that is supported only in `.sage` file.

Common syntax difference
| Behavior                 | Sage               | Python            |
| -------------------------|--------------------|-------------------|
| Exponent                 | ^                  | **                |
| XOR                      | ^^                 | ^                 |
| Polynomial               | `R.<x> = QQ[]`     | `QQ['x']`         |
| Multivariable Polynomial | `R.<x,y,z> = QQ[]` | `QQ['x','y','z']` |

Most of the syntax are supported in both files

The best part of SageMath is that it provides simple syntax for many complex mathematical operations.

# Ring and Field

First, one must identify these symbols

| Symbol | Meaning               | Type |
|--------|-----------------------|------|
| ZZ     | Integers              |Ring  |
| QQ     | Rational Numbers      |Field |
| RR     | Real Numbers          |Field |
| CC     | Complex Numbers       |Field |
| Zmod(N)| Integer Modulo N      |Ring  |
| GF(N)  | Finite Field of size N|Field |

Note that for `GF(N)` the N must be $p^a$ where p is a prime.

The main differences between a ring and a field is

1. Not all non-zero element in a Ring has an inverse.
2. Ring might has zero divisor.

Take integer modulo 6 ring as an example

1. The number 3 does not have inverse. There is no such number $a$ such that $3 \cdot a \equiv 1 \mod 6$
2. The number 3 and 2 is a zero divisor. $3 \cdot 2 \equiv 0 \mod 6$ 

You need to be very careful for the choice of your Ring/Field as some functions are only valid for field but not for ring.

In general, field are easier to work with as it has more properties. Most of the function that works for ring will work for field.

Therefore, if you are dealing with integer modulo a prime number, you should use `GF(p)` instead of `Zmod(p)`

Once you convert a number to `GF(p)` or `Zmod(p)`, you don't have to keep applying `%` operator as the modulo operation will already be done automatically.

```
sage: a = 63283
sage: b = 45342
sage: a = GF(17)(a)
sage: b = GF(17)(b)
sage: a + b
12
sage: a / b
3
sage: a^-1
2
sage: a^30
4
```

Operation `+, -, *, ^` are well defined for Rings and Field.

Opeartion `/` can be use if the element has an inverse

Factor a number with the `factor()` function

```python
sage: a = 63283
sage: a.factor()
11^2 * 523
```

# Polynomial

## Univariate Polynomial

Recall the syntax to declare a polynomial is `R.<x> = QQ[]`

`R` represents the Polynomial Ring

`x` represents the variable

`QQ` represents the ring for the coefficient of the polynomial.

Let's say you want to declare a polynomial such that the coefficient can only be an integer.

Then it will be `R.<x> = ZZ[]`

```python
sage: R.<x> = ZZ[]
sage: f = 1*x + 2*x^2 + 3*x^3
sage: g = 3*x + 10*x^2
```

You can use `+, -, *, /, ^` for polynomials as usual

```python
sage: f-g
3*x^3 - 8*x^2 - 2*x
sage: f*g
30*x^5 + 29*x^4 + 16*x^3 + 3*x^2
sage: f/g
(3*x^2 + 2*x + 1)/(10*x + 3)
sage: g^3
1000*x^6 + 900*x^5 + 270*x^4 + 27*x^3
```

Factor the polynomiak with `factor()`

```python
sage: f.factor()
x * (3*x^2 + 2*x + 1)
```

To apply some value to the polynomial, use the syntax `f(x = 2)` or `f(2)`

```python
sage: f(2)
34
sage: f(x=2)
34
```

To extract the coefficient of the polynomial, use `list()` function

```python
sage: g.list()
[0, 3, 10]
```

To get the roots of a polynomial, use `roots()` function

```python
f.roots()
```

Note : This function is only defined for univariate polynomial in integer Ring or Field.

## Multivariate Polynomial

Declaring the polynomial by `R.<x,y> = QQ[]`

Operation `+, -, *, /, ^` are well defined as usual

```python
sage: R.<x,y> = QQ[]
sage: f = x + y + x*y
sage: g = x^2 + y^2
sage: f + g
x^2 + x*y + y^2 + x + y
sage: f ^ -1 * g
(x^2 + y^2)/(x*y + x + y)
```

You can use `f(x = 2, y = 3)` to apply some value to the polynomial

```python
sage: f(x=3,y=5)
23
```

There are 2 ways that I use to solve system of equations

### Resultant

If the underlying ring for the polynomial is either integer ring or field, then you can use resultant to solve system of linear equations.

```python
sage: R.<x,y> = QQ[]
sage: f = x + y + x*y
sage: g = x^2 + y^2
sage: k = f.resultant(g, x)
sage: k
y^4 + 2*y^3 + 2*y^2
```

As `roots()` are only definied for univariate polynomial, we must change `k` to a univariate polynomial first.

```python
sage: k = k(x = 0)
sage: k = k.univariate_polynomial()
sage: k.roots()
[(0, 2)]
```

### Groebner basis

Groebner basis is much slower and less consistent. It might not find a solution for certain equations.

But as it works on any ring, sometimes we have no choice to use it especially when we are dealing with the ring of Integer modulo n.

```python
sage: R.<x,y> = Zmod(30)[]
sage: f = x + y + 3
sage: g = 3 * x + y + 10
sage: I = Ideal([f,g])
sage: I.groebner_basis()
[x + 26, y + 7, 15]
```

# Matrix

You can declare a matrix by :

```python
sage: Matrix(ZZ, [[2,2,3],[4,2,5],[3,3,3]])
[2 2 3]
[4 2 5]
[3 3 3]
sage: Matrix(GF(2), [[2,2,3],[4,2,5],[3,3,3]])
[0 0 1]
[0 0 1]
[1 1 1]
```
The first parameter represents the underlying field for the entries of the matrix.

Access the entries of the matrix with the natural way

```python
sage: A = Matrix(ZZ, [[2,2,3],[4,2,5],[3,3,3]])
sage: A[2][1]
3
```

Operation `+, -, *, ^` are well defined for a matrix

Operation `/` is valid if the matrix is invertible

Other functions for matrix includes :

```python
sage: A.rref()
[1 0 0]
[0 1 0]
[0 0 1]
sage: A.kernel()
Free module of degree 3 and rank 0 over Integer Ring
Echelon basis matrix:
[]
sage: A.charpoly()
x^3 - 7*x^2 - 16*x - 6
```

# Discrete Logarithm

As long as you are dealing with a finite group, you can always use `discrete_log()` to find discrete logarithm.

```python
sage: a = GF(23)(10)
sage: b = a^13
sage: discrete_log(b,a)
13
```

```python
sage: K = GF(3^6,'x')
sage: x = K.gen()
sage: a = x^3 + 3*x^2 + 2 
sage: discrete_log(a, x)
299
```

```python
sage: a = Matrix(GF(7), [[2,2,3],[4,2,5],[3,3,3]])
sage: b = a^3
sage: discrete_log(b,a)
3
```

# Others

There are other useful functions in SageMath such as 

- Chinese Remainder Theorem
- Find multiplicative order
- Dealing with elliptic curve

You can learn how to use them by referring to the official [documentation](https://doc.sagemath.org/html/en/reference/index.html)