---
layout: post
title:  "[프로그래머스/86971] 전력망을 둘로 나누기"
date:   2025-06-05 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-05-29-coding-test.md
  - study/_posts/coding-test/2025-05-31-coding-test.md
  - study/_posts/coding-test/2025-06-02-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 86971](https://school.programmers.co.kr/learn/courses/30/lessons/86971)

## ☑️ 풀이

```java
import java.util.*;

class Solution {
    static int answer;
    static boolean[] visited;
    static int N;
    static ArrayList<Integer>[] list;
    public int solution(int n, int[][] wires) {
        answer = n;
        list = new ArrayList[n + 1];
        visited = new boolean[n + 1];
        N = n;
        
        for (int i = 1; i <= n; i++) {
            list[i] = new ArrayList<>();
        }
        
        for (int i = 0; i < wires.length; i++) {
            list[wires[i][0]].add(wires[i][1]);
            list[wires[i][1]].add(wires[i][0]);
        }
        
        dfs(1);
        return answer;
    }
    
    private static int dfs(int now) {
        visited[now] = true;
        int sum = 0; // 1. 
        
        // 2. 
        for (int next : list[now]) {
            if (!visited[next]) {
                int cnt = dfs(next); // 3.
                answer = Math.min(answer, Math.abs(N - cnt * 2)); // 4.
                sum += cnt;
            }
        }
         // 5.
        return sum + 1;
    }
}
```

- 최소값을 구하는 게 아니라 **깊이 우선 탐색**을 사용하면 된다까지는 생각했는데, 전력망을 둘로 나누었을 때 두 그룹의 송전탑의 개수가 비슷하게 구하려면 어떻게 해야할 지를 찾지 못햇다.
    - 책을 보니, 전력망의 구조는 **하나의 트리**이기 때문에 전선을 하나 끊어도 무조건 둘로 나뉜다고 되어 있다.
    - 그러면 **`두 번째 그룹의 송전탑의 개수`** = **`전체 송전탑의 개수`** - **`첫 번째 그룹의 송전탑 개수`** 가 된다.
    - 이걸 이용해서 깊이 우선 탐색을 진행할 때 **`전체 노드의 개수 - 자식 트리의 노드 수 - 자식 트리의 노드 수`** 의 절댓값이 가장 작은 값을 구하면 쉽게 찾을 수 있다.
- 풀이 과정
    - 전선의 연결 정보를 인접 리스트에 저장한다.
    - 1번 노드부터 깊이 우선 탐색(DFS)을 시작한다.
        1. 자식 노드의 개수를 저장할 변수 sum을 선언한다. 
        2. 각 노드와 연결된 모든 노드를 확인한다. 이 때, 해당 노드를 방문하지 않았다면, 해당 노드와 연결된 모든 자식 노드에 대해 DFS를 호출한다. 
            1. 자식 노드를 방문한 후, 자식 노드에서 반환되는 서브 트리의 크기를 cnt로 받는다. 
        3. 서브 트리 크기를 합산한다.
            1. 각 자식 노드가 반환한 cnt 값을 sum에 더한다. 
            2. sum에 자식 노드들의 서브 트리의 크기를 합산하면, **`sum + 1`**을 반환하여 현재 노드를 포함한 서브트리 크기를 반환한다. 
        4. 서브 트리 차이를 계산한다. 
            1. 각 자식 노드의 서브 트리를 탐색할 때마다,
            2. **`전체 노드의 개수 - 자식 트리의 노드 수 - 자식 트리의 노드 수`** 의 절댓값이 작은 값으로 **answer**를 갱신한다.
        5. DFS 종료 후, 서브트리 크기를 반환한다. 
            1. 자식 노드를 모두 탐색했다면, 해당 노드의 서브 트리의 크기(**`sum + 1`**)를 반환하여 부모 노드로 돌아간다.