---
layout: post
title:  "[자바] Object, 불변, String 클래스"
date:   2024-04-02 20:43:06 +0900
categories: 
            - Study
            - java
tags: [Java]            
toc: true
---
* this unordered seed list will be replaced by the toc
{:toc}

# 13. Object 

## java.lang 패키지
- 자바 언어를 이루는 가장 기본이 되는 클래스들을 보관하는 패키지
- java.lang 패키지의 대표적인 클래스들
  - Object : 모든 자바 객체의 부모 클래스
  - String : 문자열
  - Integer, Long, Double : 래퍼타입 (기본형 데이터 타입을 객체로 만든 것)
  - Class : 클래스 메타 정보
  - System : 시스템과 관련된 기본 기능들 제공
- java.lang 패키지는 자동으로 `ìmport` 된다. (`import` 생략 가능)
<br>

## Object 클래스
- 자바의 모든 클래스의 최상위 부모 클래스는 항상 `Object` 클래스이다.
  - 상속 받을 부모 클래스가 없으면 <strong>묵시적으로</strong> `Object` 클래스를 상속 받는다. 
  - 묵시적(implicit) vs. 명시적(explicit)
    - 묵시적 : 코드에 직접 기술하지 않아도 시스템 또는 컴파일러에 의해 자동 수행
    - 명시적 : 코드에 직접 기술해서 작동
<br>

## 자바에서 `Object` 클래스가 최상위 부모 클래스인 이유

1. 모든 객체에 필요한 공통 기능을 제공
  - toString(), equals(), getClass(), .. 
2. 모든 객체를 `Object` 타입으로 처리할 수 있어서 다형성 구현 가능
<br>

## Object 클래스의 다형성
  
  ```java
  private static void action(Object obj) {
    if (obj instanceof Dog dog) { // 다운캐스팅
      dog.sound();
    } else if (obj instanceof Car car) { 
      car.move();
    }
  }
  ```

  - `Object` 타입의 매개변수를 사용하면 어떤 객체든 인자로 전달 가능
  - `obj.sound()` 호출 불가(메서드 오버라이딩 불가) -> obj는 최종 부모 클래스이기 때문에 sound() 호출을 위해서 <strong>다운캐스팅</strong> 해야 한다.
<br>

## Object 배열
- `Object[]` : 세상의 모든 객체를 담을 수 있는 배열 
- `size(Object[] objects)` 메서드 : 배열에 담긴 객체의 수를 세는 역할 담당
  - `Object` 타입만 사용하기 때문에 새로운 클래스가 추가되거나 변경되어도 메서드의 수정이 필요없다.
<br>

## toString()
- Object 클래스의 메서드   
  - 객체의 정보를 제공  
  - 모든 클래스에서 상속 받아 사용 가능
  - 실행 결과 : ` java.lang.Object@a09ee92`
- `println()`
  - `println()` 내부에서 `toString()`을 호출하여 결과 출력
  - `toString()`을 호출할 필요 없이 객체를 바로 전달하면 객체 정보 출력
- 보통 `toString()`을 오버라이딩해서 정보를 제공한다. 
  - `toString()`을 오버라이딩 하지 않는다면 `Object` 클래스가 제공하는 기본 `toString()` 메서드를 사용한다. 
<br>

## equals() 
- `Object`가 제공하는 동등성 비교를 위한 메서드
  - 동일성 (Identity) : `==` 연산자를 사용해서 두 객체의 참조가 동일한 객체를 가리키고 있는 지 확인
  - 동등성 (Equality) : `equals()` 메서드를 사용해서 두 객체가 논리적으로 동등한 지 확인
- `equals()` 메서드 내부 
  ```java
   public boolean equals(Object obj) {
     return (this == obj);
     }
  ```

  - `Object`가 기본으로 제공하는 `equals()`는 `==`으로 동일성 비교를 제공한다.
  - 동등성 비교를 위해서는 메서드 재정의가 필요하다.
    ```java
    public class UserV2 {
      private String id;
      public UserV2(String id) {
        this.id = id;
      }

    @Override
    public boolean equals(Object obj) {
        UserV2 user = (UserV2) obj;
        return id.equals(user.id);
    }
    ```

    - 문자열 비교는 `equals()`를 사용
<br>
<br>

# 14. 불변 객체

## 기본형과 참조형 공유
- 기본형 : 하나의 값을 여러 변수에서 절대로 공유하지 않는다.
- 참조형 : 하나의 객체를 참조값을 통해 여러 변수에서 공유할 수 있다. 
<br>

