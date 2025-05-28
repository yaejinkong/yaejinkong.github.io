---
layout: post
title:  "[프로그래머스/1844] 게임 맵 최단거리2"
date:   2025-05-28 22:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-30-coding-test.md
  - study/_posts/coding-test/2025-05-13-coding-test.md
  - study/_posts/coding-test/2025-05-28-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 1844](https://school.programmers.co.kr/learn/courses/30/lessons/1844)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Node {
    int x = 0;
    int y = 0;
    
    public Node (int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Solution {
		// 상하좌우 탐색을 위한 방향 배열
    static final int[] dirX = {0, 0, 1, -1};
    static final int[] dirY = {1, -1, 0, 0,};
    static int[][] maps; // 누적 거리를 저장할 maps
    static boolean[][] visited; // 방문 여부 체크 배열
    private static int endX; // maps의 가로 길이
    private static int endY; // maps의 세로 길이
    
    public int solution(int[][] maps) {
        endX = maps.length; 
        endY = maps[0].length;
        visited = new boolean[endX][endY];
        this.maps = maps;
        
        // 1. 
        bfs(0, 0); 
        
        // 5.
        return visited[endX - 1][endY - 1] ? maps[endX - 1][endY - 1] : -1;
    
    }
    
    private static void bfs(int x, int y) {
		    // 2. 
        Queue<Node> q = new LinkedList<>();
        q.add(new Node(x, y)); 
        visited[x][y] = true;
        
        while (!q.isEmpty()) {
		        // 3.
            Node node = q.poll();
           
            // 4.
            for (int i = 0; i < 4; i++) {
                int nx = node.x + dirX[i];
                int ny = node.y + dirY[i];
                
                if (nx >= 0 && nx < endX && ny >= 0 && ny < endY && 
                    maps[nx][ny] == 1 && !visited[nx][ny]) {
                    visited[nx][ny] = true;
                    q.add(new Node(nx, ny));
                    maps[nx][ny] = maps[node.x][node.y] + 1; 
                }
            }
        }
    }
}
```

- 지난 번에 풀었던 문제지만, 다시 한 번 더 풀어보았다.
- 최단 거리를 구하는 문제기 때문에 **너비 우선 탐색(BFS)**을 이용하면 된다.
    1. (0, 0)을 시작점으로 해서 bfs 탐색을 한다.
    2. 탐색을 위한 Queue를 생성하며, 탐색은 Queue가 빌 때까지 동작한다.
    3. Queue에서 가장 앞의 Node를 꺼낸다.
    4. Node의 상하좌우(nx, ny)를 탐색한다. 
        1. `0 ≤ nx < endX`, `0 ≤ ny < endY`, 벽이 아니어야 함(`maps[nx][ny] == 1`), 방문하지 않은 칸이어야 함(`!visited[nx][ny]`) → 4가지 조건을 모두 충족하면,
        2. 방문 체크하고, Queue에 해당 Node를 추가하고,
        3. 이전 Node의 누적 거리(`maps[node.x][node.y]`)에 1을 더한 값을 다시 `maps[nx][ny]`에 저장한다. 
    5. 탐색 종료 후, 상대 팀 진영에 방문했다면(`visited[endX - 1][endY - 1]`) 최단거리(`maps[endX - 1][endY - 1]`)를 반환하고, 방문하지 않았다면 -1를 반환한다.
- 시간복잡도
    - maps의 크기는 **N * M**
    - Queue에 들어갈 수 있는 노드의 개수 → **O(N * M)**
    - Queue에서 꺼낼 때마다 4방향으로 수행하는 연산 → **O(1)**
        - 범위 검사 → **O(1)**
        - 방문 여부 확인 → **O(1)**
        - Queue에 추가 → **O(1)**
    - 최종 시간복잡도 → **O(N * M)**