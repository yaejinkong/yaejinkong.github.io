---
layout: post
title:  "[프로그래머스/42579] 베스트앨범"
date:   2025-04-29 20:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-25-sql(2).md
  - study/_posts/coding-test/2025-04-25-sql(3).md
  - study/_posts/coding-test/2025-04-28-coding-test.md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 42579](https://school.programmers.co.kr/learn/courses/30/lessons/42579)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    private class Music {
        public int idx; 
        public int plays;
        
        public Music(int idx, int plays) {
            this.idx = idx;
            this.plays = plays;
        }
    }
    
    public int[] solution(String[] genres, int[] plays) {        
        // key : 장르, value : 재생 횟수의 합
        Map<String, Integer> sumMap = new HashMap<>();
        // key : 장르, value : list<(idx, plays)>
        Map<String, ArrayList<Music>> genreMap = new HashMap<>();
        
        // 1. 
        for (int i = 0; i < genres.length; i++) {
            if (!genreMap.containsKey(genres[i])) {
                genreMap.put(genres[i], new ArrayList<>());
            } 
            sumMap.put(genres[i], sumMap.getOrDefault(genres[i], 0) + plays[i]);
            genreMap.get(genres[i]).add(new Music(i, plays[i]));
        }
        
        // 2. 
        String[] arr = sumMap.entrySet().stream().sorted((o1, o2) -> Integer.compare(o2.getValue(), o1.getValue())).map(HashMap.Entry::getKey).toArray(String[]::new);

        // 3.        
        List<Integer> answerList = new ArrayList<>();        
        for (int i = 0; i < arr.length; i++) {
            List<Music> list = genreMap.get(arr[i]);
            list.sort((a, b) -> {
                if (b.plays != a.plays) {
                    return Integer.compare(b.plays, a.plays);
                } else {
                    return Integer.compare(a.idx, b.idx);
                }
            });
            
            // 4. 
            if (list.size() != 1) {
                for (int j = 0; j < 2; j++) {
                    answerList.add(list.get(j).idx);
                }
            } else {
                answerList.add(list.get(0).idx);
            }
        }
        
        // 5. 
        return answerList.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

- 책을 참고한 풀이로, 두 가지 **HashMap**을 사용하여 풀었다.
    - **Map<String, Integer> sumMap** : 각 장르(key)별 음악의 총 재생횟수(value)를 담고 있다.
    - **Map<String, ArrayList<Music>> genreMap** : 각 장르(key)별 Music 객체의 List(Value)를 담고 있다.
        - Music 객체는 인덱스와 재생횟수로 이루어져 있다.
1. **genre[]** 을 돌면서 **Hashmap**을 구성한다.
    - **genreMap**에 해당 장르가 없다면 **genreMap**에 **`(genres[i], new ArrayList<>())`** 를 추가한다.
        - 바로 **Music** 객체를 생성해서 추가하면 **`NullPointException`**이 발생하기 때문에 먼저 빈 **ArrayList**로 초기화해준다.
    - 이후 **Music** 객체를 생성해서 **genreMap**에 추가해준다.
    - **sumMap**에는 장르별 누적 재생 횟수를 담는다.
2. **Stream**을 사용하여 **sumMap**의 **value** 값인 총 재생 횟수가 큰 순서로 정렬하여 배열을 생성한다.  
3. **genreMap**을 돌면서 **value** 값인 **ArrayList**를 꺼내온다.
    - **ArrayList**의 원소인 Music 객체에서 먼저 재생횟수별 내림차순 정렬 후, 재생횟수가 동일하다면 인덱스별 오름차순으로 정렬한다.
    - 여기서 **`sorted()`**를 사용했기 때문에 순서가 있는 스트림을 정렬할 때, 기존 순서가 유지되는 stable sort로 정렬이 된다.
    - 즉, 인덱스가 낮은 순서대로 데이터를 삽입했기 때문에 **ArrayList**에서 **Music**의 재생횟수가 같다면 따로 인덱스를 오름차순 정렬하지 않아도 동일한 결과를 반환한다.
    - 그래서 다음과 같이 코드를 작성해도 무방하다.
        
        ```java
        list.sort((a, b) -> Integer.compare(b.plays, a.plays));
        ```
        
4. **ArrayList**의 크기에 따라 정답 **ArrayList**에 인덱스를 추가한다.
    1. 크기가 1이 아니라면 해당 장르의 음악을 2개만 추가한다.
    2. 크기가 1이라면 해당 장르의 음악을 1개 추가한다.
5. 정답 **ArrayList**를 배열로 변경하여 리턴한다.
- 시간 복잡도
    - HashMap 저장 → **O(N)**
    - 장르별 재생횟수 내림차순 정렬 → **O(GlogG) (G : 장르 개수, 100개 미만 → 상수로 취급)**
    - 재생횟수 별 내림차순 정렬 → **O(NlogN)**
    - 최종 시간복잡도 → **O(NlogN)**