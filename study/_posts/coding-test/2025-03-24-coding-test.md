---
layout: post
title:  "[프로그래머스/49993] 스킬트리"
date:   2025-03-24 19:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-03-24-sql(5).md
  - study/_posts/coding-test/2025-03-24-sql(6).md
  - study/_posts/coding-test/2025-03-24-sql(7).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 49993](https://school.programmers.co.kr/learn/courses/30/lessons/49993)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    public int solution(String skill, String[] skill_trees) {
        int answer = 0; 
        
        // map에 skill의 인덱스 저장
        HashMap<Character, Integer> map = new HashMap<>();
        int idx = 0;
        for (int i = 0; i < skill.length(); i++) {
            map.put(skill.charAt(i), idx++);
        }
        
        // skill_trees의 각 원소를 인덱스로 변경
        for (String s : skill_trees) {
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < s.length(); i++) {
                if (map.containsKey(s.charAt(i))) {
                    sb.append(map.get(s.charAt(i)));
                }
            }
                        
            // 3. 올바른 순서인지 검사       
            int sIndex = 0;
            boolean checked = true;
            for (int i = 0; i < sb.length(); i++) {
                int n = sb.charAt(i) - '0';
                if (sIndex == n) {
                    sIndex++;
                } else {
                    checked = false;
                    break;
                }
            }
                      
            answer += checked ? 1 : 0;
        }
        
        return answer;
    }
} 
```

- **접근 방식(다른 사람의 풀이를 참고)**
    1. **skill**의 각 자리의 문자에 대한 인덱스를 **Map**에 저장한다. 
    2. 각 **skill_tree** 문자열에서 **skill에 포함된 문자만** 남긴 후, 그 순서를 Map을 통해 인덱스로 변환한다. 
    3. 숫자로 변환된 인덱스를 앞에서부터 skill과 동일한 인덱스로 나오는 지 검증한다.
        - 중간에 순서를 건너뛰면 무효
        - 순서가 올바르면 유효
- **놓친 부분**
    
    ```java
    int n = sb.charAt(i) - '0';
    ```
    
    - 문자형 숫자를 실제 정수로 변환할 때 사용한다.
    - **문자형 숫자를 sb.charAt(i)**한 값은 **ASCII** 값으로 나오기때문에 **‘0’(48)**을 빼줘야 한다.

### 두 번째 풀이

```java
class Solution {
    public int solution(String skill, String[] skill_trees) {
        int answer = 0; 
        
        for (String s : skill_trees) {
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < s.length(); i++) {
                char c = s.charAt(i);
                if (skill.indexOf(c) != -1) {
                    sb.append(c);
                }
            }
            
            answer += skill.startsWith(sb.toString()) ? 1 : 0;
        }
        
        return answer;
    }
}
```

- Map을 쓰지 않고 문자 그대로 비교하며 풀 수 있다.
    1. 각 **skill_tree**의 문자열에서 **skill에 포함된 문자**만 추출한다.
        - **`String.indexOf()`** 를 사용하면 skill에 포함된 문자인지 아닌 지 확인할 수 있다.
    2. **skill**이 추출된 문자로 시작하면 유효 처리한다.
        - **`String.startsWith()`** 메서드를 사용하여 정확히 일치하는 지 확인할 수 있다.
- 복잡하게 인덱스 및 타입 변환을 할 필요 없이 직관적으로 비교 가능하다.

## ☑️ 문법 정리

### String.indexOf(char)

```java
int idx = "CBD".indexOf('B'); // 1
int idx = "CBD".indexOf('X'); // -1
```

- 주어진 문자가 문자열에 존재하는 지 확인한다.
- 존재하면 **해당 위치(index)**를 반환하고, 없으면 **-1**를 반환한다.

### String.startsWith(String)

```java
"CDE".startsWith("C"); // true
"CDE".startsWith("CD"); // true
"CDE".startsWith("D"); // false
```

- 어떤 문자열이 주어진 접두어로 시작하는 지 확인한다.