## 사이드 이펙트 (side effect)
- 프로그램의 특정 부분에서 발생한 변경이 다른 부분에 의도치 않게 영향을 미치는 경우에 발생
- 디버깅이 어려워지고 코드의 안정성 저하
- 처음부터 서로 다른 객체를 참조하면 해결 가능하지만, 메모리와 성능상 비효율적
- 여러 변수가 하나의 객체를 공유하는 것을 막을 수 없다. => <strong>불변객체</strong> 도입
<br>

## 불변 객체 (Immutable Object)
- 사이드 이펙트는 객체를 공유한 뒤, <strong>공유된 객체의 값을 변경</strong> 할 때 발생한다.
- 불변 객체 도입
  ```java
  public class ImmutableAddress {

    private final String value; // 값 변경 불가

    public ImmutableAddress(String value) {
        this.value = value;
    }

    public String getValue() {
        return value;
    }

    @Override
    public String toString() {
        return "Address{" +
                "value='" + value + '\'' +
                '}';
    }
  }

  public static void main(String[] args) {
        ImmutableAddress address = new ImmutableAddress("서울");

        MemberV2 memberA = new MemberV2("회원A", address);
        MemberV2 memberB = new MemberV2("회원B", address);

        //회원B의 주소를 부산으로 변경해야함
        // memberB.getAddress().setValue("부산"); // 컴파일 오류
        memberB.setAddress(new ImmutableAddress("부산"));
        System.out.println("memberA = " + memberA); // 서울
        System.out.println("memberB = " + memberB); // 부산
    }
  ```

  - `final`로 필드 선언
  - 생성자를 통해서만 값을 설정 가능하다.
  - `memberB.setAddress(new ImmutableAddress("부산"))`
    - 값을 변경하기 위해 새로운 주소 객체를 만들어서 전달한다. 
<br>
<br>

# 15. String 클래스

## char, String
- char : 문자 하나를 다룰 때 사용
- String : 문자열을 다룰 때 사용
  - <strong>String은 참조형 클래스</strong>
  - 즉, String을 사용한 변수에는 String 인스턴스의 참조값만 들어갈 수 있다.<br>
  하지만 문자열은 매우 자주 사용되기 때문에 자바에서는 `String str1 = "hello"`라고 입력시 `String str1 = new String("hello")`와 같이 변경해준다. 
<br>

## 기능
- length() : 문자열의 길이를 반환한다.
- charAt(int index) : 특정 인덱스의 문자를 반환한다. 
- substring(int beginIndex, int endIndex) : 문자열의 부분 문자열을 반환한다.
- indexOf(String str) : 특정 문자열이 시작되는 인덱스를 반환한다.
- toLowerCase(), toUpperCase() : 문자열을 소문자 또는 대문자로 변환한다.
- trim() : 문자열 양 끝의 공백을 제거한다.
- concat(String str) : 문자열을 더한다.
  - 자바는 문자열을 더할 때 편의상 `+` 연산을 제공하고 있다. 

## 문자열 풀
  ```java
  public static void main(String[] args) {
        String str3 = "hello";
        String str4 = "hello";
        System.out.println("리터럴 == 비교: " + (str3 == str4)); // true
        System.out.println("리터럴 equals 비교: " + (str3.equals(str4))); // true
    }
  ```
  
  - 자바가 실행되는 시점에 클래스에 문자열 리터럴이 있으면 문자열 풀에 String 인스턴스를 미리 만들어둔다.
  - 같은 문자열이 있으면 인스턴스를 만들지 않는다.
  - 같은 문자를 사용하는 경우 메모리 사용과 문자를 만드는 시간을 줄이기 때문에 성능이 최적화된다.
<br>

## String 클래스 - 불변 객체
- String은 불변 객체이다.
  - 생성 이후에 절대로 내부의 문자열 값을 변경할 수 없다.
  - 사이드 이펙트 문제를 방지 하기 위해 불변 객체로 설계
- 변경이 필요한 경우, 새로운 결과를 만들어서 반환해야 한다. 
<br>

## String 클래스의 주요 메서드
- 문자열 정보 조회
  - `length()` : 문자열의 길이를 반환한다.
  - `isEmpty()` : 문자열이 비어 있는지 확인한다. (길이가 0)
  - `isBlank()` : 문자열이 비어 있는지 확인한다. (길이가 0이거나 공백(Whitespace)만 있는 경우)
  - `charAt(int index)` : 지정된 인덱스에 있는 문자를 반환한다.
<br>

