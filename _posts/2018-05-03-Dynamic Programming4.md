---
layout: post
title: "Beauty Of Dynamic Programming - Edit Distance"
description: "ID : 알고리즘 정리"
date: 2017-10-31
tags: [알고리즘, 알고리즘 정리]
comments: true
share: true
---

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [Edit Distance란 - Wikipedia](#edit-distance란-wikipedia)
	* [예제를 보자  (codeground-note)](#예제를-보자-codeground-note)
* [1. 모든 결과를 다 찾아보고 최선의 값을 구한다](#1-모든-결과를-다-찾아보고-최선의-값을-구한다)
	* [재귀함수로 표현해보자](#재귀함수로-표현해보자)
* [2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.](#2-결과를-도출해내는-과정에서-중복-계산이-있는지-확인한다)
* [3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.](#3-중복이-된다면-그-부분의-값을-기억하고-다음에-사용한다)
	* [DP를 사용한 재귀함수를 보자](#dp를-사용한-재귀함수를-보자)
	* [점화식으로 풀어보자](#점화식으로-풀어보자)
	* [점화식으로 된 코드를 보자](#점화식으로-된-코드를-보자)

<!-- /code_chunk_output -->

## Edit Distance란 - Wikipedia
In computational linguistics and computer science, edit distance is a way of quantifying how dissimilar two strings (e.g., words) are to one another by counting **the minimum number of operations required to transform one string into the other**. Edit distances find applications in natural language processing, where automatic spelling correction can determine candidate corrections for a misspelled word by selecting words from a dictionary that have a low distance to the word in question. In bioinformatics, it can be used to quantify the similarity of DNA sequences, which can be viewed as strings of the letters A, C, G and T.

Edit Distance는 문자열 A에서 문자열 B로 변경하는데 필요한 최소 연산 횟수를 구하는 문자열 유사도 판단 알고리즘이다. 문자열 A에 대해 가능한 연산으로는 원하는 문자의 삽입, 삭제, 수정 등이 있다.

### 예제를 보자  (codeground-note)
두 문자열  **a: "kitten", b: "sitting"** 이 있다. 우리는 string a에서 한 글자씩 삽입/삭제/수정하는 연산을 통해 string b로 만들어야 한다. 어떻게 최소 연산 횟수를 구할 수 있을까.

## 1. 모든 결과를 다 찾아보고 최선의 값을 구한다
우리가 할 수 있는 연산은 문자의 삽입(insert), 삭제(remove), 수정(replace)가 있다. 그리고 이러한 **연산을 할때는 count가 1씩 추가되며**, **같을 땐 그냥 넘어간다**. insert가 일어나면, a에 추가하는 것이므로 b의 비교 인덱스만 다음으로 넘어간다. remove일땐 a의 인덱스만 넘어간다. replace일땐 a, b 둘다 다음 인덱스로 넘어간다.   
인덱스를 뒤에서부터 구분하는 코드가 좀 더 깔끔하므로 뒤에서부터 비교하도록 구현했다.
### 재귀함수로 표현해보자
```java
int A, B;		// 문자열의 길이
string a, b;		// 문자열

int sol(int i, int j) {
  if (i == 0) return j;	// a를 전부 검사했다면 남은 b의 갯수만큼 리턴
  if (j == 0) return i;	// b를 전부 검사했다면 남은 a의 갯수만큼 리턴
  // 둘이 같다면 count하지 않고 다음으로 넘어감
  if (a[i - 1] == b[j - 1])
    return sol(i - 1, j - 1);
  // 연산을 해야할 경우 최솟값을 구한다.
  int ret = sol(i - 1, j - 1);
  ret = min(ret, sol(i - 1, j));
  ret = min(ret, sol(i, j - 1));
  return ret + 1;
}
```
## 2. 결과를 도출해내는 과정에서 중복 계산이 있는지 확인한다.
역시나 재귀 함수를 통해서는 중복이 발생한다. **dp[i][j]** 를 이용하여 이미 계산한 값을 저장하자. dp[i][j]는 **맨 앞부터 a의 i번째와 b의 j번째까지 연산한 최소 연산값이다**.

## 3. 중복이 된다면, 그 부분의 값을 기억하고 다음에 사용한다.
c++ reference를 이용, ret이라는 변수에 저장할 것이며, 배열을 INT_MAX로 초기화 하여 값이 구해졌는지 아닌지 확인할 것이다. _(INT_MAX로 초기화하는 이유는 min을 사용하기 때문에)_
### DP를 사용한 재귀함수를 보자
```java
int sol(int i, int j) {
  // 현재 값을 저장
  int& ret = dp[i][j];
  if (ret != INT_MAX) return ret;

  if (i == 0) return ret = j;
  if (j == 0) return ret = i;
  if (a[i - 1] == b[j - 1])
    return ret = sol(i - 1, j - 1);

  ret = sol(i - 1, j - 1);
  ret = min(ret, sol(i - 1, j));
  ret = min(ret, sol(i, j - 1));

  // overflow 방지
  if (ret == INT_MAX)
    return ret;

  return ++ret;
}

int main() {
  for (int i = 0; i < MAX_A; i++)
    for (int j = 0; j < MAX_B; j++)
      dp[i][j] = INT_MAX;
  cin >> A >> B;
  cin >> a >> b;
  sol(A, B);
  cout << dp[A][B] << endl;
  return 0;
}
```
참고로 -1로 배열을 초기화할때는 **memset**을 사용해도 되지만, **자료형의 최댓값으로 초기화할땐 사용할수 없다.**  memset의 두번째 인자가 single byte이기 때문이다.
### 점화식으로 풀어보자
**D[i][j]**를 선언하자. D[i][j]는 a의 i번째까지 사용해서 b의 j번째를 만드는데 드는 최소 연산갯수이다. 또한 인덱스 계산을 편리하게하기 위해 1부터 시작하자.

|      | A :  |   | k | i | t | t | e | n |
|:----:|:----:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **B :**  |  **idx** | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|      |   0  |   |   |   |   |   |   |   |
|   **s**  |   1  |   |   |   |   |   |   |   |
|   **i**  |   2  |   |   |   |   |   |   |   |
|   **t**  |   3  |   |   |   |   |   |   |   |
|   **t**  |   4  |   |   |   |   |   |   |   |
|   **i**  |   5  |   |   |   |   |   |   |   |
|   **n**  |   6  |   |   |   |   |   |   |   |
|   **g**  |   7  |   |   |   |   |   |   |   |

다음과 같은 테이블을 정의할수 있다. 그 다음 점화식을 만들어보자.

	D[i][j] =
		min {
			replace : D[i - 1][j - 1] + 1 (if A[i] != B[j])
			delete :  D[i - 1][j] + 1
			insert :  D[i][j - 1] + 1
		}

이 점화식을 이용, 위의 테이블을 채워보자.

|      | A :  |   | k | i | t | t | e | n |
|:----:|:----:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **B :**  |  **idx** | **0** | **1** | **2** | **3** | **4** | **5** | **6** |
|      |   **0**  | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|  **s**  |   **1**  | 1 | 1 | 2 | 3 | 4 | 5 | 6 |
|   **i**  |   **2**  | 2 | 2 | 1 | 2 | 3 | 4 | 5 |
|   **t**  |   **3**  | 0 | 1 | 2 | 1 | 2 | 3 | 4 |
|   **t**  |   **4**  | 0 | 1 | 3 | 2 | 1 | 2 | 3 |
|   **i**  |   **5**  | 0 | 1 | 4 | 3 | 2 | 2 | 3 |
|   **n**  |   **6**  | 0 | 1 | 5 | 4 | 3 | 3 | 2 |
|   **g**  |   **7**  | 0 | 1 | 6 | 5 | 4 | 4 | 3 |

### 점화식으로 된 코드를 보자
```java
// la : length of a, lb : length of b
for(int i = 1; i <= la; i++) {
		d[i][0] = i;
}
for(int j = 1; j <= lb; j++) {
		d[0][j] = j;
}
for(int i = 1; i <= la; i++) {
		for(int j = 1; j <= lb; j++) {
				if(a[i - 1] == b[j - 1]) {
						d[i][j] = d[i - 1][j - 1];
				} else {
						d[i][j] = min(min(d[i - 1][j], d[i][j - 1]), d[i - 1][j - 1]) + 1;
				}
		}
}
```
DP문제는 풀다보면 다들 비슷하다는 것을 알 수 있다.
