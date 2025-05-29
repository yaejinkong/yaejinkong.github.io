---
layout: post
title:  "[프로그래머스/159993] 미로 탈출"
date:   2025-05-29 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-05-13-coding-test.md
  - study/_posts/coding-test/2025-05-28-coding-test.md
  - study/_posts/coding-test/2025-05-28-coding-test(2).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 159993](https://school.programmers.co.kr/learn/courses/30/lessons/159993)

## ☑️ 풀이

```java
import java.util.*;
class Node {
    int x;
    int y;
    
    public Node(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Solution {
    static String[][] maze;
    static final int[] dirX = {0, 0, 1, -1};
    static final int[] dirY = {1, -1, 0, 0};
    static Node startN; 
    static Node endN;
    static Node leverN;
    
    public int solution(String[] maps) {
        int answer = 0;
        maze = new String[maps.length][maps[0].length()];
        
        // 1. 
        for (int i = 0; i < maps.length; i++) {
            for (int j = 0; j < maps[i].length(); j++) {
                char c = maps[i].charAt(j);
                if (c == 'S') {
                    startN = new Node(i, j);
                } else if (c == 'E') {
                    endN = new Node(i, j);
                } else if (c == 'L') {
                    leverN = new Node(i, j);
                }
                
                maze[i][j] = String.valueOf(c);
            }
        }
        
        // 2.
        int startToLever = bfs(startN, leverN);
        int leverToEnd = bfs(leverN, endN);
        
        // 7.
        return (startToLever == 0 || leverToEnd == 0) ? -1 : startToLever + leverToEnd;
    }
    
    private static int bfs(Node start, Node end) {
        Queue<Node> q = new LinkedList<>();
        q.add(start);
        // 3. 
        int[][] countMap = new int[maze.length][maze[0].length];
        
        // 4.
        while (!q.isEmpty()) {
            Node now = q.poll();
            for (int i = 0; i < 4; i++) {
                int nx = now.x + dirX[i];
                int ny = now.y + dirY[i];
                
		            // 5.
                if (nx >= 0 && nx < maze.length &&
                    ny >= 0 && ny < maze[0].length &&
                    !maze[nx][ny].equals("X") &&
                    countMap[nx][ny] == 0) {
                    countMap[nx][ny] = countMap[now.x][now.y] + 1;
                    q.add(new Node(nx, ny));
                }
            }
        }
        
        // 6.
        return countMap[end.x][end.y]; 
        
    }
}
```

- 이 문제의 포인트는 레버를 당겨야지 출구로 갈 수 있는 점을 생각해야한다.
    - 레버를 당긴 후에는 지났던 길도 다시 지날 수 있다.
    - 그래서 **`시작지점 - 레버까지의 최단 거리` + `레버 - 종료지점까지의 최단 거리`** 를 따로 구해야 한다.
    - 두 범위를 나누어서 너비 우선 탐색을 진행했다.
- 풀이 과정
    1. 문자열로 이루어진 배열인 maps의 각 문자를 2차원 배열로 분리한다. 
        1. 이 과정에서 시작 지점(startN), 종료 지점(endN), 레버(leverN) 위치를 따로 저장해준다. 
    2. `시작지점 - 레버까지의 최단 거리(startToLever)`과 `레버 - 종료지점까지의 최단 거리(leverToEnd)`를 구하기 위해 너비 우선 탐색을 한다.
    3. 이전의 너비 우선 탐색과 다른 점은 최단 거리를 저장하는 배열을 bfs 메서드 안에서 선언한다. 
    4. Queue가 빌 때까지 현재 노드(now) 기준 네 방향으로 탐색한다.
    5. 범위를 벗어나지 않고, 지나갈 수 있는 통로(`!maze[nx][ny].equals("X")`)여야 하고, 이전에 방문하지 않았다면(`countMap[nx][ny] == 0`)
        - 현재 노드(now)에서 1을 더한 값을 다음 노드의 최단 거리(`countMap[nx][ny]`)로 저장한다.
        - 큐에 다음 노드를 추가한다.
    6. 도착지점의 최단 거리(`countMap[end.x][end.y]`)를 반환한다.
    7. `시작지점 - 레버까지의 최단 거리(startToLever)`과 `레버 - 종료지점까지의 최단 거리(leverToEnd)` 중 하나라도 0이라면 즉, 
        1. 레버에 도착하지 못했거나, 
        2. 종료 지점에 도착하지 못했거나
        3. -1을 반환하고, 그렇지 않다면 두 거리를 더한 값을 반환한다.
- 시간 복잡도
    - map의 한 변의 길이 : N
    - map의 크기 → **N * N**
    - 4방향을 모두 탐색하므로 각 칸에서 최대 4번의 연산을 수행함 → **O(4 * N^2)**
    - 최종 시간 복잡도 → **O(N^2)**