---
layout: post
title:  "[프로그래머스/72411] 메뉴 리뉴얼"
date:   2025-04-30 21:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-28-coding-test.md
  - study/_posts/coding-test/2025-04-29-coding-test.md
  - study/_posts/coding-test/2025-04-29-coding-test(2).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 72411](https://school.programmers.co.kr/learn/courses/30/lessons/72411)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    private static Map<Integer, HashMap<String, Integer>> courseMap = new HashMap<>();
    
    public String[] solution(String[] orders, int[] course) {
        
        // 1. 
        for (int c : course) {
            courseMap.put(c, new HashMap<String, Integer>());
        }
        
        // 2. 
        for (String order : orders) {
            char[] orderArray = order.toCharArray();
            Arrays.sort(orderArray);
            combinations(0, orderArray, "");
        }
        
        // 3. 
        List<String> answerList = new ArrayList<>();
        for (int c : course) {
            if (courseMap.containsKey(c)) {
                HashMap<String, Integer> countMap = courseMap.get(c);
                
                List<Map.Entry<String, Integer>> list = new ArrayList(countMap.entrySet());
                list.sort((a, b) -> b.getValue() - a.getValue());
                
                int max = list.isEmpty() ? 0 : list.get(0).getValue();
                for (int i = 0; i < list.size(); i++) {
                    if (list.get(i).getValue() < 2 || !list.get(i).getValue().equals(max)) {
                        break;
                    } else {
                        answerList.add(list.get(i).getKey());
                    }
                }
            }
        }
        
        Collections.sort(answerList); // 4. 
        return answerList.toArray(String[]::new); // 5.
    }
    
    public static void combinations(int idx, char[] order, String result) {
        if (courseMap.containsKey(result.length())) {
            HashMap<String, Integer> map = courseMap.get(result.length());
            map.put(result, map.getOrDefault(result, 0) + 1);   
        }
        
        for (int i = idx; i < order.length; i++) {
            combinations(i + 1, order, result + order[i]);
        }
    }
}
```

- 책에 있는 풀이를 참고했다.
- **`Map<Integer, HashMap<String, Integer>> courseMap`** 을 전역 변수로 선언해서 사용한다.
    - **key** : course(ex. 2, 3, 4), **value** : HashMap<String, Integer>
    - HashMap은 **key** : 코스메뉴, **value** : 개수
1. **courseMap**을 구성하기 전 먼저 초기화를 해준다.
2. **orders[]**를 순회하면서 **courseMap**을 완성한다.
    1. 각 손님의 주문 메뉴를 **`toCharArray()`** 메서드를 이용하여 단품 메뉴로 분리한 배열인 **orderArray**를 생성한다.
    2. **orderArray**를 오름차순 정렬한다.
    3. **combinations** 메서드로 **orderArray**를 순회하며 메뉴를 조합한다.
        - 조합은 재귀로 구현했다.
3. **courseMap**을 순회하면서 해당 course 별 코스 메뉴(key)의 개수(value)를 **내림차순** 정렬한 **ArrayList**를 생성한다. 
    1. 가장 빈도수가 높은 코스 메뉴를 하나를 뽑아서 **answerList**에 추가한다.
    2. 빈도수가 2회 이상이어야 한다.
    3. 이 때, 가장 빈도수가 높은 값과 동일한 코스 메뉴가 있다면 해당 코스 메뉴도 **answerList**에 추가한다.
4. 다시 **answerList**를 **오름차순** 정렬한다.
5. **answerList**를 **`String[]`**으로 바꿔 반환한다.

## ☑️ 문법 정리

### Map.Entry<K, V>

- **`Map<K, V>`**의 한 쌍의 원소를 표현하는 중첩 인터페이스
- Map에 들어있는 데이터를 **`entrySet()`**으로 꺼낼 때 사용한다.
    
    ```java
    Map<String, Integer> map = new HashMap<>();
    map.put("A", 1);
    
    // entrySet()으로 key-value 쌍 전체 순회
    for (Map.Entry<String, Integer> entry : map.entrySet()) {
    		String key = entry.getKey();
    		Integer value = entry.getValue();
    }
    ```
    

**전체 entry 가져오기**

```java
Set<Map.Entry<String, Integer>> entrySet = map.entrySet();
```

**정렬 예시**

```java
List<Map.Entry<String, Integer>> list = new ArrayList<>(map.entrySet());
list.sort((a, b) -> b.getValue().compareTo(a.getValue()));
```