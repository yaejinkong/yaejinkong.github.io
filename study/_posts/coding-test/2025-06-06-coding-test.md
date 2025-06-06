---
layout: post
title:  "[알고리즘] 백트래킹"
date:   2025-06-06 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-05-31-coding-test.md
  - study/_posts/coding-test/2025-06-02-coding-test.md
  - study/_posts/coding-test/2025-06-05-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

정수 N을 입력받아 1부터 N까지의 숫자 중에서 합이 10이 되는 조합을 리스트롤 반환하는 solution() 함수를 작성하라.

- 백트래킹을 활용해야 한다.
- 숫자 조합은 오름차순으로 정렬되어야 하낟.
- 같은 숫자는 한 번만 선택할 수 있다.
- N은 1 이상 10 이하인 정수이다.

## ☑️ 풀이

### 풀이

```java
import java.util.ArrayList;

public class Solution43 {

    public static void main(String[] args) {
        System.out.println(solution(5));
        System.out.println(solution(2));
        System.out.println(solution(7));
    }

    private static ArrayList<ArrayList<Integer>> result; 
    private static int n;
    
    private static void backtrack(int sum, ArrayList<Integer> selectedNums, int start) {
        // 3. 
        if (sum == 10) {
            result.add(selectedNums);
            return;
        }
        
				// 4.
        for (int i = start; i <= n; i++) {
            if (sum + i <= 10) {
                ArrayList<Integer> list = new ArrayList<>(selectedNums);
                list.add(i);
                backtrack(sum + i, list, i + 1);
            }
        }
    }

    private static ArrayList<ArrayList<Integer>> solution(int N) {
        result = new ArrayList<>(); // 1. 
        n = N;
        backtrack(0, new ArrayList<>(), 1); // 2.

        return result;
    }

}
```

### 출력

```
[[1, 2, 3, 4], [1, 4, 5], [2, 3, 5]]
[]
[[1, 2, 3, 4], [1, 2, 7], [1, 3, 6], [1, 4, 5], [2, 3, 5], [3, 7], [4, 6]]
```

- 유망함수 조건 (**유망함수** → 해가 될 가능성을 판단)
    - 조합한 숫자의 합이 10이 되면 해당 조합을 결과 리스트에 추가
    - 조합한 숫자의 합이 10보다 크면 백트래킹
- 풀이 과정
    1. 조합한 숫자 리스트를 담을 answer 리스트를 선언한다.
    2. 백트래킹 메서드(backtrack())를 호출한다.
        1. sum : 현재까지의 숫자의 합, selectedNums : 조합한 숫자 리스트, start : 조합에 포함 여부를 확인할 숫자
    3. 현재까지의 조합의 합(sum)이 10이 되면 해당 조합 리스트(selectedNums)를 결과 리스트에 추가한다.
    4. 현재 숫자(start)부터 N까지 범위에 반복문을 수행한다.
        1. backtrack() 메서드는 1부터 N까지 차례대로 조합을 하면서 특정값 K가 되는 부분합을 찾기 때문에 현재 숫자(start) 이전의 숫자는 체크할 필요가 없다. 
        2. 만약 현재까지의 숫자의 합(sum)이 10보다 작거나 같을 경우, 
        3. 해당 숫자를 포함한 새로운 조합 리스트를 생성하여 다시 백트래킹 메서드(backtrack())를 재귀적으로 호출한다.
- 시간복잡도
    - N : 선택할 수 있는 숫자의 개수
    - 비복원 방식으로 조합을 구하고 있음
        - 처음 선택지는 N개, 그 다음 선택지는 (N - 1)개 … → **O(N!)**