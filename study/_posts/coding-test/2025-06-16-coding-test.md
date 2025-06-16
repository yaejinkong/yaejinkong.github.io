---
layout: post
title:  "[프로그래머스/87946] 피로도"
date:   2025-06-16 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-06-05-coding-test.md
  - study/_posts/coding-test/2025-06-06-coding-test.md
  - study/_posts/coding-test/2025-06-13-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 87946](https://school.programmers.co.kr/learn/courses/30/lessons/87946)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    private static boolean[] visited; 
    private static int[][] Dungeons;
    private static int answer = 0;
    
	  private static void backtrack(int now, int cnt) {
        for (int i = 0; i < Dungeons.length; i++) {
            if (!visited[i] && now >= Dungeons[i][0]) { // 1. 
                visited[i] = true; // 2. 
                backtrack(now - Dungeons[i][1], cnt + 1); // 3. 
                answer = Math.max(answer, cnt + 1); // 4. 
                visited[i] = false; // 5.
            }
        }
    }   
    
    public int solution(int k, int[][] dungeons) {
        visited = new boolean[dungeons.length]; 
        Dungeons = dungeons;
        
        backtrack(k, 0);
        return answer;
    }
    
}
```

- **유망함수 조건**
    - **`현재 피로도 < 최소 필요 피로도`** 일 경우, 백트래킹 한다.
    - **`최소 필요 피로도`**는 **`소모 피로도`**보다 항상 크거나 같기 때문에 **`소모 피로도`**와의 관계는 고려할 필요가 없다.
- **풀이과정**
    1. 모든 던전을 순회하면서 **방문하지 않은 던전**이며, **현재 피로도가 최소 필요 피로도보다 크거나 같은 경우** 백트래킹을 수행한다.
    2. 해당 던전은 방문처리한다.
    3. 현재 피로도에서 소모 피로도를 뺀 값으로 현재 피로도를 갱신하고(**`now - Dungeons[i][1]`**), 현재까지의 최대 탐험 가능한 던전의 개수(**`cnt + 1`**)를 인자로 하여 백트래킹을 다시 수행한다.
    4. 백트래킹하며 구한 최대 방문 던전 수가 더 크면 answer를 갱신한다.
        1. 모든 탐색이 끝나면, 가장 많이 방문한 던전의 개수를 구할 수 있다.
    5. **`visited[i] = false`** → 백트래킹으로 다시 돌아갔을 때, 해당 던전을 선택지로 고려하기 위해 방문처리를 false로 바꾼다. 
- **시간복잡도**
    - 던전의 개수 N
    - 최악의 경우 모든 경로를 탐색하므로 N * (N - 1) * … * 1 → **O(N!)**
    - 실제로는 유망함수에 의해 훨씬 적음