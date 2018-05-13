---
layout: post
title: "Beauty Of Dynamic Programming - Knapsack"
description: "ID : 알고리즘 정리"
date: 2018-04-25
tags: [알고리즘, 알고리즘 정리]
comments: true
share: true

---

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [Kanpsack Problem이란  - Wikipedia](#kanpsack-problem이란-wikipedia)
	* [예제를 보자](#예제를-보자)
* [1. 모든 결과를 다 찾아보고 최선의 값을 구한다](#1-모든-결과를-다-찾아보고-최선의-값을-구한다)
	* [재귀함수로 표현해보자.](#재귀함수로-표현해보자)
* [2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.](#2-결과를-도출해내는-과정에서-중복-계산이-있는지-확인한다)
* [3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.](#3-중복이-된다면-그-부분의-값을-기억하고-다음에-사용한다)
	* [DP를 사용한 재귀함수를 보자](#dp를-사용한-재귀함수를-보자)
	* [점화식으로 풀어보자](#점화식으로-풀어보자)
	* [점화식으로 된 코드를 보자](#점화식으로-된-코드를-보자)
* [최대값일때 어떤 물건을 넣었는지 추적하는 방법](#최대값일때-어떤-물건을-넣었는지-추적하는-방법)

<!-- /code_chunk_output -->


## Kanpsack Problem이란  - Wikipedia
The knapsack problem or rucksack problem is a problem in combinatorial optimization: Given a set of items, each with a weight and a value, determine the number of each item to include in a collection so that the total weight is less than or equal to a given limit and the total value is as large as possible. It derives its name from the problem faced by someone who is constrained by a fixed-size knapsack and must fill it with the most valuable items.  

위 내용을 보면 **제한 조건이 있을 때 가치들의 총합** 을 구하는 문제이다. 예를 들어, 무게 제한이 K인 배낭과 무게와 가치가 정해진 N개의 물건이 있을 때 가치의 총합이 가장 크도록 배낭을 싸는 문제다. 삼성 코드그라운드에서 발췌한 예제를 보자.
### 예제를 보자

| i |  v | w |
|:-:|:--:|:-:|
| 1 | 13 | 6 |
| 2 |  8 | 4 |
| 3 |  6 | 3 |
| 4 | 12 | 5 |

무게 제한이 7kg이고, 위와 같이 4개의 물건이 있을 때, 가치의 총합을 가장 크게 가방을 쌀려면 어떤 물건을 넣어야 할까. 가장 큰 물건부터 넣어보고 생각해보는 건 바로 틀리다는 것을 알 수 있다. 4번을 제일 먼저 넣으면 최대값은 12가 된다. 하지만 이것은 최대값이 아니다. 2와 3을 넣으면 14이기 때문이다.
이러한 문제를 어떻게 접근해야 할까.
## 1. 모든 결과를 다 찾아보고 최선의 값을 구한다
그렇다. 무게 제한이 K라면 총 무게가 K 안으로 모든 경우의 수를 찾아보면 된다. 우린 하나의 물건에 대해서 선택할 수 있는 경우의 수는 2가지이다. **넣을까, 넣지 않을까**.  
그럼 간단하게 재귀함수로 코드를 짤 수 있다.
### 재귀함수로 표현해보자.

```java
//i : i번째 물건을 넣을지 결정할 때, j : 남은 무게, 재귀로 들어가서 최대값을 반환
int sol(int i, int j) {
  // 물건을 다 체크했다면 return 0
  if (i == N + 1 || j < 0)
	return 0;
  // i번째 물건을 넣지 않고 다음 것을 고민할 때
  int ret = sol(i + 1, j);
  // i번째 물건을 넣을거라면, 남은 무게가 충분한지 확인하고 넣는다. W[i]는 i의 무게, V[i]는 i의 가치
  if (j >= W[i])
    //그리고 안넣었을 때와 비교를 해서 큰 값을 반환한다.
    ret = max(ret, sol(i + 1, j - W[i]) + V[i]);
  return ret;
}
```
i번째 물건에 대해서 넣을지 안넣을지를 고민한다. 이렇게 마지막 물건까지 체크를 하면 return 0을 반환하면서 최대 값을 얻을 수 있다.
## 2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.
하지만 이렇게 했을시 시간초과가 뜰 수 있다. 왜냐하면 중복 계산이 존재하기 때문이다. 이미 계산을 한 값이 있다면 그 값들을 저장해서 다시 사용할 수 있다.
## 3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.
그럼 이미 계산된 값들을 저장을 해보자. dp[i][j] 를 선언한다. dp[i][j]에 저장되는 값은 남은 무게가 j일 때, i번째 물건을 체크하는 순간 가장 큰 값이다. 그리고 c++ reference를 이용, ret이라는 변수에 저장할 것이며, 배열을 -1로 초기화 하여 값이 구해졌는지 아닌지 확인할 것이다.
### DP를 사용한 재귀함수를 보자

```java
int dp[101][10001];
int sol(int i, int j) {
  if (i == N + 1 || j < 0)
    return 0;
  int& ret = dp[i][j];
  if (ret != -1) return ret;
  ret = sol(i + 1, j);
  if (j >= W[i])
    ret = max(ret, sol(i + 1, j - W[i]) + V[i]);
  return ret;
}
```
계산을 한번씩만 함으로써 중복을 줄일 수 있다. 여기서 다시 한번보면 DP로 선언한 배열의 차원은 재귀 함수로 넘겨주는 인자의 수와 같다. 조금만 생각해보면 그럴 수 밖에 없다.
### 점화식으로 풀어보자
재귀로 풀수 있는 문제는 점화식으로도 접근할 수 있다. 위에서 정한 dp[i][j]를 점화식으로 표현할 수 있다.

	dp[i][j] = Max(dp[i-1][j], dp[i-1][j - W[i]] + V[i])

**dp[i][j]** 를 **dp[i-1][j] (i-1까지 체크를 하고 j가 남은 상태)** 에서 i번째를 포함할 때와 안할때로 나눠서 크기를 비교한다. 우린 이 점화식을 i와 j에 포문 두번을 돌려서 앞에서 부터 확인을 하면 된다. 이것이 바로 bottom-up이다.
### 점화식으로 된 코드를 보자

```java
for (int i = 1; i < N + 1; i++) {
	for (int j = 0; j < K + 1; j++) {
		if (j < W[i]) {
  		dp[i][j] = dp[i - 1][j];
		} else if (dp[i - 1][j - W[i]] + V[i] > dp[i - 1][j]){
  		dp[i][j] = dp[i - 1][j - W[i]] + V[i];
		} else {
  		dp[i][j] = dp[i - 1][j];
		}
	}
}
```
재귀 함수와 점화식은 계산을 하는 방향에 차이가 있으며, 문제에 알맞게 사용하면 된다. knapsack에서 기억을 할 것은 **넣을까, 말것인가** 이 두가지 경우가 있다는 것이다.

## 최대값일때 어떤 물건을 넣었는지 추적하는 방법
좀 더 심화가 되면 어떠한 물건을 넣었는지 알아야 할수도 있다. 그럴 때 어떠한 방법을 쓸까. 재귀함수에서 보면 i번째 물건을 넣었을때 최대값이 된다면 그때 체크를 해주면 된다. dp[i][j]와 묶기 위해서 **check[i][j]** 를 선언한다. check[i][j]는 무게가 j남았을 때 i번째 물건이 추가가 된다면 1, 아니면 0을 저장한다.

```java
// i번째 물건 포함하지 않는다면 check[i][j] = 0
ret = sol(i+1, j);
check[i][j] = 0;
// i번째 물건을 포함한다면
if(j >= w[i])
    if(v[i] + sol(i + 1, j - w[i]) > ret){
        ret = v[i] + sol(i + 1, j - w[i]);
        check[i][j] = 1;
    }
```
이렇게 sol() 함수를 변경할수 있다. 그리고 재추적을 하기 위해서 trace()함수를 만들었다.

```java
void trace() {
    int j = max_j;	// max_j : 최대값을 가질때 무게 값
    for(int i = 0; i < N; i++) {	// N은 전체 물건
      if (check[i][j]) {
              carry.push_back(name[i]);//carry라는 백터에 이름을 넣는다.
              j -= w[i];
      }
    }
}
```
이런식으로 하나하나 체크해서 뒤에서부터 재추적을 할 수 있다. 하지만 조금 더 깔끔하게 재추적을 할수도 있다.

```java
void trace(int i, int j, vector<string>& picked) {
      if(i == N) return;
      if(dp[i, j] == dp[i + 1, j]){
          trace(i + 1, j, picked);
      } else {
          picked.push_back(name[i]);
          trace(i + 1, j - w[i], picked);
      }
}
```
picked이라는 vector 배열에 dp 값이 변했으면 계속 저장을 하고 아니면 넘어가는 재귀함수로 재추적을 할수도 있다. 이 코드는 대회문제로 푸는 알고리즘해결전략에서 발췌했다.
