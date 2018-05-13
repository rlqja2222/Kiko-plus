---
layout: post
title: "고대어 사전"
description: "ID : DICTIONARY"
date: 2017-07-19
tags: [알고리즘, 알고스팟, 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, DFS, graph]
comments: true
share: true

---

DICTIONARY의 문제의 포인트는 2가지이다.
* 문자열을 비교해서 순서를 가져야 한다.
* 여러개가 복합적으로 서로에게 순서 영향을 준다.

처음에 접근할 때는 간단한 순서 비교를 통해 서로의 인덱스에 표시를 했었다. 하지만 두번째 포인트처럼 단순히 a와 b 의 비교가 아닌, a와 b를 비교하고 c와 b 를 비교한다음 a와 c가 순서가 정해진다면 인덱스로 표시 하기가 어려워진다. 이것이 바로 dfs가 가진 장점이다.
이렇게 삽질을 해보면 dfs의 강력한 능력을 알 수 있다. 순서와 같이 영향을 주는 요인들을 영향을 주는 순서에 따라 정렬을 할수 있다. dfs의 개념은 간단하다.
> cycle 유무를 알 수 있다.
> 순서에 영향을 줄때 간편하게 정렬할 수 있다.
> **재귀**를 통해 영향을 주는 모든 요인을 검사할 수 있다.

### 책에서 제시하는 기본적인 dfs코드를 보자.
    vector<vector<int> > adj;
    vector<bool> visited;
    void dfs(int here){
        visited[here] = true;
        for(int i = 0; i < adj[here].size(); i++){
            int there = adj[here][i];
            if(!visited[there])
                dfs(there);
        }
    }
    
here에서 근접한 모든 노드를 검사하면서 끝에 도달하면 재귀를 통해 그 바로 전 시작점으로 돌아온다. 끝날때마다 해당 인덱스를 체크해주면 위상정렬이 자동으로 된다. 코드를 외울 필욘 없다. 하지만 어떻게 작동하는지 **개념은 꼭 알아야 한다!**

### 자 그럼 여기서 사용한 dfs 코드를 보자.
    void dfs(int here){
        visited[here] = 1;
        for(int i = 0; i < 26; i++)
            if(adj[here][i] == 1){
                int there = i;
                if(!visited[there])
                    dfs(there);
                else if(!finished[there]){
                    cycle = 1;
                    return;
                }
            }
        finished[here] = 1;
        st.push(here);
    }
    
책에 있는 코드에서 별로 변한건 없다. 하지만 cycle의 유무를 확인하기 위해 값을 finished라는 배열을 추가하여 해당 dfs를 포함하는 노드에 접근한다면 cycle를 체크했다. 그리고 끝에 도달할 때마다 스택에 넣어서 위상정렬을 했다.

### 나머지 코드를 보자
    for(int i = 0; i < 26; i++)
        if(!visited[i])
            dfs(i);

    if(cycle == 1){
        cout << "INVALID HYPOTHESIS" <<endl;
        while(!st.empty())
            st.pop();
    }
    else{
        while(!st.empty()){
            cout << (char)(st.top() + 'a');
            st.pop();
        }
        cout << endl;
    }
            
알파벳이 26개이므로 전부 돌아가면서 check를 하였고 cycle일 경우와 아닐 경우를 나눠서 체크했다. 책에서는 스택을 사용하지 않고 일단은 다 넣은다음 역방향 간선이 존재할 경우로 cycle 유무를 파악했다.

### 책에 있는 코드를 보자

    void dfs(int here){
        seen[here] = 1;
        for(int there=0;there<adj.size();there++)
            if(adj[here][there] && !seen[there])
                dfs(there);
        order.push_back(here);
    }

    //adj에 주어진 그래프를 위상정렬한 결과를 반환한다.
    //그래프가 DAG가 아니라면 빈 벡터를 반환한다.
    vector<int> topologicalSort(){
        int n = adj.size();
        seen = vector<int>(n,0);
        order.clear();
        for(int i=0;i<n;i++)
            if(!seen[i]) dfs(i);
        reverse(order.begin(), order.end());

        //만약 그래프가 DAG가 아니라면 정렬 결과에 역방향 간선이 있다.
        for(int i=0;i<n;i++)
            for(int j=i+1;j<n;j++)
                if(adj[order[j]][order[i]])
                    return vector<int>();
        //없는 경우라면 깊이 우선 탐색에서 얻은 순서를 반환한다.
        return order;
    }
    
DAG을 조사하는 방법도 여러가지가 있다. 각자 편한 방법으로 구현하면 된다.
