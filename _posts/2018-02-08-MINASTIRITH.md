---
layout: post
title: "미나스 아노르"
description: "ID : MINASTIRITH"
date: 2018-02-08
tags: [알고리즘, 알고스팟, 프로그래밍 대회에서 배우는 알고리즘 문제해결전략,  greedy, 삼각함수]
comments: true
share: true

---

문제를 풀면서 각도에 대해 한번쯤 정리를 해야겠다고 생각했었다. 미나스 아노르 문제는 각도에 관한 함수를 정리하게 좋은 문제 같다.
문제 해결에 관한 포인트를 보기 전에 각도 계산에 필요한 함수들을 미리 정리해보자.

| NAME                        | USE                                       | RETURN    |
|-----------------------------|-------------------------------------------|-----------|
| atan2((double)y, (double)x) | (0,0)부터 (y, x)까지의 각도를 반환        | (-pi, pi) |
| fmod(x, y)                  | atan2(y,x)에서 나온 값을 (0, 2*pi)로 변경 | (0, 2*pi) |
| asin((double)x)             | (-pi, pi)에서 x의 역사인 값 반환          | (-pi, pi) |
| 2.0*acos(0)                 | double 형 pi 값을 반환                    | pi        |


* pi값을 acos()함수를 통해 간단히 구할 수 있다.
* atan2(y, x)를 통해 값을 구할 수 있고, 범위를 (0, 2pi)로 하기 위해 fmod(2pi +atan(y, x), 2pi) 값을 이용한다
* asin()과  acos()를 통해 사잇각을 알 수 있다.

그럼 이제 문제의 포인트를 보자
* 길이로 접근하는 것은 어렵기 때문에 각도로 접근해야한다.
* 어떤 기준으로 정렬후 각도를 포함시킬 것인가.

기준을 세울땐 순차적으로 하는 것이 좋다. 즉 **0을 기준으로 포함하는 각을 시작으로** 최대한 앞의 각과 겹치면서 사잇각이 큰 각을 골라야 한다. 0을 기준으로 하는 것이 중요했다.

### 코드를 보자
    for(int i = 0; i < num; i++){
        double center_degree =fmod(2*pi + atan2(y[i], x[i]), 2*pi);
        double side_degree = 2.0*asin(r[i]/2.0/8.0);
        v.push_back(make_pair(center_degree - side_degree, center_degree + side_degree));
    }

    sort(v.begin(), v.end());

    int ret = INF;	//const int INF = 9876654321

    for(int i = 0; i < num; i++){
        if(v[i].first <= 0 || v[i].second >= 2*pi){
            double begin = fmod(v[i].second, 2*pi);
            double end = fmod(v[i].first + 2*pi, 2*pi);
            ret = min(ret, 1 + find_MIN(begin, end));
        }
    }

v라는 배열에 시작과 끝 각도를 넣어서 정렬했다. 그리고 0을 포함하는 각도들 중 가장 적게 포함하는 경우의 수를 찾았다.

### 다음 코드를 보자
    int find_MIN(double begin, double end) {
        int used = 0, idx = 0;
        while(begin < end){
            double maxCover = -1;
            while(idx < num && v[idx].first <= begin){
                maxCover = max(maxCover, v[idx].second);
                ++idx;
            }
            if(maxCover <= begin) return INF;
            begin = maxCover;
            ++used;
        }
        return used;
    }

이 코드는 책의 있는 코드이다. 가장 적게 포함하는 경우의 수를 정말 깔끔하게 구현했다.
이번 문제에서는 각도를 내가 정렬하기 쉽게 변환하는 것과 어떤 기준을 세워서 경우의 수를 구하는 것이 중요했다.
특히 fmod()함수가 정말 깔끔했다.
