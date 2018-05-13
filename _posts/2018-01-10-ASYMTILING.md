---
layout: post
title: "비대칭 타일링"
description: "ID : ASYMTILING"
date: 2017-07-06
tags: [알고리즘, 알고스팟, 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, dynamic programming]
comments: true
share: true

---

ASYMTILING 문제의 포인트 3가지로 나뉜다.
* 타일링 전체의 갯수를 세는 방법
* 대칭인 갯수를 구하는 방법
* modular 연산을 정확히 구현

첫번째, 어떻게 전체의 갯수를 구할 것인가. 
그림을 몇번 그려보면 직사각형 안에 들어갈 수 있는 경우의 수는 1개가 세로로 들어가거나 가로로 들어가는(가로로 두개) 2가지이다.
`경우의 수가 2개로 나뉜다는 것`은 재귀로 구현하기에 굉장히 매력적이다. 그래서 매 순간순간 1개를 넣을때와 2개로 넣을때로 나눠서 남은 길이를 parameter로 넘겼다.

### 이제 코드를 보자
	unsigned long long find_MAX(int l) {

	if(l < 0)
		return 0;

	unsigned long long& ret = cache[l];
	if(ret != 0) return ret;

	ret = find_MAX(l - 1) + find_MAX(l - 2);

	if(l == 0)
		ret = 1;

	return ret;
	}
    
자료형이 unsigned long long 은 문제에서 보면 방법 수가 굉장히 크기 때문에 int로는 부족했다. **여기서 한번 자주 쓰이는 자료형에 관해 정리를 해보자.**

    
| Type Name | Bytes |                      Range of Value                     |
|:---------:|:-----:|:-------------------------------------------------------:|
|    int    |   4   |             –2,147,483,648 to 2,147,483,647             |
|    char   |   1   |                    –32,768 to 32,767                    |
| long long |   8   | –9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
|   double  |   8   |                 1.7E +/- 308 (15 digits)                |

unsigned는 signed의 2배이므로 필요할 때 사용하면 된다. 같은 계산이 여러번 중복이 되어 cache를 저장했고, 길이가 0이 됬을 때는 가득 채워진 것이므로 1을 return 했다. 이렇게 끝까지 돌리면 주어진 길이에 만들 수 있는 모든 경우의 수가 return 된다.

### 대칭을 구하는 코드를 보자
    unsigned long long find_symmetry(int l) {

        if(l%2 == 0){
            return find_MAX(l/2) + find_MAX(l/2 - 1);
        }else{
            return find_MAX(l/2);
        }
    }
	
비대칭이 나올 경우는 전체 경우의 수에서 대칭인 경우의 수를 제외하면 되므로 대칭인 갯수를 구현했다. 대칭일려면 왼쪽과 오른쪽의 모양이 같아야 한다. **즉 반으로 나눠서 생각을 해야한다.** 
대칭일 경우는 3가지 모양이 있다. **딱 가운데에 세로로 1개가 있을때(홀수일 때)**, 그리고 **반으로 정확히 나뉘거나 가운데에 가로로 있을때(짝수)**이다.
그럼 일단 짝수와 홀수로 나눈다음 생각을 하자. 짝수일땐 정확히 반으로 나뉘거나, 가운데에 가로가 있을 수 있다. 즉 두번으로 나눠서 더해줘야 한다. 홀수일땐 가운데 세로 1개짜리가 있을때이므로 한번에 구할 수 있다.

하지만 답은 계속해서 오답이 나왔다.

    for(test_case = 0; test_case  < T; test_case++)
    {
        cin >> l; //l은 전체 길이
        cout << (find_MAX(l)- find_symmetry(l))% MOD << endl;
    }
    
분명 자료형도 확인했고, 예제도 정답이 잘 나오고 나머지 계산도 했는데 왜일까.
만약 내 논리가 맞았다면 답이 출력되는 부분에서 오류가 있다고 생각했다. 여기서부턴 책을 확인했다. 

### MODULAR 연산의 함정

    (find_MAX(l)- find_symmetry(l))% MOD
    
**find_MAX(l)이 만약 MOD값보다 커서 나머지 계산이 되었고, find_symmetry(l)이 MOD값보다 작아서 원래 값이 나왔다면** 
결과가 음수값이 나온다. 이것은 정말 중요했다. 실제 대회에서 
모든게 다 맞는데 오답이 나온다면 얼마나 멘붕이겠는가. 
`그래서 MOD계산을 해줄 때는 MOD를 한번 더해줘야 한다!!!`
그래서 대칭인 갯수를 구하는 함수를 사용하지 않고 출력할 때 경우를 나눠서 구현했다.

### 정답이 뜬 코드를 보자

    #include <iostream>

    #define MOD 1000000007

    using namespace std;

    int l;

    unsigned long long cache[101];

    unsigned long long find_MAX(int l) {

        if(l < 0)
            return 0;

        unsigned long long& ret = cache[l];
        if(ret != 0) return ret;

        ret = (find_MAX(l - 1) + find_MAX(l - 2))%MOD;

        if(l == 0)
            ret = 1;

        return ret;
    }

    int main(int argc, char** argv)
    {
        int T, test_case;

        freopen("input.txt", "r", stdin);

        cin >> T;
        for(test_case = 0; test_case  < T; test_case++)
        {
            cin >> l;
            if(l%2 == 0){
                int ret = find_MAX(l);
                ret = (ret - find_MAX(l/2) + MOD)%MOD;
                ret = (ret - find_MAX(l/2 - 1) + MOD)%MOD;
                cout << ret << endl;
            } else {
                cout << (find_MAX(l)- find_MAX(l/2) + MOD)%MOD << endl;
            }

        }
        return 0;
    }
    
전체 코드이다. 값을 return하기 전에 항상 나머지 계산을 했다. 꼭 잊지 말고 확인해야할 부분이다.

이 문제를 푼 다른 분들은 전체 갯수를 구하는 함수를 보고 아마 피보나치 수열을 떠올렸을 수도 있다. 이 문제는 피보나치로 충분히 간단하게 구할 수 있다. 최대의 갯수를 피보나치 수열로 구하고, 구하는 도중에 전체 길이를 반으로 나눴을 때 해당하는 절반의 값을 미리 저장하면 된다.

### 그럼 피보나치 수열을 이용한 코드를 보자

	fibo_2 = 0; //find_MAX[l-2]
    fibo_1 = 1; //find_MAXX[l-1]
    fibo = 0; // find_MAX[l]
    
    int s1, s2 = 0; //s1 = 반으로 딱 나뉠때 갯수, s2 = 가운데 뭔가가 있을때 갯수
    
    if(l <= 2)
    	return 0;	// 2개 이하는 무조건 대칭이므로
    
    for(int i = 0; i < l; i++) {
    	fibo = fibo_1 + fibo_2
        if(i == l/2) {	//대칭일 경우의 수를 for 문 돌리면서 저장
        	s1 = fibo_1;
            s2 = fibo;
            if(l%2 != 0)	// 홀수일땐 필요 없는 값
            	s1 = 0;
        }
        
        fibo_2 = fibo_1;
        fibo_1 = fibo;
        
    }
    cout << (fibo - (s1 + s2)%mod + mod)%mod <<endl;
    
원리는 같다. 구현에서 달랐을 뿐. *이제부턴 modular 연산을 주의해야한다!!!*