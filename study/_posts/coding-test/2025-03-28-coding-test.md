---
layout: post
title:  "[프로그래머스/42884] 단속카메라"
date:   2025-03-28 19:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-03-26-sql(2).md
  - study/_posts/coding-test/2025-03-26-sql(3).md
  - study/_posts/coding-test/2025-03-26-sql(4).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 42884](https://school.programmers.co.kr/learn/courses/30/lessons/42884)

## ☑️ 풀이

```java
import java.util.*;

class Solution {
    public int solution(int[][] routes) {
        // 진출 시점 기준 배열 오름차순 정렬
        Arrays.sort(routes, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[1] - o2[1];
            }
        });
        
        int cnt = 1;
        int currentIdx = routes[0][1];
        for (int i  = 1; i < routes.length; i++) {
            if (currentIdx < routes[i][0]) { // 카메라 설치 지점 < 진입 지점
                cnt++;
                currentIdx = routes[i][1]; // 카메라 설치 지점 = 진출 지점
            }
        }
        
        return cnt;
    }
}
```

- 진출 시점 기준으로 겹치는 최대 구간의 끝지점에 카메라를 설치하여 최소 카메라 개수를 구한다.
- 먼저 진출 시점 기준으로 routes 배열을 오름차순한다.
- 카메라 설치 로직
    - 첫 차량의 진출 시점(`routes[0][1]`)에 카메라를 설치한다. → **currentIdx**
        - 반복문을 돌면서 만약 차량들의 진입 지점(`routes[i][0]`)이 **currentIdx**보다 뒤에 있다면 새 카메라를 설치한다.
        - **currentIdx**는 다시 해당 차량의 진출 시점으로 변경한다.

## ☑️ 문법 정리

### 2차원 배열 정렬

- **형식**
    
    ```java
    Arrays.sort(배열이름, new Comparator<int[]>() {
        @Override
        public int compare(int[] a, int[] b) {
            return a[기준인덱스] - b[기준인덱스];
        }
    });
    ```
    
    - **Arrays.sort()** 메서드와 **Comparator** 인터페이스를 조합하여 사용한다.
    - 특정 인덱스를 기준으로 정렬할 수 있다.
    - **내림차순** 정렬 시 **`b[기준인덱스] - a[기준인덱스]`** 처럼 순서를 바꾸면 된다.
- Java8 이상의 **람다식** 활용 가능
    
    ```java
    Arrays.sort(배열이름, (a, b) -> Integer.compare(a[기준인덱스], b[기준인덱스]));
    ```
    
    - 정수 오버플로우의 위험을 피하기 위해 `Integer.compare(a[0], b[0])` 사용을 권장한다.