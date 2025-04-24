---
layout: post
title:  "[프로그래머스/42889] 실패율"
date:   2025-04-24 13:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-04-sql(3).md
  - study/_posts/coding-test/2025-04-23-coding-test.md
  - study/_posts/coding-test/2025-04-23-coding-test(2).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 42889](https://school.programmers.co.kr/learn/courses/30/lessons/42889)

## ☑️ 풀이

### 과정

1. 오름차순 정렬
2. 실패율 구하기
    1. 스테이지에 도달한 플레이어 수 : **`전체 - 누적 실패 count`**
    2. 도달했으나 클리어하지 못한 플레이어 수 : **`stages[i] = N`**인 개수
3. 실패율 내림차순 정렬

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    public int[] solution(int N, int[] stages) {
        Map<Integer, Double> map = new HashMap<>();

        int failNum = 0; // 누적 실패 count
        int total = stages.length;
        int idx = 0;
    
        Arrays.sort(stages);
        
        for (int i = 0; i < N; i++) {
            // 스테이지에 도달한 플레이어 수
            int a = total - failNum;
            
            // 도달했으나 클리어하지 못한 플레이어 수
            int b = 0;
            for (int j = idx; j < stages.length; j++) {
                if (stages[j] > i + 1) {
                    idx = j;
                    break;
                }
                if (stages[j] == i + 1) {
                    b += 1;
                }
            }
            
            if (a == 0) {
                map.put(i + 1, 0.0);
            } else {
                map.put(i + 1, (double)b / a);            
            }
            
            failNum += b;   
        }
        
        // 실패율 내림차순으로 스테이지 번호 반환
        return map.entrySet().stream().sorted((o1, o2) -> o1.getValue().equals(o2.getValue()) ? 
            Integer.compare(o1.getKey(), o2.getKey()) : Double.compare(o2.getValue(), o1.getValue())).mapToInt(HashMap.Entry::getKey).toArray();
        
    }
}
```

- 처음에는 **HashMap**에 스테이지별 실패율을 저장할 때 이렇게만 작성했었는데, 일부 케이스가 통과되지 않았다.
    
    ```java
    map.put(i + 1, (double)b / a);
    ```
    
    - **`a == 0`**인 경우 즉, **스테이지에 도달한 플레이어 수가 0이면** 나누기가 발생했을 때 Infinity나 NaN이 저장되어 정렬 시 이상 동작을 한다.
    - 그래서 0으로 나누는 경우를 고려해서 다시 작성해주었다.
- 마지막에 **HashMap**에 저장된 실패율을 내림차순으로 정렬한 뒤, 스테이지 번호를 반환해야 한다.
    - 하지만 **HashMap**은 순서를 보장하지 않고, 정렬 메서드도 없다.
    - **Map.Entry 스트림**을 사용하여 정렬하고 필요한 값을 추출해야 한다.
        
        ```java
        return map.entrySet().stream().sorted((o1, o2) -> o1.getValue().equals(o2.getValue()) ? 
                    Integer.compare(o1.getKey(), o2.getKey()) : Double.compare(o2.getValue(), o1.getValue())).mapToInt(HashMap.Entry::getKey).toArray();
        ```
        
        - **`map.entrySet()`** : **HashMap**의 모든 `(key, value)` 쌍을 **Set**으로 가져온다.
        - **`.stream()`** : Set을 스트림으로 바꾼다.
        - **`.sorted(…)`** : 실패율 내림차순 (value) 정렬, 실패율이 같으면 스테이지 번호 오름차순 (key)
        - **`.mapToInt(HashMap.Entry::getKey)`** : 정렬된 entry에서 key만 추출한다.
    - **Stream**의 **sorted()** 메서드에서 정렬 기준이 동일한 값에 대해서는 직전의 순서가 유지된다.
        - 실패율이 동일하면 따로 스테이지 번호를 오름차순으로 정렬하지 않아도, 자동으로 정렬된다.
        - 그래서 아래와 같이 작성해도 결과는 동일하다.
        
        ```java
        return map.entrySet().stream()
                  .sorted((o1, o2) -> Double.compare(o2.getValue(), o1.getValue()))
                  .mapToInt(HashMap.Entry::getKey).toArray();
        ```
        

### 두 번째 풀이

```java
import java.util.*;

class Solution {
    public int[] solution(int N, int[] stages) {
        Map<Integer, Double> map = new HashMap<>();

        // 스테이지별 도전자 수
        int[] challenger = new int[N + 2];
        for (int s : stages) {
            challenger[s] += 1;
        }
        
        // 실패율 구하기
        int total = stages.length; // 전체 도전자 수
        
        for (int i = 1; i <= N; i++) {
            if (challenger[i] == 0) {
                map.put(i, 0.0);
            } else {
                map.put(i, (double)challenger[i]/total);
                // 다음 스테이지에 도달한 수 = total - 이번 스테이지 도달한 플레이어 수
                total -= challenger[i]; 
            }
        }
        
        
        // 실패율 내림차순으로 인덱스 반환
        return map.entrySet().stream()
            .sorted((o1, o2) ->  
                    Double.compare(o2.getValue(), o1.getValue()))
            .mapToInt(HashMap.Entry::getKey).toArray();
    }
}
```

- 책에 나와있는 풀이를 참고했다.
    - 가장 큰 차이는 오름차순 정렬을 생략하고, **스테이지별 도전자수**를 구해서 **challengers** 배열에 저장하는 것이다.
        - 인덱스는 스테이지 번호를 그대로 사용하기 위해 **N + 2** 크기로 지정한다.
    - 실패율을 구할 때
        - 해당 스테이지의 도전자 수가 0일 때는 실패율을 0.0로 한다.
        - 이외에는 **`(double)challenger[i]/total`** 로 실패율을 구한다.
        - 그리고 전체 도전자 수인 **total**에 해당 스테이지에 도달한 플레이어 수인 **challenge[i]**를 뺀 값으로 갱신한다.

### 시간 복잡도 (M : 사용자 수, N : 스테이지 수)

- 첫 번째 풀이
    - `Arrays.sort(stage)` → **O(M logM)**
    - 실패율 계산 → **O(M + N)**
    - 실패율 정렬 → **O(N logN)**
    - 최종적으로 **O(M logM + N logN)**
- 두 번째 풀이
    - 스테이지별 도전자수 구하기 → **O(M)**
    - 실패율 계산 → **O(N)**
    - 실패율 정렬 → **O(N logN)**
    - **최종적으로 O(M + N logN)**