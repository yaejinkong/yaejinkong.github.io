---
layout: post
title:  "[코테] 두 개의 수로 특정값 만들기"
date:   2025-04-25 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-23-coding-test(2).md
  - study/_posts/coding-test/2025-04-24-coding-test.md
  - study/_posts/coding-test/2025-04-24-coding-test(2).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[코딩 테스트 합격자 되기: 자바 편] 문제 18

### 문제

n개의 양의 정수로 이루어진 배열 arr와 정수 target이 주어졌을 때 이 중에서 합이 target인 두 수가 arr에 있는 지 찾고, 있으면 true, 없으면 false를 반환하시오.

### 제약 조건

- n은 2이상 10,000 이하의 자연수
- arr의 각 원소는 1 이상 10,000 이하의 자연수
- arr의 원소 중 중복되는 원소는 없다.
- target은 1 이상 20,000 이하의 자연수

### 입출력 예시

| arr | target | return |
| --- | --- | --- |
| 1 2 3 4 8 | 6 | true |
| 2 3 5 9 | 10 | false |

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;
import java.io.*;

class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] arr = br.readLine().split(" ");
        int target = Integer.parseInt(br.readLine());
        for (int i = 0; i < arr.length; i++) {
            for (int j = i + 1; j < arr.length; j++) {
                if (Integer.parseInt(arr[i]) + Integer.parseInt(arr[j]) == target) {
                    System.out.println("true");
                    return;
                }
            }
        }
        System.out.println("false");
    }
}
```

- 완전탐색으로 두 원소를 더했다.
- 시간 복잡도는 **O(N^2)**으로 N이 10,000이 되면 대략 1억 번의 연산이 수행되어야 해서 효율이 떨어진다.

### 두 번째 풀이

```java
import java.util.*;
import java.io.*;

class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] arr = br.readLine().split(" ");
        int target = Integer.parseInt(br.readLine());
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < arr.length; i++) {
            if (set.contains(target - Integer.parseInt(arr[i]))) {
                System.out.println(true);
                return;
            }
            set.add(Integer.parseInt(arr[i]));
        }
        
        System.out.println(false);
    }
}
```

- **HashSet**을 사용한다.
- **arr**의 각 원소를 **HashSet**에 추가할 때, `target - 해당 원소` 한 값이 **HashSet**에 존재하는 지 확인해보면 된다.
- 시간복잡도
    - 반복문 → **O(N)**
    - HashSet.contains() → **O(1)**
    - HashSet.add() → **O(1)**
    - 최종 시간복잡도는 **O(N)**