---
layout: post
title:  "[알고리즘] 다익스트라"
date:   2025-05-28 21:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-29-coding-test(2).md
  - study/_posts/coding-test/2025-04-30-coding-test.md
  - study/_posts/coding-test/2025-05-13-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

주어진 그래프와 시작 노드를 이용하여 다익스트라 알고리즘을 구현하라. 

start는 출발 노드, n은 노드의 개수를 의미한다.

graph는 배열로 주어지며 노드의 연결 정보와 가중치가 저장되어 있다. 

반환값은 시작 노드부터 시작 노드를 포함한 모든 노드까지의 최단 거리를 순서대로 저장한 배열이다.

## ☑️ 첫 번째 풀이

{% raw %}
```java
import java.util.*;

class Main {
		// 1. Node 클래스 선언
    private static class Node {
        int dest; // 목적지
        int cost; // 가중치

        public Node(int dest, int cost) {
            this.dest = dest;
            this.cost = cost;
        }
    }
    
    public static void main(String[] args) {
        int[][] graph1 = {{0, 1, 9}, {0, 2, 3}, {1, 0, 5}, {2, 1, 1}};
        int[][] graph2 = {{0, 1, 1}, {1, 2, 5}, {2, 3, 1}};
        int n = 3;
        int start = 0;

        // 2. 리스트 초기화
        ArrayList<Node>[] list = new ArrayList[n];
        for (int i = 0; i < list.length; i++) {
            list[i] = new ArrayList<>();
        }
        
        // 3. 인접 리스트 노드 추가
        for (int[] arr : graph1) {
            list[arr[0]].add(new Node(arr[1], arr[2]));
        }

        // 4. 거리를 무한대로 초기화
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);

        // 5. 시작 노드의 거리 값은 0으로 초기화
        dist[start] = 0;

        // 6. 우선순위 큐를 생성하고 시작 노드를 삽입
        PriorityQueue<Node> pq = new PriorityQueue<>((o1, o2) -> Integer.compare(o1.cost, o2.cost));
        pq.add(new Node(start, 0));

				// 7.
        while (!pq.isEmpty()) {
            Node now = pq.poll();

            // 큐에서 가져온 거리 값 < 현재 노드의 거리 값 -> 이미 방문한 노드(무시)
            if (dist[now.dest] < now.cost) {
                continue;
            }

            // 현재 노드와 인접한 노드들의 거리 값을 계산하여 업데이트
            for (Node next : list[now.dest]) {
                // 기존에 발견했던 거리보다 더 짧은 거리를 발견하면 거리 값을 갱신하고 큐에 넣음
                if (dist[next.dest] > now.cost + next.cost) {
                    dist[next.dest] = now.cost + next.cost;
                    pq.add(new Node(next.dest, dist[next.dest]));
                }
            }
        }
				
				// 8. 최단 거리 배열 출력
        System.out.println(Arrays.toString(dist));
        
    }

}
```
{% endraw %}

1. 문제에 필요한 Node 클래스를 먼저 정의한다.
    1. Node 클래스는 목적지(dest), 가중치(cost)를 인자로 가진다.  
2. graph의 정보를 인접리스트로 저장하기 전 for문으로 리스트를 초기화해준다.
3. graph의 정보를 인접리스트로 저장한다.
    1. graph를 for문으로 돌면서 인접리스트에 Node 객체를 만들어 저장한다.
    2. ex. `{0, 1, 9}` 이면, 인덱스가 0인 인접리스트에 목적지(dest)가 1, 가중치(cost)가 9인 Node 객체를 만들어 저장한다.
4. 최단거리를 저장할 dist 배열을 선언한다.
    1. 모든 시작 노드의 최단 거리 값을 무한대로 초기화한다.
5. 시작 노드의 최단 거리 값은 0으로 저장한다.
6. 우선순위 큐(pq)를 생성한다. 
    1. Node의 cost 오름차순 기준으로 우선순위를 정한다.
    2.  우선순위 큐에 먼저 목적지(dest)가 start, 가중치(cost)가 0인 Node 객체를 만들어 저장한다.
