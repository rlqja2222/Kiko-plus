---
layout: post
title: "Beauty Of Dynamic Programming - LIS"
description: "ID : 알고리즘 정리"
date: 2017-10-30
tags: [알고리즘, 알고리즘 정리]
comments: true
share: true
---

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [LIS란 - Wikipedia](#lis란-wikipedia)
	* [예제를 보자](#예제를-보자)
* [1. 모든 결과를 다 찾아보고 최선의 값을 구한다](#1-모든-결과를-다-찾아보고-최선의-값을-구한다)
	* [재귀함수로 표현해보자](#재귀함수로-표현해보자)
* [2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.](#2-결과를-도출해내는-과정에서-중복-계산이-있는지-확인한다)
* [3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.](#3-중복이-된다면-그-부분의-값을-기억하고-다음에-사용한다)
	* [DP를 사용한 재귀함수를 보자](#dp를-사용한-재귀함수를-보자)
	* [점화식으로 풀어보자](#점화식으로-풀어보자)
	* [점화식으로 된 코드를 보자](#점화식으로-된-코드를-보자)

<!-- /code_chunk_output -->

## LIS란 - Wikipedia
In computer science, **the longest increasing subsequence problem** is to find a subsequence of a given sequence in which the subsequence's elements are in sorted order, lowest to highest, and in which the subsequence is as long as possible. This subsequence is not necessarily contiguous, or unique. Longest increasing subsequences are studied in the context of various disciplines related to mathematics, including algorithmics, random matrix theory, representation theory, and physics.The longest increasing subsequence problem is solvable in time O(n log n), where n denotes the length of the input sequence.

증가 수열은 수열 내에서 원소들을 순서를 유지하면서 임의로 뽑았을 때, 원소의 값이 증가하는 수열이다. LIS는 그 중에서 가장 긴 수열이다.

### 예제를 보자

	A = {20, 1, 7, 4, 5, 6, 13, 3}

예를 들어 위 A의 수열에서 LIS를 구하면 다음과 같다.

	LIS(A) = {1, 4, 5, 6, 13}

LIS를 어떻게 구할 수 있을까.

## 1. 모든 결과를 다 찾아보고 최선의 값을 구한다
위의 수열을 예로 들어보자. 앞에서부터 숫자 하나씩 체크하면서 시작했던 숫자보다 큰지 안큰지를 전부 검사하면서 경우의 수를 구해보자
### 재귀함수로 표현해보자
```java
// i번째의 값과 a[j]을 비교,  j는 현재까지 증가해시켰던 가장 큰 원소의 index
int sol(int i, int j) {
  if (i == N)
    return 0;
  // 넘어갈 경우를 생각
  int ret = sol(i + 1, i);
  // 만약 증가한다면 1을 더해주고 비교
  if (A[j] < A[i + 1])
    ret = max(ret, sol(i + 1, i + 1) + 1);
  return ret;
}
```
## 2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.
하지만 이 역시도 계속 계산했던 값이 겹친다. 중복되는 값들을 배열 **dp[i][j]** 에 저장을 하자. dp[i][j]는 i부터 계산했을 때, j가 가장 큰 인덱스일때 길이의 값이다. 위의 함수는 증가할때마다 결과 값을 1씩 증가시키므로 최종 답에는 1을 추가해야 한다.

## 3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.
c++ reference를 이용, ret이라는 변수에 저장할 것이며, 배열을 -1로 초기화 하여 값이 구해졌는지 아닌지 확인할 것이다.
### DP를 사용한 재귀함수를 보자
```java
int sol(int i, int j) {
  if (i == N)
    return 0;
  /***** 값을 저장하고 사용하는 코드 *****/
  int& ret = dp[i][j];
  if (ret != -1) return ret;
  /*****/
  ret = sol(i + 1, i);
  if (A[j] < A[i + 1])
    ret = max(ret, sol(i + 1, i + 1) + 1);
  return ret;
}
```
그냥 검사하던 코드에서 두줄만 추가 됬다. 이것만으로도 시간을 굉장히 줄일 수 있다.

### 점화식으로 풀어보자
이번엔 점화식으로 풀어보자. **D[i]** 는 i번째 원소를 마지막으로 갖는 최장 증가수열이라고 하자. 이 이야기는 i번째 원소가 무조건 수열의 마지막에 위치해야한다. 그리고 전부 i번째 값보다 작아야 한다.
즉 D[i]의 값은 i보다 작은 j에 대해 A[j] < A[i]를 만족하는 D[j] 중 최댓값 + 1이 된다.

	D[i] =
	j부터 ~ i - 1 까지
	A[j] < A[i] 인
	D[j] 중 최댓값 + 1 이다.

위의 예제를 표로 확인해 보자.

| idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|:------:|:----:|:---:|:---:|:---:|:---:|:---:|:----:|:---:|
| A[i] | 20 | 1 | 7 | 4 | 5 | 6 | 13 | 3 |
| D[j] | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |

각 인덱스 i에 대해 우리는 **A[j](0 ≤ j < i)가 마지막에 위치하며 길이가 D[j]인 증가 수열에 A[i]를 추가할 수 있는지** 확인해야 한다. 위에서 다뤘듯 '추가할 수 있다'의 기준은 **A[i]가 A[j]보다 큰 것을** 의미한다.  
0 ~ i-1까지 이를 **만족하는 j중 가장 큰 D[j]를 D[i]에 저장** 한다. 만약 해당하는 j가 없다면 D[i]는 0이 되고, 이후 D[i]에 1을 더한다. 이 과정은 **A[i]를 마지막 원소로 추가하여 길이가 1 증가하는 것** 을 의미한다. 이를 통해 위 테이블을 갱신해나가면 결과는 아래와 같다.

| idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|:------:|:----:|:---:|:---:|:---:|:---:|:---:|:----:|:---:|
| A[i] | 20 | 1 | 7 | 4 | 5 | 6 | 13 | 3 |
| D[j] | 1 | 1 | 2 | 2 | 3 | 4 | 5 | 2 |

위 테이블에서도 확인할 수 있듯, D[i]의 결과는 뒤로 갈수록 커지는 형태가 아니다. A[i]의 값에 따라 D[i - 1]보다 D[i]가 작을 수도 있으므로 최장 증가 부분 수열의 길이를 구하기 위해서는 D[i]의 값 중 가장 큰 값을 다시 찾아야 한다.

### 점화식으로 된 코드를 보자
```java
for (int i = 0; i < N; i++) {
	for (int j = 0; j < i; j++) {
		if (A[j] < D[i])
			if (D[j] > D[i])
				D[i] = D[j];
	}
	D[i]++;
}
int ans = 0;
for (int i = 0; i < N; i++)
	ans = max(ans, D[i]);
```
다시 한번 강조하지만 DP는 **전의 썼던 값을 기록해 놓고 재사용** 하는 것이다.
