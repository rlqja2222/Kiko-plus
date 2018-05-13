---
layout: post
title: "단어제한 끝말잇기"
description: "ID : WORDCHAIN"
date: 2017-07-20
tags: [알고리즘, 알고스팟, 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, DFS, graph, 오일러]
comments: true
share: true

---

WORDCHAIN 문제의 포인트는 2가지이다.
* a단어의 끝 글자와 b단어의 첫 글자를 비교해야 한다.
* 찾았을 경우 단어를 출력해야 한다.

처음에 들었던 생각은 전부 검색해보는 것이였다. adj[100][100]배열을 만든 후 처음과 끝이 같을 때 1로 해놓고 재귀적으로 dfs를 하면서 **끝에 도달했을때 총 단어의 숫자와 같다면** 스택에 넣으면서 재귀 반환을 하였다.

### 일단 코드를 보자

	for(int i = 0; i < n; i++){
        count_word = 0;
        solve(i);

        if(st.size() == n)
            break;
    }

    if(st.size() != n)
        cout << "IMPOSSIBLE";
    else
        while(!st.empty()){
            cout << s[st.top()] << " ";
            st.pop();
        }
        
count_word는 전역 변수로 dfs내에서 재귀로 들어갈때마다 카운트를 증가시켜 총 단어와 비교하게 된다.

### dfs함수를 보자
    string s[100];
    int visited[100];
    stack<int> st;
    int count_word;

    void solve(int start) {
        visited[start] = 1;
        for(int i = 0; i < n; i++)
            if(adj[start][i] == 1 && !visited[i]){
                count_word++;
                solve(i);
            }
        if(count_word == n - 1)
            st.push(start);
        else{
            count_word--;
            visited[start] = 0;
        }
    }
    
시작점부터 모든 경우의 수를 해본다. 하지만 이 방법은 당연히 시간초과가 나온다. 최대 100!의 경우의 수가 나오기 때문이다. 그렇다면 어떻게 풀어야 할까. 이 문제의 경우 어느정도 수학적 기본 지식이 있어야 한다.

### 오일러 서킷과 오일러 트레일
> 오일러 서킷은 시작점에서 모든 간선을 통과하고 다시 시작점으로 돌아오는 서킷.
> 
> 오일러 트레일은 모든 간선을 통과하지만 시작점은 다른 통로.

둘다 모든 간선을 통과한다점에서 같지만 끝나는 점이 다르다. 여기 문제에서는 모든 단어를 사용해야하므로 모든 간선을 통과해야하는 오일러를 사용할 수가 있다. 하지만 단어를 간선에 대응하기위해선 정점이 시작단어와 끝단어가되어야 한다. 또 하나의 포인트는 경우의 수를 조사하기 전에 오일러가 가능한지 알 수 있다.
* 오일러 특성상 시작점과 끝점이 같거나 시작점과 끝점이 다를 경우 한개만 존재해야 한다.
* 두가지 경우를 먼저 조사한 후 오일러 함수를 적용할 수 있다.

### 오일러를 적용하기 위한 그래프 생성 함수를 보자.
    vector<vector<int> > adj;
    vector<string> graph[26][26];
    vector<int> indegree, outdegree;

    void makeGraph(const vector<string>& words){
        for(int i = 0; i < 26; i++)
            for(int j = 0; j < 26; j++)
                graph[i][j].clear();

        adj = vector<vector<int> >(26, vector<int>(26, 0));
        indegree = outdegree = vector<int>(26, 0);

        for(int i = 0; i < words.size(); i++){
            int a = words[i][0] - 'a';
            int b = words[i][words[i].size() - 1] - 'a';
            graph[a][b].push_back(words[i]);
            adj[a][b]++;
            outdegree[a]++;
            indegree[b]++;
        }
    }
    
indegree와 outdegree는 오일러 특성인 끝점과 시작점의 차이를 조사할때 사용하기 위해 값을 저장한다. 각 정점당 outdegree와 indegree차이가  (1, -1) 이거나 모두 0이여야 한다.

### 오일러 체크하는 함수를 보자.
    bool checkEuler(){
        int plus1 = 0, minus1 = 0;
        for(int i = 0; i < 26; i++){
            int delta = outdegree[i] - indegree[i];
            if(delta < -1 || 1 < delta) return false;
            if(delta == 1) plus1++;
            if(delta == -1) minus1++;
        }
        return (plus1 == 1 && minus1 == 1) || (plus1 == 0 && minus1 == 0);
    }
    
모든 점을 조사하면서 차이를 확인한다. 

### 오일러를 확인하는 함수를 보자
    void getEulerCircuit(int here, vector<int>& circuit){
        for(int there = 0; there < adj.size(); there++)
            while(adj[here][there] > 0){
                adj[here][there]--;
                getEulerCircuit(there, circuit);
            }
        circuit.push_back(here);
    }

    vector<int> getEulerTrailOrCircuit(){
        vector<int> circuit;
        for(int i = 0; i < 26; i++)
            if(outdegree[i] == indegree[i] + 1){
                getEulerCircuit(i, circuit);
                return circuit;
            }
        for(int i = 0; i < 26; i++){
            if(outdegree[i]){
                getEulerCircuit(i, circuit);
                return circuit;
            }
        }
        return circuit;
    }
    
이 함수는 dfs개념을 이용해서 오일러 서킷을 반환한다.위 부분이 트레일 일때, 아랫 부분이 서킷일때이다.

### 마지막 최종함수를 보자
    string solve(const vector<string>& words){
        makeGraph(words);
        if(!checkEuler()) return "IMPOSSIBLE";
        vector<int> circuit = getEulerTrailOrCircuit();
        if(circuit.size() != words.size() + 1) return "IMPOSSIBLE";

        reverse(circuit.begin(), circuit.end());
        string ret;
        for(int i = 1; i < circuit.size(); i++){
            int a = circuit[i - 1], b = circuit[i];
            if(ret.size()) ret += " ";
            ret += graph[a][b].back();
            graph[a][b].pop_back();
        }
        return ret;
    }
    
먼저 오일러가 가능한지 체크한후 오일러를 조사한다. 여기서 뛰어쓰기를 집어넣는 부분도 확인을 하자.

이 문제는 오일러의 지식이 없다면 상당히 접근하기 어렵다. 오일러를 알더라도 코드를 직접 짜보지 않았다면 시간이 꽤나 걸릴 문제이다. 이 문제를 통해 오일러에 대해 깊게 공부하게 되었다.