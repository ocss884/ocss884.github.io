---
layout: post
title: "BIOST823 HW1 - Project Euler P85-87"
date: "2021-09-02"
author: Junrong Lin
tag: 
---
## **Problem 85 Counting rectangles** (24619 solved)
Counting Rectangle By counting carefully it can be seen that a rectangular grid measuring 3 by 2 contains eighteen rectangles:  

![p085.png](/assets/biost823/p085.png)

Although there exists no rectangular grid that contains exactly two million rectangles, find the area of the grid with the nearest solution.  

---
The key point for solving this problem is to find a function calculating all rectangles given a (m, n)-grid.
Consider a grid has *m* rows and *n* columns, a way to count without missing anyone is to count from top to bottom. Find all rectangles with blocks in the top row first, then the second top and so on. Animation below is the 3x3 case.
<div class="loader">
  <div class="logo">
    <div class="red"></div>
    <div class="white" style="width:100%;"></div>
    <div class="white" style="width:calc(200%/3);"></div>
    <div class="white" style="width:calc(100%/3);"></div>
    <div class="white" style="bottom:calc(100%/3);"></div>
    <div class="white" style="bottom:calc(200%/3);"></div>
  </div>
  <p>3x3 grid</p>
</div>
By this method, any (m,n)-grid has $$\frac{mn(m+1)(n+1)}{4}$$ rectangles. To find the area of the grid with rectangles cloest to two million, there is a useful build-in python function $$\min(iterable, key)$$. With a key function which returns the difference in rectangles, it can return the $$(m, n)$$ pair.  

---

## **Problem 86 Cuboid route** (12587 solved)
A spider, S, sits in one corner of a cuboid room, measuring 6 by 5 by 3, and a fly, F, sits in the opposite corner. By travelling on the surfaces of the room the shortest "straight line" distance from S to F is 10 and the path is shown on the diagram.  

![p086.png](/assets/biost823/p086.png)

However, there are up to three "shortest" path candidates for any given cuboid and the shortest route doesn't always have integer length.  

It can be shown that there are exactly 2060 distinct cuboids, ignoring rotations, with integer dimensions, up to a maximum size of M by M by M, for which the shortest route has integer length when M = 100. This is the least value of M for which the number of solutions first exceeds two thousand; the number of solutions when M = 99 is 1975.  

Find the least value of M such that the number of solutions first exceeds one million.

---

Let's first define some terms
 > $$(x, y, z)$$: 3-dimension of a cuboid  
 > $$(a, b, c)$$: 3 coprime sides of a right triangle with $$a \leq b\leq c$$. So-called primitive pythagorean triple  
 > $$(m, n)$$: odd coprimes that defines a [primitive pythagorean triple](https://en.wikipedia.org/wiki/Pythagorean_triple)  
 > $$(x, y, z)\in f(M)$$ if $$max(x, y, z) = M$$ and has integer shortest path  
 > LIMIT: bound for $$(x,y,z)$$

In this problem, any valid triple $$(x, y, z)$$ should have the smallest of these three to be an integter.
 > $$(x+y)^2+z^2$$  
 > $$x^2+(y+z)^2$$  
 > $$(x+z)^2+y^2$$  

W.L.O.G. let $$x \leq y \leq z$$. That means the first line is the shortest path of (x, y, z)-cuboid and $$(x+y, z) = (a, b)$$ is the two catheti sides of a right triangle. All three sides (a, b, c) are integer. Thus $$(a, b, c)$$ is a pythagorean triple. It should be a multiple of some primitive pythagorean triple (e.g. (6, 8, 10) is a multiple of (3, 4, 5)). The intuitive is to bound the 3-dimension of cuboid and search for all possible primitive pythagorean triple.  
We should introduce a theorem before go into details.

 > Any two odd coprimes $$(m, n)$$ define a unique primitive pythagorean triple and is all of them.  
 > $$ a=m*n $$  
 > $$ b=\frac{m^2-n^2}{2} $$  
 > $$ c=\frac{m^2+n^2}{2} $$  

Therefor, we can generate all primitive pythagorean triples $$(a, b, c)$$ by iterating all coprime odd pairs (m, n).  
For each $$(a,b,c)$$ generate *k* triples up to $$a*k \leq LIMIT$$ but $$a*(k+1) > LIMIT$$. Check all possible decomposition of $$(a*i, b*i)$$, $$i=1,...,k$$. 

There are 2 conditions:

 1. If $$a*i\leq b*i\leq LIMIT$$  
 It is safe to breakdown two sides
 2. If $$a*i\leq LIMIT \leq b*i$$  
 We should breakdown $$b*i$$ otherwise it is larger than LIMIT  

Note that for any $$(a*i, b*i)$$, it is save whenever we decompose $$a*i$$ and $$(x,a*i-x,b*i)\in f(b*i)$$. Value of x could range in (1,$$a*i//2$$).  

When dealing with $$b*i$$. There is one more special case check:  
If $$2a \leq b$$ (e.g. $$(5,12,13)$$), we can only decompose side of length 5. If we decompose 12, at least one dimension of is greater than 5. In this case the shortest path should be $$(5+x)^2+(12-x)^2$$.

---

## **Problem 87 power triples** (20810 solved)
<p>The smallest number expressible as the sum of a prime square, prime cube, and prime fourth power is 28. In fact, there are exactly four numbers below fifty that can be expressed in such a way:</p>
<p class="margin_left">28 = 2<sup>2</sup> + 2<sup>3</sup> + 2<sup>4</sup><br />
33 = 3<sup>2</sup> + 2<sup>3</sup> + 2<sup>4</sup><br />
49 = 5<sup>2</sup> + 2<sup>3</sup> + 2<sup>4</sup><br />
47 = 2<sup>2</sup> + 3<sup>3</sup> + 2<sup>4</sup></p>
<p>How many numbers below fifty million can be expressed as the sum of a prime square, prime cube, and prime fourth power?</p>

---
This problem can be solved by brutal force.  
It is clear that there is a range for any valid $$(a, b, c)$$ satisfying $$a^2+b^3+c^4<50000000$$.  
That is:
 > $$ a < \sqrt{50000000} $$  
 > $$ b < 50000000^\frac{1}{3}$$  
 > $$ c < 50000000^\frac{1}{4}$$  

There is an algorithm called [Sieve of Eratothenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes). It can find all prime up to any limit. Use the sieve 3 times to find all possible $$(a, b, c)$$ and iterate over them to find all possible sum. You can use a python $$set()$$ to fillter different $$(a, b, c)$$ but same sum.