- 문자열 비교
  - `equals(Object anObject)` : 두 문자열이 동일한지 비교한다. 
  - `equalsIgnoreCase(String anotherString)` : 두 문자열을 대소문자 구분 없이 비교한다. 
  - `compareTo(String anotherString)` : 두 문자열을 사전 순으로 비교한다. 
  - `compareToIgnoreCase(String str)` : 두 문자열을 대소문자 구분 없이 사전적으로 비교한다. 
  - `startsWith(String prefix)` : 문자열이 특정 접두사로 시작하는지 확인한다. 
  - `endsWith(String suffix)` : 문자열이 특정 접미사로 끝나는지 확인한다.
<br>

- 문자열 검색
  - `contains(CharSequence s)` : 문자열이 특정 문자열을 포함하고 있는지 확인한다.
  - `indexOf(String ch)` / `indexOf(String ch, int fromIndex)` : 문자열이 처음 등장하는 위치를 반환한다.
  - `lastIndexOf(String ch)` : 문자열이 마지막으로 등장하는 위치를 반환한다.
<br>

- 문자열 조작 및 변환
  - `substring(int beginIndex)` / `substring(int beginIndex, int endIndex)` : 문자열의 부분 문자열을 반환한다.
  - `concat(String str)` : 문자열의 끝에 다른 문자열을 붙인다. 
  - `replace(CharSequence target, CharSequence replacement)` : 특정 문자열을 새 문자열로 대체한다.
  - `replaceAll(String regex, String replacement)` : 문자열에서 정규 표현식과 일치하는 부분을 새 문자열로 대체한다.
  - `replaceFirst(String regex, String replacement)` : 문자열에서 정규 표현식과 일치하는 첫 번째 부분을 새 문자열로 대체한다.
  - `toLowerCase()` / `toUpperCase()` : 문자열을 소문자나 대문자로 변환한다. 
  - `trim()` : 문자열 양쪽 끝의 공백을 제거한다. 단순 `Whitespace` 만 제거할 수 있다. 
  - `strip()` : `Whitespace` 와 유니코드 공백을 포함해서 제거한다. 자바 11
<br>

- 문자열 분할 및 조합 
  - `split(String regex)` : 문자열을 정규 표현식을 기준으로 분할한다.
  - `join(CharSequence delimiter, CharSequence... elements)` : 주어진 구분자로 여러 문자열을 결합한다.
<br>

- 기타 유틸리티 
  - `valueOf(Object obj)` : 다양한 타입을 문자열로 변환한다.
  - `toCharArray():` 문자열을 문자 배열로 변환한다.
  - `format(String format, Object... args)` : 형식 문자열과 인자를 사용하여 새로운 문자열을 생성한다.
  - `matches(String regex)` : 문자열이 주어진 정규 표현식과 일치하는지 확인한다.
<br>

## StringBuilder - 가변 String
- `String` 클래스의 단점
  - `String`의 내부 값은 변경할 수 없기 때문에 새로운 `String` 객체를 생성한다.
  - 컴퓨터의 CPU, 메모리 자원을 더 많이 사용해야 한다.
- <strong>StringBuilder</strong> 
  - 자바가 제공하는 가변 `String`
  - 내부의 값을 바로 변경할 수 있어 새로운 객체를 생성할 필요가 없다.  
  ```java
  public final class StringBuilder { 
    char[] value;// 자바 9 이전 
    byte[] value;// 자바 9 이후
    
    //여러 메서드
    public StringBuilder append(String str) {...} 
    public int length() {...}
    ...
    }
  ```
  
  ```java
  public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        sb.append("A");
        sb.append("B");
        sb.append("C");
        sb.append("D");

        sb.insert(4, "Java");
        sb.delete(4, 8);
        sb.reverse();

        String string = sb.toString();
        System.out.println("string = " + string);
    }  
  ```
    - final이 아닌 변경할 수 있는 byte[]를 가지고 있다.
    - append() : 여러 문자열 추가
    - insert(index, 문자열) : 특정 위치에 문자열 삽입
    - delete(startIndex, endIndex) : 특정 범위의 문자열을 삭제
    - reverse() : 문자열을 뒤집는다.
    - 마지막에 toString()으로 StringBuilder의 결과를 String으로 생성하여 반환
<br>

## String 최적화
- 자바는 문자열 리터럴을 더하는 부분을 자동으로 합쳐준다. (최적화)
- 간단한 경우에는 `StringBuilder`를 사용하지 않고 문자열 더하기 연산(+)을 사용해도 충분
- 반복문의 경우, 각 반복에서 문자열이 어떻게 변할 지 예측 불가.
  - 반복문에서는 최적화가 어렵다. 
  - StringBuilder를 사용하면 된다. 
  
 




<br>
<br>
<br>

<blockquote>김영한의 실전 자바 기본편을 참고하였습니다</blockquote>










  

  


