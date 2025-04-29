---
layout: post
title:  "[프로그래머스/92334] 신고 결과 받기"
date:   2025-04-29 21:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-25-sql(3).md
  - study/_posts/coding-test/2025-04-28-coding-test.md
  - study/_posts/coding-test/2025-04-29-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 92334](https://school.programmers.co.kr/learn/courses/30/lessons/92334)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    public int[] solution(String[] id_list, String[] report, int k) {
		    // 1.
        Set<String> set = new HashSet<>();
        for (String s : report) {
            set.add(s);
        }
        
        Map<String, Integer> countMap = new HashMap<>();
        Map<String, ArrayList<String>> reportMap = new HashMap<>();
        
        // 2. 
        Iterator iter = set.iterator();
        while (iter.hasNext()) {
            String[] str = iter.next().toString().split(" ");
            countMap.put(str[1], countMap.getOrDefault(str[1], 0) + 1);
            if (!reportMap.containsKey(str[0])) {
                reportMap.put(str[0], new ArrayList<>());
            }
            reportMap.get(str[0]).add(str[1]);
        }
        
        // 3. 
        List<String> admittedUser = new ArrayList<>();
        for (String s : id_list) {
            if (countMap.getOrDefault(s, 0) >= k) {
                admittedUser.add(s);
            }
        }
        
        // 4. 
        int[] answer = new int[id_list.length];
        for (int i = 0; i < id_list.length; i++) {
            String id = id_list[i];
            if (reportMap.containsKey(id)) {
                List<String> nameList = reportMap.get(id);
                for (int j = 0; j < admittedUser.size(); j++) {
                    String name = admittedUser.get(j);
                    if (nameList.contains(name)) {
                        answer[i] += 1; 
                    }
                }
            }
        }
                
        return answer;
    }
}
```

- 한 개의 HashSet과 두 개의 HashMap을 사용했다.
    - **`Set<String> set`** : report 중복 체크용
    - **`Map<String, Integer> countMap`** : key - 신고를 받은 이용자, value - 신고받은 횟수
    - **`Map<String, ArrayList<String>> reportMap`** : key - 이용자, value - 신고한 이용자의 id 모음 ArrayList
1. **HashSet**을 사용하여 **report**의 중복을 체크한다. (중복 신고는 인정되지 않기 때문)
2. **HashSet**을 순회하면서 **countMap**과 **reportMap**을 구성한다.
3. **countMap**을 순회하면서 신고로 인정될 id를 따로 **admittedUser**에 담는다.
4. 각 이용자별 메일을 받는 횟수를 체크한다.
    1. 먼저 id_list를 순회하면서 **reportMap**에 id가 있다면 신고한 이용자의 리스트를 가져온다.
    2. 신고한 리스트에 **admittedUser**에 들어있는 id와 일치하는 id가 있다면 **answer** 배열의 값을 1씩 증가시킨다.
5. answer 배열을 리턴한다.
- 시간 복잡도
    - HashSet으로 중복 제거 → **O(N)** (N: report 길이)
    - HashMap 구성 → **O(N)**
    - 정지될 id 체크 → **O(M)** (M: id_list 길이)
    - 메일 받는 횟수 체크 → **O(M * A * L)** (A: AdmittedUser 길이, L: nameList 길이)
    - 최종 시간 복잡도 → **O(M * A * L)**

### 두 번째 풀이

```java
import java.util.*;

class Solution {
    public int[] solution(String[] id_list, String[] report, int k) {
        Set<String> set = new HashSet<>();
        for (String s : report) {
            set.add(s);
        }
        
        Map<String, Integer> countMap = new HashMap<>();
        Map<String, HashSet<String>> reportMap = new HashMap<>();
        Iterator iter = set.iterator();
        while (iter.hasNext()) {
            String[] str = iter.next().toString().split(" ");
            countMap.put(str[1], countMap.getOrDefault(str[1], 0) + 1);
            if (!reportMap.containsKey(str[0])) {
                reportMap.put(str[0], new HashSet<String>());
            }
            reportMap.get(str[0]).add(str[1]);
        }
        
        List<String> admittedUser = new ArrayList<>();
        for (String s : id_list) {
            if (countMap.getOrDefault(s, 0) >= k) {
                admittedUser.add(s);
            }
        }
        
        int[] answer = new int[id_list.length];
        for (int i = 0; i < id_list.length; i++) {
            String id = id_list[i];
            if (reportMap.containsKey(id)) {
                HashSet<String> nameSet = reportMap.get(id);
                for (int j = 0; j < admittedUser.size(); j++) {
                    String name = admittedUser.get(j);
                    if (nameSet.contains(name)) {
                        answer[i] += 1; 
                    }
                }
            }
        }
                
        return answer;
    }
}
```

- 첫 번째 풀이에서는 **reportMap**의 **Value**로 **ArrayList**를 사용했고, **`ArrayList.contains()`** 를 사용해 선형 탐색을 했기때문에 시간 복잡도가 컸다.
- **ArrayList**를 대신해서 **HashSet**을 사용하면 **`HashSet.contains()`** 사용 시 시간복잡도가 **O(1)**이 되어 최종 시간복잡도가 **O(N^2)**으로 줄어든다.