7. 우선순위 큐에서  현재 가장 최단거리가 짧은 Node(now)를 꺼낸다. 
    1. `큐에서 가져온 거리 값(dist[now.dest]) < 현재 Node의 최단거리값(now.cost)` 면 현재 Node는 최단거리가 아니므로 무시한다.
    2. `큐에서 가져온 거리 값(dist[now.dest])` > `현재 Node의 최단거리값(now.cost)` 인 경우
        1. 현재 Node(now)와 연결된 인접 리스트의 Node들(next)을 모두 확인한다.
        2. `큐에서 가져온 기존 최단거리(dist[next.dest])` > `현재 Node의 최단 거리 값(now.cost + next.cost)`이면 최단 거리 값을 갱신하고 우선순위 큐에 저장한다.
8. 최종적으로 저장된 dist 배열을 출력한다. 

### 시간 복잡도

- 노드 개수 N, 간선 개수 E
- 인접 리스트에 Node 추가 → **O(E)**
- dist 배열 초기화 → **O(N)**
- 현재 Node의 거리와 큐에서 가져온 거리 비교 → **O(N * logN)**
- 현재 Node와 인접한 Node들의 거리 값을 계산 → **O(E * logN)**
- 최종 시간 복잡도 → **O((N + E) logN)**

## ☑️ 두 번째 풀이

{% raw %}
```java
import java.util.*;

class Main {
		// 1. Node 클래스 선언
    private static class Node {
        int dest; // 목적지
        int cost; // 가중치

        public Node(int dest, int cost) {
            this.dest = dest;
            this.cost = cost;
        }
    }
    
    public static void main(String[] args) {
        int[][] graph1 = {{0, 1, 9}, {0, 2, 3}, {1, 0, 5}, {2, 1, 1}};
        int[][] graph2 = {{0, 1, 1}, {1, 2, 5}, {2, 3, 1}};
        int n = 3;
        int start = 0;

        // 2. 리스트 초기화
        ArrayList<Node>[] list = new ArrayList[n];
        for (int i = 0; i < list.length; i++) {
            list[i] = new ArrayList<>();
        }
        
        // 3. 인접 리스트 노드 추가
        for (int[] arr : graph1) {
            list[arr[0]].add(new Node(arr[1], arr[2]));
        }

        // 4. 거리를 무한대로 초기화
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);

        // 5. 시작 노드의 거리 값은 0으로 초기화
        dist[start] = 0;

        // 6. 방문 노드 체크 배열 생성
        boolean[] visited = new boolean[n]; 
        
        // 7. 우선순위 큐를 생성하고 시작 노드를 삽입
        PriorityQueue<Node> pq = new PriorityQueue<>((o1, o2) -> Integer.compare(o1.cost, o2.cost));
        pq.add(new Node(start, 0));

				// 8.
        while (!pq.isEmpty()) {
            Node now = pq.poll();

            // 9. 이미 방문한 노드면 무시
            if (visited[now.dest]) {
                continue;
            }
            visited[now.dest] = true;

            // 10. 현재 노드와 인접한 노드들의 거리 값을 계산하여 업데이트
            for (Node next : list[now.dest]) {
                // 기존에 발견했던 거리보다 더 짧은 거리를 발견하면 거리 값을 갱신하고 큐에 넣음
                if (dist[next.dest] > now.cost + next.cost) {
                    dist[next.dest] = now.cost + next.cost;
                    pq.add(new Node(next.dest, dist[next.dest]));
                }
            }
        }
				
		// 11. 최단 거리 배열 출력
        System.out.println(Arrays.toString(dist));
        
    }

}
```
{% endraw %}

- 방문 여부 저장 배열을 생성하여 사용한다.
    - 방문한 노드이면 탐색하지 않고,
    - 방문하지 않은 노드이면 방문 체크를 한 후 탐색한다.