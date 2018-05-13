---
layout: post
title: "출전 순서 정하기"
description: "ID : MATCHORDER"
date: 2017-07-15
tags: [알고리즘, 알고스팟, 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, greedy]
comments: true
share: true

---

Greedy의 개념을 알기위한 예제이다. Greedy의 중요한 포인트는 내가 결정하는 순간이 최선의 선택이라는 것이다. 이 조건이 맞아야만 사용할 수 있는 개념이다. 
사실 특별한 것은 아니다. 일종의 생각의 방향을 제시하는 느낌이다. 완전 탐색으로도 값을 구할 수 있지만, 어떠한 경우에는 완전 탐색보다 빠르게 구할 수 있다는 것이다.

MATCHORDER 문제는 쉬웠다. 예제를 가지고 손으로 풀어보니까 한가지 포인트가 있다는 것을 알았다.
* 상대방의 값보다 효율적으로 같거나 커야 한다.

즉 기회비용? 이 적게 이겨야 한다.
### 먼저 코드를 보자
    for(test_case = 0; test_case  < T; test_case++)
        {
            int num, input, answer = 0;
            cin >> num;
            vector<int> Ru;
            vector<int> Ko;
            for(int i = 0; i < num; i++) {
                cin >> input;
                Ru.push_back(input);
            }
            for(int i = 0; i < num; i++) {
                cin >> input;
                Ko.push_back(input);
            }

            sort(Ru.begin(), Ru.end());
            sort(Ko.begin(), Ko.end());

            int i = 0, j = 0;
            while(i != num && j != num) {
                if(Ru[i] <= Ko[j]){
                    i++; j++; answer++;
                } else{
                    j++;
                }
            }
            cout << answer << endl;
        }
        
상대방의 값보다 같거나 적게 만들려면 어떻게 해야 할까. **상대방과 내 값을 정렬한 다음, 나의 작은 값부터 차례대로 상대방의 작은 값과 비교를 한다.** 만약 비교를 해서 내가 승리한다면 상대방의 index와 나의 index를 다음으로 넘긴다. 상대방 값이 나의 값보다 크다면 다음 나의 값으로 비교를 한다.
그래서 **sort()를 사용해 오름차순으로 정렬한 후 while문을 통해 비교를 했다.**
책은 조금 다르게 풀었다. 

### 책의 코드를 보자

    int order(const vector<int>& Ru, const vector<int> Ko) {
        int n = Ru.size(), wins = 0;
        multiset(int) ratings(Ko.begin(), Ko.end());
        for(int i = 0; i < num; i++) {
            if(*ratings.rbegin() < Ru[i])
                ratings.erase(ratings.begin());
            else{
                ratings.erase(ratings.lower_bound(Ru[i]));
                ++wins;
            }
        }
        return wins;
    }
    
책에서는 상대방의 값보다 큰 값이 없다면 제일 작은 값을 지워버린다. 그리고 큰 값이 있다면 그것보다 큰 것 중 제일 작은 것을 지우고 1승을 올린다. 이 논리를 구현하기 위해  multiset을 사용했다. **multiset에 값을 넣으면 자동 정렬이 되며 lower_bound()같은 함수를 사용할 수 있기 때문이다.**

### 탐욕적 알고리즘 레시피
> 1. 문제의 답을 만드는 과정을 여러 조각으로 나눈다.
> 2. 각 조각마다 어떤 우선순위로 선택을 내려야 할지 결정. 예제를 통해 직관을 얻는 것이 빠름.
> 3. 어떤 방식이 동작할 것 같으면 두가지 속성 증명
>> a) 탐욕적 선택 속성 : 각 단계에서 우리가 선택한 답을 포함하는 최적해가 존재(다른 최적해 존재 가정)
>> 
>> b) 최적 부분 구조 : 각 단계에서 최적해를 구했을 때 전체 최적해를 구할 수 있는지 확인

