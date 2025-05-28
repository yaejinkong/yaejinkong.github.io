---
layout: post
title:  "[알고리즘] DFS/BFS"
date:   2025-05-13 21:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-29-coding-test.md
  - study/_posts/coding-test/2025-04-29-coding-test(2).md
  - study/_posts/coding-test/2025-04-30-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

깊이 우선 탐색으로 모든 그래프의 노드를 순회하시오.

시작노드는 start로 주어지고, graph는 [출발노드, 도착 노드] 쌍들이 들어있는 리스트이다.

반환값은 그래프의 시작 노드부터 모든 노드를 깊이 우선 탐색으로 진행한 순서대로 노드가 저장된 리스트이다.

## ☑️ 풀이

### 깊이 우선 탐색 순회

```java
import java.util.*;

class Main {
    private static ArrayList<Integer>[] list; // ArrayList 배열
    private static boolean[] visited; // 방문했는지 체크
    private static ArrayList<Integer> answerList; // 정답 리스트
    public static void main(String[] args) {
        int[][] graph = {{1, 2}, {1, 3}, {2, 4}, {2, 5}, {3, 6}, {5, 6}};
        int n = 6; // 노드 개수
        int start = 1; // 시작 노드

				// 1.
        list = new ArrayList[n + 1];
        
        // 2. 
        for (int i = 0; i < list.length; i++) {
            list[i] = new ArrayList<>(); // 초기화
        }

				// 3. 
        for (int[] arr : graph) {
            list[arr[0]].add(arr[1]);
        }
        
        visited = new boolean[n + 1];
        answerList = new ArrayList<>();
        
        // 5. 
        dfs(start);
        
        // 6.
        int[] answerArr = answerList.stream().mapToInt(Integer::intValue).toArray();
        System.out.println(Arrays.toString(answerArr));
    }
		
		// 4. 
    private static void dfs(int now) {
        visited[now] = true; 
        answerList.add(now);

        for (int next : list[now]) {
            if (!visited[next]) {
                dfs(next);
            }
        }
        
    }
}
```

1. 인접 리스트를 저장할 ArrayList를 선언한다. 
    1. 인덱스는 0부터, 노드는 1부터 시작하니까 `[노드 개수 + 1]`로 크기를 설정한다. 
2. 반복문을 돌면서 각 인접 리스트를 초기화해준다. 
3. graph를 순회하면서 출발 노드를 인덱스로 하여 도착 노드를 각각의 인접 리스트에 추가한다.
4. dfs 메서드를 정의한다.
    1. 현재 노드를 방문했으니 **visited** 배열에 **true**로 저장한다. 
    2. answerList에 현재 노드를 추가한다. 
    3. 현재 노드와 인접한 노드를 순회하며 dfs 메서드를 다시 실행한다. 
5. 시작 노드에서 깊이 우선 탐색을 시작한다.
6. answerList를 반환한다.

### 시간 복잡도

- 노드의 개수 N개
- 간선의 개수 E개
- 인접 리스트 생성 → **O(E)**
- 깊이 우선 탐색 → **O(N)**
- 최종 시간 복잡도 → **O(N + E)**

### 넓이 우선 탐색 순회

```java
import java.util.*;

class Main {
    private static ArrayList<Integer>[] list; // ArrayList 배열
    private static boolean[] visited;
    private static ArrayList<Integer> answerList;
    public static void main(String[] args) {
        int[][] graph1 = {{1, 2}, {1, 3}, {2, 4}, {2, 5}, {3, 6}, {3, 7}, {4, 8}, {5, 8}, {6, 9}, {7, 9}};
        int[][] graph2 = {{1, 3}, {3, 4}, {3, 5}, {5, 2}};
        int n = 9;
        int start = 1;

        list = new ArrayList[n + 1];
        
        for (int i = 0; i < list.length; i++) {
            list[i] = new ArrayList<>();
        }

        for (int[] arr : graph1) {
            list[arr[0]].add(arr[1]);
        }

        // bfs 순회
        visited = new boolean[n + 1];
        answerList = new ArrayList<>();
        bfs(start);
    
        int[] answerArr = answerList.stream().mapToInt(Integer::intValue).toArray();
        System.out.println(Arrays.toString(answerArr));
    }

    private static void bfs(int start) {
		    // 1.
        Queue<Integer> queue = new LinkedList<>();
        queue.add(start);
        visited[start] = true;
				
				// 2.
        while (!queue.isEmpty()) {
            int now = queue.poll();
            answerList.add(now);
						
						// 3.
            for (int next : list[now]) {
                if (!visited[next]) {
                    queue.add(next);
                    visited[next] = true;
                }
            }
        }
        
    }
}
```

- dfs와 메서드 선언 부분을 제외하면 모두 동일하다.
    1. **Queue**를 선언해서 시작 노드부터 탐색할 수 있도록 추가한다.
        1. 시작 노드를 방문했으니 **visited** 배열에 **true**로 저장한다. 
    2. **Queue**가 비어있지 않다면 **poll()** 한 노드를 answerList에 추가한다.
    3. **poll()** 한 노드와 인접한 노드들을 다시 **Queue**에 추가하고 방문처리한다. 
    4. **Queue**가 비는 시점까지 계속해서 2, 3번을 반복한다.

### 시간 복잡도

- 노드의 개수 N개
- 간선의 개수 E개
- 인접 리스트 생성 → **O(E)**
- 넓이 우선 탐색 → **O(N)**
- 최종 시간 복잡도 → **O(N + E)**