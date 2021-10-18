---
layout: post
title: "BIOST823 HW2 - Pi-Prime Puzzle"
date: "2021-09-17"
author: Junrong Lin
tag: 
---
## Number theory and a Google recruitment puzzle

Find the first 10-digit prime in the decimal expansion of 17π.

The first 5 digits in the decimal expansion of π are 14159. The first 4-digit prime in the decimal expansion of π are 4159. You are asked to find the first 10-digit prime in the decimal expansion of 17π. First solve sub-problems (divide and conquer):

Write a function to generate an arbitrary large expansion of a mathematical expression like π. Hint: You can use the standard library decimal or the 3rd party library sympy to do this
Write a function to check if a number is prime. Hint: See Sieve of Eratosthenes
Write a function to generate sliding windows of a specified width from a long iterable (e.g. a string representation of a number)
Write unit tests for each of these three functions. You are encouraged, but not required, to try test-driven development.

Now use these helper functions to write the function that you need. Write a unit test for this final function, given that the first 10-digit prime in the expansion e is 7427466391. Finally, solve the given problem.