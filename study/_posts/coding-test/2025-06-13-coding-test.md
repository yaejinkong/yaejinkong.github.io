---
layout: post
title:  "[알고리즘] 백트래킹2"
date:   2025-06-13 11:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-06-02-coding-test.md
  - study/_posts/coding-test/2025-06-05-coding-test.md
  - study/_posts/coding-test/2025-06-06-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

9 * 9 스도쿠 보드를 다 채워 완성된 스도쿠 보드를 반환하는 solution() 함수를 작성하시오. 해는 유일하지 않을 수 있습니다. 스도쿠의 조건에 맞다면 맞는 해라고 생각하시면 됩니다. 스도쿠의 규칙은 아래와 같습니다.

1. 가로줄, 세로줄에는 1부터 9까지의 숫자가 한 번씩 나타나야 합니다.
2. 9 * 9 보드를 채울 9개의 작은 박스(3 * 3 크기)에도 1부터 9까지의 숫자가 한 번씩 나타나야 합니다.

**제약조건**

문제에 주어지는 board 중 스도쿠를 완성하지 못하는 board는 없다고 가정합니다. 예를 들어 특정 행이나 열에 같은 숫자가 있는 경우는 없습니다.

## ☑️ 풀이

{% raw %}
```java
import java.util.*;
public class Solution44 {

    private static int[][] Board;
    public static void main(String[] args) {
        int[][] board1 = {
                {5, 3, 0, 0, 7, 0, 0, 0, 0},
                {6, 0, 0, 1, 9, 5, 0, 0, 0},
                {0, 9, 8, 0, 0, 0, 0, 6, 0},
                {8, 0, 0, 0, 6, 0, 0, 0, 3},
                {4, 0, 0, 8, 0, 3, 0, 0, 1},
                {7, 0, 0, 0, 2, 0, 0, 0, 6},
                {0, 6, 0, 0, 0, 0, 2, 8, 0},
                {0, 0, 0, 4, 1, 9, 0, 0, 5},
                {0, 0, 0, 0, 8, 0, 0, 7, 9}
        };
        System.out.println(Arrays.deepToString(solution(board1)));

        int[][] board2 = {
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0, 0, 0}
        };

        System.out.println(Arrays.deepToString(solution(board2)));
    }

    public static class Block {
        int x;
        int y;

        public Block(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }

		// 1. 
    private static boolean isValid(int num, int row, int col) {
        return !inRow(num, row) && !inCol(num, col) && !inBox(num, row, col);
    }

    // 해당 행에 num이 있는지 확인
    private static boolean inRow(int num, int row) {
        for (int i = 0; i < 9; i++) {
            if (Board[row][i] == num) {
                return true;
            }
        }
        return false;
    }

    // 해당 열에 num이 있는지 확인
    private static boolean inCol(int num, int col) {
        for (int i = 0; i < 9; i++) {
            if (Board[i][col] == num) {
                return true;
            }
        }
        return false;
    }

    // 3 * 3 박스 안에 numd이 있는 지 확인
    private static boolean inBox(int num, int row, int col) {
        int rowStart = (row / 3) * 3;
        int colStart = (col / 3) * 3;

        for (int i = rowStart; i < rowStart + 3; i++) {
            for (int j = colStart; j < colStart + 3; j++) {
                if (Board[i][j] == num) {
                    return true;
                }
            }
        }
        return false;
    }
    
    // 2.
    private static Block findEmptyPosition() {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (Board[i][j] == 0) {
                    return new Block(i, j);
                }
            }
        }
        return null;
    }

		// 3.
    private static boolean findSolution() {
        Block now = findEmptyPosition(); // 4.
				
        if (now == null) {
            return true;
        }
        
        int x = now.x;
        int y = now.y;

				// 5.
        for (int i = 1; i <= 9; i++) {
            if (isValid(i, x, y)) {
                Board[x][y] = i; 

                if (findSolution()) {
                    return true;
                }
							
                Board[x][y] = 0;
            }
        }
        return false;
    }

    private static int[][] solution(int[][] board) {
        Board = board;
        findSolution();
        return board;
    }}

```
{% endraw %}

- 아래 조건을 만족하면 백트래킹한다.
    1. 해당 행에 넣으려는 숫자 num이 있는지 확인한다.
    2. 해당 열에 넣으려는 숫자 num이 있는지 확인한다.
    3. 해당 위치를 포함하는 3 * 3 박스에 num이 있는 지 확인한다.
- **풀이과정**
    1. `isValid()` : 해당 위치(row, col)에 값(num)을 넣을 수 있는 지 확인한다. 
        1. inRow() : 같은 행에 값(num)이 있는 지 확인한다.
        2. inCol() : 같은 열에 값(num)이 있는 지 확인한다.
        3. inBox() : 현재 위치가 있는 3 * 3 박스에 값(num)이 있는 지 확인한다.
    2. `findEmptyPosition()`  : 현재 스도쿠 보드에서 빈 칸(값이 0)이 있으면 해당 위치를 반환하고, 없다면 null을 반환한다.
    3. `findSolution()` : 스도쿠를 푸는 메인 메서드
    4. `findEmptyPosition()` 을 사용하여 빈 위치(now)를 가져온다.
        1. now가 null이면 스도쿠 보드가 다 채워진 것 → 더이상 탐색을 하지 않기 위해 true를 반환한다.
    5. 해당 빈 위치(now)에 숫자를 1~9까지 넣어보면서 isValid()로 체크한다. 
        1. 만약 가능하다면 해당 스도쿠 보드 칸에 값을 넣고,
        2. `findSolution()`를 호출하여 다음 빈칸을 체크한다. 
            1. `findSolution()`가 true를 반환하면 즉, 스도쿠 보드에 빈칸이 없다면 더 탐색하지 않는다.
            2. `findSolution()`이 false를 반환하면 지금 넣은 값이 틀린 거니까, 해당 칸에 0을 넣는다.
        3. 반복문을 다 돌았는데도 해를 찾지 못하면 false를 반환한다. 
- **시간 복잡도**
    - N은 스도쿠에서 빈칸의 개수
    - 빈칸 당 1~9의 수가 들어감 → **O(9 ^ N)**
    - 실제 연산은 유망함수에 의해 훨씬 적다.