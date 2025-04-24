---
layout: post
title:  "[프로그래머스/42840] 모의고사"
date:   2025-04-23 20:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-04-sql(2).md
  - study/_posts/coding-test/2025-04-04-sql(3).md
  - study/_posts/coding-test/2025-04-23-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 42840](https://school.programmers.co.kr/learn/courses/30/lessons/42840)

## ☑️ 풀이

### 과정

1. 각 수포자의 정답 패턴을 정의한다.
2. 정답 배열과 비교하여 각 수포자의 점수를 계산한다.
3. 최고 점수를 구하고, 해당 점수를 받은 사람을 리스트에 저장한다.
4. 결과를 배열로 변환하여 리턴한다.

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {
        int[] score = new int [3];
        
        // 수포자들의 찍는 방식을 담은 2차원 배열
        int[][] sheet =  {{1, 2, 3, 4, 5}, 
                          {2, 1, 2, 3, 2, 4, 2, 5}, 
                          {3, 3, 1, 1, 2, 2, 4, 4, 5, 5}};
        int max = 0; 
        
        // 문제를 맞춘 개수를 구하는 과정
        for (int i = 0; i < sheet.length; i++) {
            for (int j = 0; j < answers.length; j++) {
                if (answers[j] == sheet[i][j % sheet[i].length]) {
                    score[i] += 1; 
                }
            }
            
            // 가장 높은 점수 구하기
            if (max < score[i]) {
                max = score[i];
            }
        }
        
        // 가장 높은 점수와 동일한 점수가 있다면 리스트에 수포자 번호 추가
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            if (score[i] == max) {
                list.add(i + 1);
            }
        }
        
        // 리스트를 배열로 변경
        int[] answer = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            answer[i] = list.get(i);
        }
        
        return answer;
    }
}
```

- 나머지 연산을 통해 수포자들의 점수를 비교했다.
    
    ```java
    if (answers[j] == sheet[i][j % sheet[i].length])
    ```
    
- 최고 점수를 구할 때는 **Math.max**를 사용하면 더 간편할 거 같다.
    
    ```java
    max = Math.max(max, score[i]);
    ```
    

### 두 번째 풀이 (Stream 사용)

```java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {
        int[] score = new int [3];
        
        // 수포자들의 찍는 방식을 담은 2차원 배열
        int[][] sheet =  {{1, 2, 3, 4, 5}, 
                          {2, 1, 2, 3, 2, 4, 2, 5}, 
                          {3, 3, 1, 1, 2, 2, 4, 4, 5, 5}};
        
        // 문제를 맞춘 개수를 구하는 과정
        for (int i = 0; i < sheet.length; i++) {
            for (int j = 0; j < answers.length; j++) {
                if (answers[j] == sheet[i][j % sheet[i].length]) {
                    score[i] += 1; 
                }
            }
        }
        
        int max = Arrays.stream(score).max().getAsInt();
        
        // 가장 높은 점수와 동일한 점수가 있다면 리스트에 인덱스 추가
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            if (score[i] == max) {
                list.add(i + 1);
            }
        }
        
        // 리스트를 배열로 변경
        return list.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

- 가장 높은 점수는 **Stream**을 사용해서 쉽게 구할 수 있다.
    
    ```java
    int max = Arrays.stream(score).max().getAsInt();
    ```
    
- 수포자의 번호를 담은 리스트를 배열로 변경할 때도 **Stream**을 사용하면 된다.
    
    ```java
    return list.stream().mapToInt(Integer::intValue).toArray();
    ```
    

### 시간 복잡도

- sheet를 돌면서 순회할 때 수포자의 수는 항상 3이므로 항상 **O(1)**
- 내부 반복문에서 정답과 패턴을 비교할 때는 **answers.length**만큼 연산하기 때문에 **O(N)**
- 최종 시간 복잡도는 **O(N)**