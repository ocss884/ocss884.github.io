---
layout: post
title: "Can open set expressed by countable union of disjoint closed intervals?"
date: "2021-01-13"
author: Junrong Lin
---

In last days of this vacation I met this [video](https://www.bilibili.com/video/BV1FK4y1W7ta). Just as the name given in title, it is about proving this statement is impossible. But it is not complete and there are some similar statements. I want to a review on them.  
Every math students should know this very basic statement in their mathematical analysis or real analysis course:  
> - Any **open set** in  real line can be represented as a union of disjoint **open intervals**.
{:.poetry}
What about the **closed** sets? By the definition of closed set---they are the complement of open set, a similar statement can be obtained by taking complement on both sides of the statement above:
> - Any **closed set** in  real line can be represented as a union of **closed intervals whose complement is disjoint**.
{:.poetry}
So, what if these closed intervals is itself disjoint?
The answer will also be trival once we relax the requirement to be almost disjoint (in fact, disjoint v.s almost disjoint is hugely different for such kind of questions). In this case, we even can extend the result to *n* dimensional Euclidean space $$ \mathbb{R}^n $$ by just changing open interval to open cube. 

Another way to solve this problem is to, in this question shares a same topological structure as the famous Cantor set.  
Terence gave an [answer](https://terrytao.wordpress.com/2010/10/04/covering-a-non-closed-interval-by-disjoint-closed-intervals/) in his website.
[Stein's Real Analysis book](https://assets.press.princeton.edu/chapters/s8008.pdf) see Thm 1.3 and 1.4