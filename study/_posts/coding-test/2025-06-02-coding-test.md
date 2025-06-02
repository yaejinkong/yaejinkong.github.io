---
layout: post
title:  "[프로그래머스/67259] 경주로 건설"
date:   2025-06-02 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-05-28-coding-test(2).md
  - study/_posts/coding-test/2025-05-29-coding-test.md
  - study/_posts/coding-test/2025-05-31-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 67259](https://school.programmers.co.kr/learn/courses/30/lessons/67259)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Node {
    int x, y, dir, cost;
    
    public Node(int x, int y, int dir, int cost) {
        this.x = x;
        this.y = y;
        this.dir = dir;
        this.cost = cost;
    }
}

class Solution {
    static int[][] board;
    static int[][][] countBoard;
    static final int[] dirX = {0, -1, 0, 1}; // 좌 하 우 상
    static final int[] dirY = {-1, 0, 1, 0};
    static int N;
    static int answer = Integer.MAX_VALUE;
    
    public int solution(int[][] board) {
        N = board.length;
        this.board = board;
        countBoard = new int[N][N][4]; // 1. 
        bfs(); // 2. 
        
        return answer;
    }
    
    private static int calculate(int dir, int prevDir, int prevCost) {
        if (prevDir == -1 || (dir - prevDir) % 2 == 0) {
            return prevCost + 100;
        } else {
            return prevCost + 600;
        }
    }
    
    private static void bfs() {
        Queue<Node> q = new LinkedList<>();
        q.add(new Node(0, 0, -1, 0)); // 처음에는 방향이 없어서 -1
        
        while (!q.isEmpty()) {
            Node now = q.poll();
            
			      // 3.
            for (int i = 0; i < 4; i++) {
                int nx = now.x + dirX[i];
                int ny = now.y + dirY[i];
                
                
                if (nx >= 0 && nx < N && ny >= 0 && ny < N && board[nx][ny] == 0) {
                    // 4. 
                    int newCost = calculate(i, now.dir, now.cost); 
                    
                    // 5. 
                    if (nx == N - 1 && ny == N - 1) {
                        answer = Math.min(answer, newCost);
                    }
                    // 6. 
                    else if (countBoard[nx][ny][i] == 0 || countBoard[nx][ny][i] > newCost) {
                        countBoard[nx][ny][i] = newCost;
                        q.add(new Node(nx, ny, i, newCost));                  
                    }
                }
                
            }
        }
    }
}
```

- 경주로를 건설하는데 필요한 최소비용을 계산하는 문제니까 너비 우선 탐색(BFS)로 풀어야 하고, 직선 도로인지 코너 도로인지에 따라 건설 비용이 달라지는 걸 고려해야 한다.
    - 최소 비용 갱신을 위해 필요한 건 `현재 도로 좌표`, `현재 도로 방향`, `현재까지 도로 건설 비용` → 이를 인자로 가지는 **Node** 객체를 사용하여 Queue에 추가하도록 한다.
    - 방향 전환을 위해 사용할 **dirX**, **dirY** 배열은 `좌, 하, 우, 상` 순서로 저장해야 했다.
        - `좌, 하, 우, 상` 의 인덱스는 `0, 1, 2, 3`
        - 이전 도로 방향과 현재 도로 방향이 주어졌을 때, 완전히 반대인 경우(`좌-우`, `상-하`)를 제외하고는 다 갱신한다.
- 풀이 과정
    1. `countBoard[x][y][dir]` 선언
        1. 도착한 방향에 따라 비용이 달라지기 때문에 방향 별 최소 비용을 기록하기 위해 3차원 배열로 선언한다.
    2. 너비 우선 탐색을 Queue가 빌 때까지 수행한다.
    3. 현재 좌표를 기준으로 상하좌우를 체크한다. 
    4. 해당 좌표가 board를 벗어나지 않는다면, calculate 메서드를 사용하여 경주로 건설 비용(newCost)을 계산한다.
        1. 이전 도로 방향이 정해지지 않거나(`-1`), 이전 도로 방향과 현재 도로 방향이 동일하거나 완전히 반대인 경우 → **직선 도로**이므로, `현재 비용(cost) + 100`으로 갱신
        2. 이외의 경우 → **코너 도로**이므로, `현재 비용(cost) + 100 + 500`으로 갱신
    5. 해당 좌표가 도착지(`N - 1, N - 1`)면 answer에 최소 비용을 갱신한다.
    6. 도착지가 아니면,
        1. 아직 해당 Node를 방문하지 않았거나(`countBoard[nx][ny][i] == 0`),
        2. 해당 Node의 최소 비용의 값(`countBoard[nx][ny][i]`)이 현재 계산한 새로운 최소 비용(newCost)보다 크면
        3. 해당 좌표의 최소 비용으로 갱신하고, 큐에 추가한다.
- 시간 복잡도
    - board의 한 변의 길이 → N
    - 모든 Node를 탐색→ **O(N * N)**
    - 각 Node에서 최대 4방향을 탐색 → **O(4 * N * N)**
    - 최종 시간 복잡도 → **O(N ^ 2)**