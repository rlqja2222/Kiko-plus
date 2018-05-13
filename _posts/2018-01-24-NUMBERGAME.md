---
layout: post
title: "숫자 게임"
description: "ID : NUMBERGAME"
date: 2017-07-14
tags: [알고리즘, 알고스팟, 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, dynamic programming]
comments: true
share: true

---

NUMBERGAME 문제의 핵심은 모든 차이의 결과를 구해야 한다는 것이다. 완전탐색을 하기 위해서 중요한 포인트를 찾을 수 있다.
* 게임은 현우부터 시작한다.
* 오른쪽 혹은 왼쪽의 카드를 가져갈 수 있다.
* 왼쪽 혹은 오른쪽의 카드를 2장씩 지울 수 있다.(2장이상 남아있다면)
* 현우와 서하의 차이 값이 커야하므로 모든 경우를 찾아야 한다.

### 이제 코드를 보자
    int find_MAX(int l, int r) {

	int k = 0;

	int& ret = cache[l][r];
	if(ret != -1) return ret;

	if(l == r)
		return ret = s[l];

	ret = max(s[l] - find_MAX(l + 1, r), s[r] - find_MAX(l, r - 1));

	if(r - l >= 2)
		k = max(-find_MAX(l, r - 2), -find_MAX(l + 2, r));

	ret = max(k, ret);

	return ret;
	}
    
코드를 짜고나서 책을 봤는데 너무나 비슷해서 놀랐다. 일단 **왼쪽과 오른쪽을 줄여가며 봐야 하므로** 남은 카드를 넘기는 것보단 index로 접근하는 것이 편하다는 생각이 들었다. 그 다음 중복이 발생하니 dp를 사용했고, 1장 남았을 경우는 무조건 가져가야하므로 조건을 넣었다. 
카드는 s[]에 저장했고 한장 씩 가져갈때 와 두장을 지울때를 나눠서 값을 비교했다. **여기서 포인트는 현우부터 시작하기 때문에 2장을 지웠을 경우 -를 붙여서 비교해야 한다는 점이다.**

이번 문제는 파악만 잘했다면 금방 풀 수 있었다. 이런 유형의 문제는 절대 놓쳐서는 안된다.