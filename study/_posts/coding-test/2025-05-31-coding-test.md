---
layout: post
title:  "[프로그래머스/12978] 배달"
date:   2025-05-31 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-05-28-coding-test.md
  - study/_posts/coding-test/2025-05-28-coding-test(2).md
  - study/_posts/coding-test/2025-05-29-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 12978](https://school.programmers.co.kr/learn/courses/30/lessons/12978)

## ☑️ 풀이

```java
import java.util.*;

class Node {
    int dest; 
    int cost;
    
    public Node (int dest, int cost) {
        this.dest = dest;
        this.cost = cost;
    }
}

class Solution {
    static ArrayList<Node>[] list;
    static int[] dist;
    
    public int solution(int N, int[][] road, int K) {
        int answer = 0;
        list = new ArrayList[N + 1];
        dist = new int[N + 1];
        
        // 1. 인접 리스트 초기화
        for (int i = 1; i <= N; i++) {
            list[i] = new ArrayList<>();
        }
        
        // 2. 인접 리스트 저장
        for (int[] r : road) {
            list[r[0]].add(new Node(r[1], r[2]));
            list[r[1]].add(new Node(r[0], r[2]));
        }
        
        // 3. 모든 노드의 거리를 최댓값으로 초기화
        Arrays.fill(dist, Integer.MAX_VALUE);
        
        // 4. 탐색 시작
        bfs();
    
		    // 8. 
        for (int i = 1; i <= N; i++) {
            if (dist[i] <= K) {
                answer++;
            }
        }
        
        return answer;
    }
    
    private static void bfs () {
			  // 5. 우선순위큐 생성
        PriorityQueue<Node> pq = new PriorityQueue<>((o1, o2) -> Integer.compare(o1.cost, o2.cost));
        
        // 6. 
        q.add(new Node(1, 0));
        dist[1] = 0;
        
        // 7.
        while(!q.isEmpty()) {
            Node now = pq.poll();
            
            if (dist[now.dest] < now.cost) {
                continue;
            }
            
            for (Node next : list[now.dest]) {
                if (dist[next.dest] > dist[now.dest] + next.cost) {
                    dist[next.dest] = dist[now.dest] + next.cost;
                    pq.add(new Node(next.dest, dist[next.dest]));
                }
            }
        }
    }
}
```

- 다익스트라 알고리즘을 사용해서, 시작 노드에서 나머지 노드들까지의 최소 비용을 구하려고 했다.
    - 기존의 다익스트라 구현 방식를 적용했지만 **양방향으로 통행할 수 있는 도로**라는 점을 놓쳐서 실수했다.
    - 인접 리스트를 구현할 때 양방향으로 간선을 추가하면 된다.
- 풀이 과정
    1. 간선 정보를 인접 리스트를 초기화한다. 
    2. 양방향으로 road의 정보를 인접 리스트에 저장한다.
    3. 최소 거리를 저장할 dist 배열의 값을 모두 최댓값으로 저장한다.
    4. 다익스트라 탐색을 시작한다.
    5. 탐색에서 사용할 우선순위 큐를 생성하고, Node의 가중치를 기준으로 오름차순하도록 정렬한다. 
        1. 우선순위 큐를 사용한 이유 : 최소 비용을 구하는 과정에서, 현재까지 선택하지 않은 노드 중 최소 비용의 값이 최소인 노드를 선택하기 위해 
    6. 우선순위 큐에 시작 노드를 추가하고, 시작 노드의 최소 비용 값(dist[1])을 0으로 설정한다.
    7. 우선순위 큐에서 현재 노드(now)를 꺼낸 후,
        1. 큐에서 가져온 최소 비용 값(dist[now.dest]) < 현재 노드의 최소 비용 값(now.cost)이면 현재 노드의 최소 비용 값이 최소가 아니므로 무시
        2. 위에 해당하지 않으면, 다음 노드의 최소 비용(next.cost) 값과 큐에서 가져온 최소 비용 값을 비교해서 최소 값으로 갱신한다. 
    8. 마지막에 완성된 dist 배열을 순회하면서, 음식 배달이 가능한 시간(K) 이하인 노드의 개수를 구하여 반환한다.
- 시간 복잡도
    - N은 Node 개수, E는 간선 개수
    - 인접 리스트에 Node 추가 → **O(E)**
    - 최소 거리 배열(dist) 초기화 → **O(N)**
    - 다익스트라 알고리즘
        - 우선순위 큐에 poll() → **O(N * logN)**
        - 우선순위 큐에 add() → **O(E * logN)**
    - 결과 리스트 순회 → **O(N)**
    - 최종 시간 복잡도 → **O((N + E) logN)**