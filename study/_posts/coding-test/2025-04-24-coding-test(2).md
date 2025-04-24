---
layout: post
title:  "[프로그래머스/49994] 방문 길이"
date:   2025-04-24 17:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-23-coding-test.md
  - study/_posts/coding-test/2025-04-23-coding-test(2).md
  - study/_posts/coding-test/2025-04-24-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 49994](https://school.programmers.co.kr/learn/courses/30/lessons/49994)

## ☑️ 풀이

```java
import java.util.*;

class Solution {
    public int solution(String dirs) {
        int answer = 0;
        int x = 5;
        int y = 5;
        
        //          U  D  R   L
        int[] dx = {0, 0, -1, 1};
        int[] dy = {1, -1, 0, 0};
        
        Map<String, Integer> directions = new HashMap<>();
        directions.put("U", 0);
        directions.put("D", 1);
        directions.put("R", 2);
        directions.put("L", 3);
        
        Set<String> set = new HashSet<>();
        
        for (int i = 0; i < dirs.length(); i++) {
            int dir = directions.get(String.valueOf(dirs.charAt(i)));
            int nx = x + dx[dir];
            int ny = y + dy[dir];
            
            if (isValid(nx, ny)) {
                set.add(x + " " + y + " " + nx + " " + ny);
                set.add(nx + " " + ny + " " + x + " " + y);
                x = nx;
                y = ny;
            }
        }
        
        return set.size() / 2;
    }
    
    public boolean isValid(int x, int y) {
        return x > -1 && x < 11 && y > -1 && y < 11;
    }
}
```

- `(5,5)` 를 중심으로 설정한 후, **HashMap**에 각 방향별 인덱스를 저장한다.
- dirs 문자열의 문자를 하나씩 반복문으로 돌면서 해당 방향에 맞게 다음 좌표인 `(nx, ny)` 를 구한다.
    - **HashMap**에서 각 방향에 맞는 인덱스를 찾아온다.
    - 해당 인덱스를 dir에 대입하여 다음 좌표인 `(x + dx[dir], y + dy[dir])` 를 구한다.
- **isValid** 메서드를 사용하여 다음 좌표가 범위를 벗어나는 지 확인한다.
    - 벗어나지 않는다면 **HashSet**에 두 가지 문자열을 저장한다.
        
        ```java
        set.add(x + " " + y + " " + nx + " " + ny); // "x y nx ny"
        set.add(nx + " " + ny + " " + x + " " + y); // "nx ny x y"
        ```
        
    - **HashSet**은 중복을 제거하기 때문에 이미 거쳐간 중복된 길은 제거된다.
- 같이 저장된 두 문자열은 동일한 길로 취급되기 때문에 **HashSet**의 크기에서 2를 나눈 값을 리턴해준다.