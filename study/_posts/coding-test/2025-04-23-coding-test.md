---
layout: post
title:  "[프로그래머스/68644] 두 개 뽑아서 더하기"
date:   2025-04-23 19:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-04-sql.md
  - study/_posts/coding-test/2025-04-04-sql(2).md
  - study/_posts/coding-test/2025-04-04-sql(3).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 68644](https://school.programmers.co.kr/learn/courses/30/lessons/68644)

## ☑️ 풀이

### 과정

1. 두 수를 선택하는 모든 경우의 수
2. 두 수를 더해서 새로운 배열에 저장 후 중복값 제거
3. 오름차순 정렬 후 리턴

### 첫 번째 풀이 (HashSet)

```java
import java.util.*;

class Solution {
    public int[] solution(int[] numbers) {
  
        HashSet<Integer> set = new HashSet<>();
        
        for (int i = 0; i < numbers.length -1; i++) {
            for (int j = i + 1; j < numbers.length; j++) {
                set.add(numbers[i] + numbers[j]);
            }
        }
        
        int[] arr = new int[set.size()];
        
        Iterator<Integer> iter = set.iterator();
        int idx = 0;
        while (iter.hasNext()) {
            arr[idx++] = iter.next();
        }
        
        Arrays.sort(arr);
        
        return arr;
    }
}
```

1. 먼저 **HashSet**을 사용해서 이중 for문으로 두 수를 더한 합의 중복을 제외한다. 
2. **Iterator**로 HashSet의 모든 원소를 배열에 추가한 후, 오름차순 정렬을 했다. 
    - **Iterator**를 쓰지 않고 **for문**을 사용할 수 있다.
        - 여기서 **Set**의 **Integer** 타입 원소를 **int** 타입의 배열에 넣을 수 있는 건 자바의 **auto-unboxing(오토 언박싱)** 때문!
        
        ```java
        int[] arr = new int[set.size()];
        int idx = 0; 
                
        for (Integer i : set) {
            arr[idx++] = i;
        }
                
        Arrays.sort(arr);
        
        return arr;
        ```
        
    - **Stream**을 사용해서 **HashSet**을 오름차순 정렬 후 배열 형태로 리턴할 수도 있다.
        
        ```java
        return set.stream().sorted().mapToInt(Integer::intValue).toArray();
        ```