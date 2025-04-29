---
layout: post
title:  "[프로그래머스/42888] 오픈채팅방"
date:   2025-04-28 21:53:06 +0900
categories: 
            - coding-test
tags:        
toc: true
related_posts:
  - study/_posts/coding-test/2025-04-25-sql.md
  - study/_posts/coding-test/2025-04-25-sql(2).md
  - study/_posts/coding-test/2025-04-25-sql(3).md
---
* this unordered seed list will be replaced by the toc
{:toc}

## ☑️ 문제

[프로그래머스 42888](https://school.programmers.co.kr/learn/courses/30/lessons/42888)

## ☑️ 풀이

### 첫 번째 풀이

```java
import java.util.*;

class Solution {
    public String[] solution(String[] record) {
        // 1. HashMap에 key=userid, value=nickname 저장
        // 2. list에 출력될 명령 따로 저장 (형식 : 명령어 아이디)
        Map<String, String> map = new HashMap<>();
        List<String> list = new ArrayList<>();
        for (int i = 0; i < record.length; i++) {
            String[] arr = record[i].split(" ");
            if (arr.length == 3) {
                map.put(arr[1], arr[2]);
                if (arr[0].equals("Enter")) {
                    list.add(arr[0] + " " + arr[1]);
                } 
            } else {
                list.add(arr[0] + " " + arr[1]);
            }
        }
        
        // 3. 닉네임을 Hashmap에서 찾고, list에 담긴 명령어에 따라 형식을 구분해 출력
        String[] answer = new String[list.size()];
        for (int i = 0; i < list.size(); i++) {
            String[] listStr = list.get(i).split(" ");
            String nickName = map.get(listStr[1]);
            if (listStr[0].equals("Enter")) {
                answer[i] = nickName + "님이 들어왔습니다.";
            } else {
                answer[i] = nickName + "님이 나갔습니다.";
            }
        }
        
        return answer;
    }
}
```

- **HashMap**에 `key=userid, value=nickname` 형식으로 저장한다.
- 출력되어야 할 명령(Enter, Leave)은 따로 **list**에 저장한다.
    - 형식 : `명령어 아이디`
- 리스트를 돌면서 출력문을 배열에 저장한다.
    - 닉네임은 **Hashmap**에서 찾는다.
    - **list**에 담긴 명령어에 따라 출력 형식을 구분해 저장한다.
- 시간복잡도
    - record 순회 → **O(N)**
    - list 순회 → **O(N)**
    - 총 시간 복잡도 → **O(N)**

### 두 번째 풀이

```java
import java.util.*;

class Solution {
    public String[] solution(String[] record) {
        Map<String, String> map = new HashMap<>();
        for (int i = 0; i < record.length; i++) {
            String[] arr = record[i].split(" ");
            if (arr.length == 3) {
                map.put(arr[1], arr[2]);
            }
        }
        
        Map<String, String> msg = new HashMap<>();
        msg.put("Enter", "님이 들어왔습니다.");
        msg.put("Leave", "님이 나갔습니다.");
        
        List<String> list = new ArrayList<>();
        for (int i = 0; i < record.length; i++) {
            String[] arr = record[i].split(" ");
            if (msg.containsKey(arr[0])) {
                list.add(map.get(arr[1]) + msg.get(arr[0]));
            }
        }
        
        return list.toArray(new String[0]);
    }
}
```

- 책을 참고했다.
- 첫번째 풀이와 다르게 출력할 명령을 담은 새로운 List를 만들지 않는다.
    - **HashMap인 msg**에 `key=Enter/Leave, value=문장형식` 저장한다.
    - **record** 배열을 돌면서 `msg.containsKey()` 를 사용한다. **msg**의 **key**(Enter, Leave)와 일치하는 key가 있다면 `map.get(arr[1]) + msg.get(arr[0])` 로 문장을 조합하여 **list**에 담는다.
- 시간 복잡도
    - record 2번 순회 → **O(N)**

### 세 번째 풀이

```java
import java.util.*;

class Solution {
    private static final String ENTER_MESSAGE = "님이 들어왔습니다.";
    private static final String LEAVE_MESSAGE = "님이 나갔습니다.";
    
    private class User {
        public String userId;
        public String nickname; 
        
        public User(String userId, String nickname) {
            this.userId = userId;
            this.nickname = nickname;
        }
    }
    
    private class Command {
        public char command;
        public String userId;
        
        public Command(char command, String userId) {
            this.command = command;
            this.userId = userId;
        }
    }
    
    private Map<String, User> map = new HashMap<>();
    private List<Command> list = new ArrayList<>();
        
    public String[] solution(String[] record) {
        for (int i = 0; i < record.length; i++) {
            String[] r = record[i].split(" ");
            char cmd = r[0].charAt(0);
            
            switch(r[0].charAt(0)) {
                case 'E' :
                    if (!map.containsKey(r[1])) {
                        map.put(r[1], new User(r[1], r[2]));
                    } else {
                        map.get(r[1]).nickname = r[2];
                    }
                    list.add(new Command(cmd, r[1]));
                    break;
                
                case 'L' :
                    list.add(new Command(cmd, r[1]));
                    break;
                
                case 'C' :
                    map.get(r[1]).nickname = r[2];
                    break;
            }
        }
        
        String[] answer = new String[list.size()];
        int idx = 0;
        
        for (Command cmd : list) {
            String msg = cmd.command == 'E' ? ENTER_MESSAGE : LEAVE_MESSAGE; 
            answer[idx++] = map.get(cmd.userId).nickname + msg; 
        }
        
        return answer;
        
    }
}
```

- 객체 지향적으로 푼 풀이를 보고 다시 풀어보았다.
    - **`User`** 클래스 : userId, nickname을 관리
    - **`Command`** 클래스 : 명령어(Enter, Leave), userId를 관리
    - **`Map<String, User> map`** : userId를 기준으로 **User** 객체 관리
    - **`List<Command> list`** : 출력할 **Command** 객체 관리
- **동작 방식**
    1. record를 순회하면서
    - 명령어가 **Enter**인 경우
        - map에 UserId가 없으면 User를 생성해서 저장한다.
        - map에 UserId가 있으면 nickname만 변경한다.
        - list에 Command를 생성해서 저장한다.
    - 명령어가 **Leave**인 경우
        - list에 Command를 생성해서 저장한다.
    - 명령어가 **Change**인 경우
        - nickname만 변경한다.
    1. list를 순회하면서
        - userId에 해당하는 nickname을 가져온다.
        - Command에 따라 메시지를 만들어 answer에 저장한다.
- 시간 복잡도
    - record 순회 → **O(N)**
    - list 순회 → **O(N)**
    - 최종 시간 복잡도 → **O(N)**

## ☑️ 문법 정리

### List → 배열

1. **toArray() 메서드 사용**
- 타입 없는 기본 **toArray()**
    
    ```java
    Object[] arr = list.toArray();
    ```
    
    - 리턴 타입이 **Object[]**기 때문에 형변환이 필요하다.
    - 타입 안정성이 떨어진다.
- 타입을 명시하는 **toArray(T[] a)**
    
    ```java
    String[] arr = list.toArray(new String[0]);
    ```
    
    - 넘긴 배열 타입에 맞춰 결과를 반환한다.
    - `new String[0]`을 넘기면, 자바가 알아서 크기를 맞춰서 새로운 배열을 만들어준다.
    - 가장 널리 쓰인다.

1. **Stream 사용**
- **String[] 배열로 변환**
    
    ```java
    String[] arr = list.stream().toArray(String[]::new);
    ```
    
    - Strean API를 활용해 배열로 변환한다.
    - `String[]::new`는 생성자 레퍼런스를 사용해 배열을 생성한다.
    - 코드가 간결하고 타입 안정성이 뛰어나다.
- **int[] 배열로 변환**
    
    ```java
    List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
    int[] arr = list.stream().mapToInt(Integer::intValue).toArray();
    ```
    
    - **`mapToInt()`**를 써서 **Integer → int**로 변환한다.
    - **`.toArray()`**를 쓰면 **int** 배열이 만들어진다.