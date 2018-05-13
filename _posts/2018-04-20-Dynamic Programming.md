---
layout: post
title: "Beauty Of Dynamic Programming - Introduction"
description: "ID : 알고리즘 정리"
date: 2018-04-20
tags: [알고리즘, 알고리즘 정리]
comments: true
share: true

---

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [동적 계획법(Dynamic Programming)이란 - Wikipedia](#동적-계획법dynamic-programming이란-wikipedia)
	* [DP를 사용하게 되는 순서](#dp를-사용하게-되는-순서)
	* [DP의 두가지 방법](#dp의-두가지-방법)

<!-- /code_chunk_output -->



## 동적 계획법(Dynamic Programming)이란 - Wikipedia
In computer science, mathematics, management science, economics and bioinformatics, dynamic programming (also known as dynamic optimization) is a method for solving a complex problem by breaking it down into a collection of simpler subproblems, solving each of those subproblems just once, and storing their solutions. The next time the same subproblem occurs, instead of recomputing its solution, one simply looks up the previously computed solution, thereby saving computation time at the expense of a (hopefully) modest expenditure in storage space. (Each of the subproblem solutions is indexed in some way, typically based on the values of its input parameters, so as to facilitate its lookup.) The technique of storing solutions to subproblems instead of recomputing them is called **"memoization"**.  

위 내용에서 우리가 주목할 부분은 바로 **memoization**이다. 동적계획법(이하 DP)는 커다란 문제를 여러개의 작은 부분 문제로 만들어서 해결하는 기법이다. 하지만 이 문장은 보통 잘 와닿지 않는다. 그러면 더 와 닿는 정의는 무엇일까.  
> DP는 최선의 결과를 구하기 위해 **여러가지 경우의 수**를 찾아야 할때, 경우의 수를 구하는 도중에 **중복**이 되는 부분이 있다면, 그 **중복을 저장한 후** 그 계산할 시간을 줄이는 방법이다.  

핵심은 **중복이 되는 부분을 저장한 후 다음에 사용한다.** 이다. 중복이 되는지 안되는지는 이제 코드를 어떻게 구현하는데 따라 다르다. 여러개의 작은 부분 문제로 만드는 이유는 중복을 만들기 위해서이며, 한번 계산한 그 값들을 계속해서 이용하는 것이다.  

### DP를 사용하게 되는 순서
보통 주어진 문제에서 어떠한 조건일 때, 최선의 결과를 찾는 경우가 대부분이다. 그렇다면 첫째로 우리가 해야할 일은 무엇인가.  

    1. 모든 결과를 다 찾아보고 최선의 값을 구한다


간단하다. 모든 경우의 수를 찾으면 된다. 그리고 최선의 값을 알아내면 된다. 하지만 문제는 시간초과가 뜰 경우가 많다. 시간초과를 줄일려면 어떻게 해야할 것인가.  

	2. 결과를 도출해내는 과정에서 같은 값들을 계산을 하는지 확인한다.

모든 경우의 수를 확인하고 있는데 했던 계산들을 또 하면 당연히 시간이 초과된다. 그 시간들을 줄일 수만 있다면 우린 시간초과를 해결할수 있다.  

	3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.

이것이 바로 DP의 핵심이다. 모든 경우의 수를 구하지만 이미 구했던 값이라면, 다시 계산할 필요가 없기 때문이다. 이미 가본 길이 막힌 길이라면, 그 길을 다시 갈 필요는 없지 않은가.

### DP의 두가지 방법
DP는 꼭 중복이 되는 과정이 있어야 한다. 중복이 되는 과정을 이용하는 방법은 두가지가 된다.  

	1. 점화식 (Recurrence relation)
	2. 재귀 (Recursion)

점화식과 재귀는 결국 같은 말이다. 하지만 구현하는 데에 있어 차이가 있다. DP로 풀어야 하는 문제들은 꼭 점화식과 재귀 두가지 형태로 풀어야 확실한 이해를 할수가 있다. 점화식은 앞에서부터 차근차근 계산하는 방법이며(Bottom-up), 재귀는 뒤에서부터 계산하는 방법이다(Top-down). 앞에서부터 계산할때 빠른 경우가 있으며, 뒤에서부터 계산할 때 빠른 경우가 있다.  
앞으로 DP의 5가지 유형에 대해 포스팅할 것이다.
> 1. Knapsack  
> 2. LCS  
> 3. LIS  
> 4. Edit Distance  
> 5. Matrix Chain Multiplication  

이 자료들은 삼성 코드그라운드에서 발췌했으며, 점화식과 재귀로 이루어진 코드를 같이 포스팅할 것이다.
