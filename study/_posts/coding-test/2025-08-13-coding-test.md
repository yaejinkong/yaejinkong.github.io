---
layout: post
title:  "[프로그래머스/60062] 외벽점검"
date:   2025-08-13 11:20:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-06-16-coding-test.md
  - study/_posts/coding-test/2025-07-08-coding-test.md
  - study/_posts/coding-test/2025-07-21-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 60062](https://school.programmers.co.kr/learn/courses/30/lessons/60062)

## ☑️ 풀이

### 작성한 코드

```java
import java.util.*;

class Solution {
    private static int[] Weak;
    private static boolean[] used;
    private static int length;
    private static int answer;
    
    // 1. dist 배열의 친구들로 모든 외벽이 점검 가능한 지 체크
    private static boolean check(int[] dist) {
        for (int i = 0; i < length; i++) {
            int idx = i;
            for (int d : dist) {
                int position = Weak[idx++] + d;
                while (idx < Weak.length && Weak[idx] <= position) {
                    idx++;
                }
            }
            // 해당 친구들로 모든 외벽이 점검 가능하면 true, 아니면 false
            if (idx - i >= length) {
                return true;
            }
        }
        return false;
    }
    
    // n명의 친구를 뽑아 org 배열로 넘기면 백트래킹으로 n명의 친구를 나열할 수 있는 경우의 수를 모두 구함
    private static void backtrack(int n, int[] dist, int[] org) {
        if (n == org.length) {
            if (check(dist)) {
                answer = n;
                return;
            }
        }
        
        for (int i = 0; i < org.length; i++) {
            if (!used[i]) {
                used[i] = true;
                dist[n] = org[i];
                backtrack(n + 1, dist, org);
                used[i] = false;
            }
        }
    }
    
    public int solution(int n, int[] weak, int[] dist) {
        length = weak.length;
        Weak = new int[length * 2];
        
        for (int i = 0; i < length; i++) {
            Weak[i] = weak[i];
            Weak[i + length] = weak[i] + n;
        }
        
        used = new boolean[dist.length];
        Arrays.sort(dist); 
        answer = -1;
        
        for (int i = 1; i <= dist.length; i++) {
            int[] org = new int[i];
            System.arraycopy(dist, dist.length - i, org, 0, i);
            backtrack(0, new int[i], org);
            if (answer > 0) break;
        }
        return answer;
    }
}
```

### 풀이 과정

1. **check** 메서드 : **`dist`** 배열의 친구들로 모든 외벽이 점검 가능한 지 체크
    1. 반복문을 돌면서 외벽을 0번째부터 끝(length - 1)까지 확인한다.
    2. **`idx`**는 **`Weak`** 배열의 인덱스로 어느 외벽까지 점검했는지 저장한다.
    3. **`dist`**를 돌면서 각 친구마다 최대한 많은 외벽을 점검한다.
        1. 이번 친구가 점검할 수 있는 거리(**`position`**)을 계산하고, 점검할 수 있는 외벽까지 **`idx`**를 증가시킨다.
        2. 다음 친구가 있다면 다음 외벽에서 출발해 동일하게 점검한다.
    4. 모든 친구(**`dist`**)의 외벽 점검이 끝나면, 모든 외벽이 점검되었는지 확인한다.
        1. **`idx - i`**가 전체 외벽 개수(**`length`**)보다 크거나 같으면 true 반환
        2. 점검 못한 외벽이 남아 있다면 false를 반환
2. **backtrack** 메서드 : n명의 친구를 뽑아 org 배열로 넘기면, 백트래킹으로 n명의 친구를 나열할 수 있는 경우의 수를 모두 구함
    1. org 배열의 모든 친구를 나열(**`n == org.length`**)하면 check 메서드를 호출해서 모든 외벽을 점검할 수 있는 지 확인
    2. 한 번 점검을 마친 친구는 다시 점검하지 않기 위해 **`used`** 배열을 활용하여 백트래킹을 수행한다.
        1. i번째 친구를 나열하지 않았다면, **`dist[n]`**에 i번째 친구(**`org[i]`**)를 넣고 **n을 1 증가**시켜 재귀호출한다.
3. **메인 메서드 (solution)**
    1. 주어진 **`weak`** 배열을 확장하여 선형으로 만들어 **`Weak`**에 저장한다. 
    2. 친구의 점검 여부를 확인하기 위한 **`used`** 배열도 초기화해준다. 
    3. **`dist`** 배열을 오름차순 정렬하여 점검범위가 큰 친구부터 점검을 시작한다.
        1. **`dist`** 배열의 뒤부터 **`i`**명을 뽑아서 **`org`** 배열에 담는다. 
        2. 백트래킹을 수행한다.
        3. **answer**가 0보다 크다면 반복문을 종료한다. 

### 시간 복잡도

- N : dist의 길이, M : weak의 길이
- 모든 weak 지점을 순회(**M**)하며 친구들의 순열을 모두 확인(**N!**)
- 현재 투입된 친구가 다음 weak까지 갈 수 있는 지 체크(while문) → **M**
- 최종 시간 복잡도 → **O(M^2 * N!)**

### 후기

- 백트래킹 … 너무 어렵다 ..

## ☑️ 문법 정리

### System.arraycopy()

Java에서 배열 복사를 빠르게 할 수 있도록 제공되는 native 메서드
for문으로 일일이 복사하는 것보다 훨씬 빠르고 효율적임

- 기본 문법
    
    ```java
    System.arraycopy(Object src, int srcPos, Object dest, int destPos, int len);
    ```
    
    - `src` : 원본 배열(복사할 대상)
    - `srcPos` : 원본 배열에서 복사를 시작할 인덱스
    - `dest` : 복사한 값을 넣을 대상 배열
    - `destPos` : 대상 배열에서 복사를 시작할 인덱스
    - `length` : 복사할 요소의 개수

- 예제
    
    ```java
    int[] src = {10, 20, 30, 40, 50};
    int[] dest = new int[5];
    
    // src의 1번 인덱스부터 3개 요소를 dest의 0번 인덱스부터 복사
    System.arraycopy(src, 1, dest, 0, 3);
    System.out.println(Arrays.toString(dest)); 
    // [20, 30, 40, 0, 0]
    ```