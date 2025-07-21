---
layout: post
title:  "[프로그래머스/92342] 양궁대회"
date:   2025-07-21 11:20:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-06-13-coding-test.md
  - study/_posts/coding-test/2025-06-16-coding-test.md
  - study/_posts/coding-test/2025-07-08-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 92342](https://school.programmers.co.kr/learn/courses/30/lessons/92342)

## ☑️ 풀이

### 문제 분석

문제를 제대로 분석하지 않고 풀었더니 끊임없이 난관에 봉착했다..

문제를 꼼꼼하게 읽고 분석하자…!

**문제** 

- 주어진 `info[]`는 10점부터 0점까지의 순서로 과녁 점수의 개수를 담은 정수 배열이다.
- 라이언이 가장 큰 점수 차로 우승하려면 n발의 화살을 어떻게 맞춰야 할지를 배열로 반환하는 문제이다.

**입출력 분석**

| n | info | result |
| --- | --- | --- |
| 5 | [2,1,1,1,0,0,0,0,0,0,0] | [0,2,2,0,1,0,0,0,0,0,0] |
| 1 | [1,0,0,0,0,0,0,0,0,0,0] | [-1] |
| 9 | [0,0,1,2,0,1,1,1,1,1,1] | [1,1,2,0,1,2,2,0,0,0,0] |
| 10 | [0,0,0,0,0,0,0,0,3,4,3] | [1,1,1,1,1,1,1,1,0,0,2] |

 

- n = 5일 경우
    
    <img width="350" alt="image" src="/assets/img/study/image3.jpeg">
    

### 작성한 코드

```java
import java.util.*;

class Solution {
    private static int[] apeach;
    private static int[] answer;
    private static int max;
    
    public int[] solution(int n, int[] info) {
        apeach = info;
        max = 0;
        backtrack(n, 0, new int[11]);
        
        return max == 0 ? new int[]{-1} : answer;
    }
    
    // 1. 
    public void backtrack(int n, int idx, int[] ryan) {
		// 1-1.
        if (n == 0) { // 남은 화살이 없을 때
            calculateDiff(ryan);
            return;
        }
        
        // 1-2.
        for (int i = idx; i <= 10; i++) {
            int cnt = Math.min(n, apeach[i] + 1);
            ryan[i] = cnt;
            backtrack(n - cnt, i + 1, ryan);
            ryan[i] = 0;
        }
    }
    
    // 2. 
    private static void calculateDiff(int[] ryan) {
        int score = 0;
        // 2-1.
        for (int i = 0; i <= 10; i++) {
            if (ryan[i] > 0 || apeach[i] > 0) {
                score += ryan[i] > apeach[i] ? 10 - i : -(10 - i);
            } 
        }
        
        // 2-2.
        if (score > max) {
            max = score;
            answer = ryan.clone();
        } 
        
        // 2-3.
        else if (max > 0 && score == max) {
            // 낮은 점수를 맞힌 개수가 큰 경우 찾기
            for (int i = 10; i >= 0; i--) {
                if (answer[i] != ryan[i]) {
                    if (answer[i] < ryan[i]) {
                        answer = ryan.clone();
                    }
                    break;
                }
            }
        }
    }
}
```

1. 백트래킹을 통해 라이언의 과녁 점수 조합을 모두 확인한다.
    - **n** : 남은 화살의 수
    **idx** : 현재까지 체크한 과녁의 배열 인덱스
    **ryan** : 라이언의 과녁 점수 정보
    
    1-1. **`n=0`** 즉, 남은 화살의 수가 **0개**이면 **`calculateDiff`** 메서드를 사용하여 점수차를 계산한다.
    
    1-2. **`n≠0`** 인 경우, 
    
    - idx부터 시작해서 10까지 순차적으로 과녁 점수를 선택한다.
    - 해당 점수에서 어피치를 이기기 위해 필요한 화살 개수는 **`apeach[i] + 1`**개이다.
    - 단, 남은 화살 수는 n보다 클 수 없기 때문에 **`Math.min()`**를 사용한다.
    - 선택한 화살 수(**cnt**)만큼 **`ryan[i]`**에 저장하고, 재귀호출을 통해 다음 점수 구간(**i + 1**)으로 넘어간다.
    - 백트래킹이므로, 재귀호출 이후에 **`ryan[i] = 0`**으로 초기화해 다른 경우의 수를 탐색한다.
2. **`calculateDiff`** : 어피치와 라이언의 과녁 점수 차이를 ‘라이언 기준’으로 구한 후, 해당 점수 차이가 최대가 되는 값을 구한다.
    
    2-1. 라이언이 점수를 얻을 수 있는 경우는 어피치보다 과녁 수의 합이 큰 경우밖에 없다.
    
    - `ryan[i] > apeach[i]` 면, `10-i` 점을 더한다.
    - `ryan[i] < apeach[i]` 면, `-(10-i)` 점을 더한다. (점수 차이기 때문)
    - 0점부터 10점까지 모두 확인한 후 점수 차이를 더한 **score** 값을 구한다.
    
    2-2. **score** 값이 직전까지 구해놨던 점수인 **max**보다 큰 경우
    
    - **max**를 **score**로 갱신한다.
    - **answer** 배열을 **ryan** 배열로 **`clone()`** 메서드를 사용하여 복사해준다.
    
    2-3. **score** 값이 **max**와 동일한 경우
    
    - 문제에서 점수 차이가 같은 경우가 많다면, 가장 낮은 점수를 더 많이 맞힌 경우를 반환해준다고 적혀있다.
    - 가장 낮은 점수 인덱스인 10부터 0까지 돌면서 기존 **answer**배열과 **ryan**배열을 비교한다.
    - **`answer[i]`**과 **`ryan[i]`**가 다른 경우를 우선적으로 골라내서, 만약 현재 **`ryan[i]`**가 더 큰 경우, **answer**을 **ryan** 배열로 **`clone()`** 메서드를 사용해서 복사해준다.

- 시간 복잡도
    - 0~10점 각 과녁에 화살을 맞춘다/못맞춘다 두 상태가 있다.
    - **`O(2^11)`**