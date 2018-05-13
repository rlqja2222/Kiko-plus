---
layout: post
title: "Beauty Of Dynamic Programming - LCS"
description: "ID : 알고리즘 정리"
date: 2017-10-29
tags: [알고리즘, 알고리즘 정리]
comments: true
share: true

---


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [LCS란 - Wikipedia](#lcs란-wikipedia)
	* [예제를 보자 - Codeground Note](#예제를-보자-codeground-note)
* [1. 모든 결과를 다 찾아보고 최선의 값을 구한다](#1-모든-결과를-다-찾아보고-최선의-값을-구한다)
	* [재귀함수로 표현해보자](#재귀함수로-표현해보자)
* [2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.](#2-결과를-도출해내는-과정에서-중복-계산이-있는지-확인한다)
* [3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.](#3-중복이-된다면-그-부분의-값을-기억하고-다음에-사용한다)
	* [DP를 사용한 재귀함수를 보자](#dp를-사용한-재귀함수를-보자)
	* [점화식으로 풀어보자](#점화식으로-풀어보자)
	* [점화식으로 된 코드를 보자](#점화식으로-된-코드를-보자)
* [LCS일때 어떤 문자열인지 추적하는 방법](#lcs일때-어떤-문자열인지-추적하는-방법)
	* [재귀함수에서 추적하는 방법](#재귀함수에서-추적하는-방법)
	* [점화식에서 추적하는 방법](#점화식에서-추적하는-방법)

<!-- /code_chunk_output -->


## LCS란 - Wikipedia
The longest common subsequence (LCS) problem is the problem of finding the longest subsequence common to all sequences in a set of sequences (often just two sequences). It differs from the longest common substring problem: unlike substrings, subsequences are not required to occupy consecutive positions within the original sequences. The longest common subsequence problem is a classic computer science problem, the basis of data comparison programs such as the diff utility, and has applications in bioinformatics. It is also widely used by revision control systems such as Git for reconciling multiple changes made to a revision-controlled collection of files.  

번역을 하면 LCS는 Longest Common Subsequence의 약자로 번역을 하면 최장 공통 부분수열이다. 부분수열은(=집합에서의 부분집합과 비슷) 기존수열의 일부 항을 원래 순서대로 나열해 얻을 수 있는 모든 수열이다.

### 예제를 보자 - Codeground Note

	A = {1, 2, 3, 4, 5, 6}
	B = {1, 3, 5, 4, 2, 6}

위 두 수열의 최장 공통 부분수열 LCS(A, B)는 {1, 3, 4, 6} 또는 {1, 3, 5, 6}이며 그 길이는 4 이다. 또한 LCS는 수열뿐만 아니라 순서를 가지는 모든 구조에 대해 적용할 수 있다.  
LCS를 문자열에 대해 적용해보자.

	string A = abaabcd
	string B = baadca

 "abaabcd"라는 문자열 A와 "baadca"라는 문자열 B가 있을 때, 두 문자열의 최장 공통 부분 수열 LCS(A, B)는 "baad" 또는 "baac"이며, 그 길이는 4가 된다.  
 LCS를 구하기 위해선 어떻게 해야할까.
## 1. 모든 결과를 다 찾아보고 최선의 값을 구한다
위 문자열을 예로 들어보자. 각 문자에 대해서 전부 비교를 해보면 된다. 기준을 A로 삼고 B에서 첫번째 글자부터 마지막 글자끼리 하나하나 찾아보면 된다.
### 재귀함수로 표현해보자
```java
// i : a의 순서, j : b의 순서
int sol(int i, int j) {
	// A : a.size(), B : b.size(), i나 j가 마지막에 도달한다면 0을 반환,
	if (i == A || j == B)
		return 0;
	int ret = 0;
	// a의 i번째와 b의 j번째가 같다면
	if (a[i] == b[j])
		ret = sol(i + 1, j + 1) + 1;
	else	// 다르다면 a의 다음으로 넘겼을때와 b의 다음으로 넘겼을 때를 나눠서 비교한다.
		ret = max(sol(i + 1, j), sol(i, j + 1));
	return ret;
}
```
## 2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.
하지만 조금만 생각해보면 역시나 중복되는 부분이 발생한다. 그래서 **dp배열** 을 생성해서 한번 계산한 값들을 저장한다. dp[i][j]는 a의 i번째와 b의 j번째부터 비교했을 때 가장 긴 길이이다.
## 3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.
c++ reference를 이용, ret이라는 변수에 저장할 것이며, 배열을 -1로 초기화 하여 값이 구해졌는지 아닌지 확인할 것이다.
### DP를 사용한 재귀함수를 보자
```java
int sol(int i, int j) {
	if (i == LA || j == LB)
		return 0;
	int& ret = dp[i][j];
	if (ret != -1) return ret;
	ret = 0;
	if (a[i] == b[j])
		ret = sol(i + 1, j + 1) + 1;
	else
		ret = max(sol(i + 1, j), sol(i, j + 1));
	return ret;
}
```
그냥 검사하던 코드에서 두줄만 추가 됬다. 이것만으로도 시간을 굉장히 줄일 수 있다.
### 점화식으로 풀어보자
사실상 **lcs[i][j]** 를 a의 i번째, b의 j번째까지 비교했을 때 가장 긴 길이라고 하자. 위의 재귀함수의 **dp[i][j]와는 정의가 반대이다**. 그러면 다음 점화식이 성립한다.

	lcs[i][j] =
		if (a[i] == b[i]) {
			lcs[i - 1][j - 1] + 1;
		} else {
			max(lcs[i - 1][j], lcs[i][j - 1]);
		}

이 규칙에 따라 테이블을 만들어보면 다음과 같다.

|   | 0 | a | b | a | a | b | c | d |
|---|---|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| b | 0 | 0 | 1 | 1 | 1 | 1 | 1 | 1 |
| a | 0 | 1 | 1 | 2 | 2 | 2 | 2 | 2 |
| a | 0 | 1 | 1 | 2 | 3 | 3 | 3 | 3 |
| d | 0 | 1 | 1 | 2 | 3 | 3 | 3 | 4 |
| c | 0 | 1 | 1 | 2 | 3 | 3 | 4 | 4 |
| a | 0 | 1 | 1 | 2 | 3 | 3 | 4 | 4 |

맨 앞을 0으로 둔것은 index를 편리하게 사용하기 위해서이다. 각각의 문자들을 비교하면서 같지 않다면 그 기준 왼쪽과 위쪽 숫자를 비교해서 더 큰 숫자를 넣고, 만약 같다면 대각선 방향(왼쪽 위)에서 +1을 한다.

### 점화식으로 된 코드를 보자
```java
for (int i = 1; i < A + 1; i++)
	for (int j = 1; j < B + 1; j++) {
		if (a[i - 1] == b[j - 1]) {
			lcs[i][j] = lcs[i - 1][j - 1] + 1;
		} else {
			lcs[i][j] = max(lcs[i - 1][j], lcs[i][j - 1]);
		}
	}
```

## LCS일때 어떤 문자열인지 추적하는 방법
LCS까지 구했다면 어떤 문자열인지 추적도 할줄 알아야 한다. 하지만 테이블까지 만든 이상 추적하는 방법은 간단하다. 왔던 방법을 똑같이 반대로 하면 된다. 뒤에서부터 추적하는 것이기 때문에 c++ STL에서 제공하는 stack을 사용했다.
### 재귀함수에서 추적하는 방법
```java
int i = 0;
int j = 0;
stack<int> st;
while (dp[i][j] != 0) {
	if (dp[i][j] == dp[i][j + 1]) {
		j++;
	} else if (dp[i][j] == dp[i + 1][j]) {
		i++;
	} else if (dp[i][j] - 1 == dp[i + 1][j + 1]) {
		st.push(i);
		i++;
		j++;
	}
}
for (int i = 0; i < dp[0][0]; i++) {
	cout << a[st.front()] << " ";
	st.pop();
}
```
### 점화식에서 추적하는 방법
```java
int i = A;
int j = B;
stack<int> st;
while (lcs[i][j] != 0) {
	if (lcs[i][j] == lcs[i - 1][j]) {
		i--;
	} else if (lcs[i][j] == lcs[i][j - 1]) {
		j--;
	} else if (lcs[i][j] - 1 == lcs[i - 1][j - 1]) {
		st.push(i - 1);
		i--;
		j--;
	}
}
for (int i = 0; i < dp[0][0]; i++) {
	cout << a[st.front()] << " ";
	st.pop();
}
```
점화식과 재귀함수의 차이를 알겠는가? 이것이 Top-down, Bottom-up의 차이이다.
