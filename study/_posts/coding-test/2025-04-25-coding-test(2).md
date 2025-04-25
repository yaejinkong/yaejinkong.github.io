---
layout: post
title:  "[프로그래머스/131127] 할인 행사"
date:   2025-04-25 15:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-24-coding-test.md
  - study/_posts/coding-test/2025-04-24-coding-test(2).md
  - study/_posts/coding-test/2025-04-25-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 131127](https://school.programmers.co.kr/learn/courses/30/lessons/131127)

## ☑️ 풀이

```java
import java.util.*;

class Solution {
    public int solution(String[] want, int[] number, String[] discount) {
        int answer = 0;
        
        // 원하는 제품을 담은 HashMap        
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < want.length; i++) {
            map.put(want[i], number[i]);
        }
        
        // 10일동안 판매하는 제품을 담은 HashMap
        for (int i = 0; i < discount.length - 9; i++) {
            Map<String, Integer> dMap = new HashMap<>();
            
            for (int j = i; j < i + 10; j++) {
                dMap.put(discount[j], dMap.getOrDefault(discount[j], 0) + 1);
            }
            
            if (map.equals(dMap)) {
                answer++; 
            }
        }
        
        return answer;
    }
}
```

- **want**, **number** 배열을 사용하여 원하는 제품과 제품 수를 담은 **HashMap**을 먼저 생성한다.
- 회원가입 할 **i일**로부터 **10일**간 판매할 제품을 담을 **HashMap**을 for문을 돌면서 계속해서 생성한다.
- 두 **HashMap**을 **`equals()`** 메서드로 일치하는 지 비교한다.
    - **`equals()`** 메서드는 해시맵 내의 모든 `키-값` 쌍이 같으면 **true**를 반환한다. → 이 부분을 놓쳐서 처음 접근했을 때 로직을 복잡하게 생각했었다..

### 시간 복잡도

- 회원가입 바깥 루프 → **O(N)**
- 10일간 판매할 제품을 담는 **HashMap** 생성 → **O(1)**
- 최종 시간 복잡도 → **O(N)**