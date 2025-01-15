---
layout: post
title:  "[Java] 패키지, 접근 제어자, 자바 메모리 구조, static, final, 상속"
date:   2024-01-15 20:43:06 +0900
categories: 
            - java
tags:            
toc: true
related_posts:
  - study/_posts/java/2024-01-22-자바-다형성.md
  - study/_posts/java/2024-04-04-자바-Object, 불변, String.md
  - study/_posts/java/2024-04-17-자바-래퍼, Class 클래스, 열거형 Enum, 날짜와 시간 .md
---
* this unordered seed list will be replaced by the toc
{:toc}

# 5. 패키지 

## 패키지 ?
- 자바 클래스들을 관련 있는 기능들로 분류하기 위한 디렉토리
<br>

## 패키지 사용
- 항상 코드 첫줄에 패키지 이름을 적어야 한다. 
  - `package pack;`
- 같은 패키지에 있는 경우 패키지 경로는 생략 가능하다. 
- 패키지가 다른 경우 
  1. 패키지의 전체 경로를 포함해서 클래스를 적어야한다. 
    - `pack.a.User`
  2. `import`를 사용하여 다른 패키지에 있는 클래스를 가져와 사용한다. 코드에서는 패키지 명을 생략하고 클래스 이름만 적을 수 있다. 
    - `import pack.a.User;`
    - `import pack.a.*;` : 특정 패키지에 포함된 모든 클래스를 포함해서 사용하고 싶을 때 *(별)을 사용한다.
- 패키지 별 클래스의 이름이 중복되는 경우, 자주 사용하는 클래스는 import하고 나머지는 패키지를 포함한 전체 경로를 적는다.
- 패키지 구성시, 서로 관련된 클래스는 하나의 패키지에 모으고, 관련이 적은 클래스는 다른 패키지로 분리한다.
<br>

## 패키지 규칙
- 패키지 이름과 위치는 폴더 위치와 같아야 한다.
- 패키지 이름은 모두 소문자를 사용한다.
<br>

## 패키지 계층 구조
- `a`, `a.b`, `a.c`는 서로 완전히 다른 패키지이다.
- `a` 패키지의 클래스에서 `a.b` 패키지의 클래스 사용시 `import`해서 사용해야 한다. 
<br>
<br>
<br>

# 6. 접근 제어자

## 접근 제어자의 종류
- `private` : 모든 외부 호출을 막는다. 같은 클래스 내에서만 접근 가능하다.
- `default (package-private)` : 같은 패키지 내에서만 접근 가능하다.
  - 접근 제어자를 명시하지 않으면 적용된다.
- `protected` : 같은 패키지 안에서 호출 가능하며, 다른 패키지라도 자식 클래스(상속)라면 접근 가능하다.
- `public` : 제한 없이 모든 외부 호출을 허용한다. 
<br>

## 접근 제어자 사용 - 필드, 메서드
  ~~~java
  package access.a; 

  public class AccessData {
    public int publicField;
    int defaultField;
    private int privateField;

    public void publicMethod() {
      System.out.println("publicMethod 호출 " + publicField);
    }

    void defaultMethod() {
      System.out.println("defaultMethod 호출 " + defaultField);
    }

    private void privateMethod() {
      System.out.println("privateMethod 호출 " + privateField);
    }

    public void innerAccess() {
      System.out.println("내부 호출");
      publicField = 100;
      defaultField = 200;
      privateField = 300;
      publicMethod();
      defaultMethod();
    privateMethod();
    } 
  }
  ~~~

  ~~~java
  package access.a;

  public class AccessInnerMain {
  public static void main(String[] args) {
  AccessData data = new AccessData();
  //public 호출 가능
  data.publicField = 1;
  data.publicMethod();

  // 같은 패키지 default 호출 가능
  data.defaultField = 2;
  data.defaultMethod();

  // private 호출 불가
  // data.privateField = 3;
  // data.privateMethod();

  data.innerAccess();
  }
  }
  ~~~

  ~~~java
  package access.b;

  import access.a.AccessData;

  public class AccessOuterMain {
    public static void main(String[] args) {
      AccessData data = new AccessData();
      //public 호출 가능
      data.publicField = 1;
      data.publicMethod();

      // 다른 패키지 default 호출 불가
      // data.defaultField = 2;
      // data.defaultMethod();

      // private 호출 불가
      // data.privateField = 3;
      // data.privateMethod();

      data.innerAccess();
    }
  }
  ~~~

- innerAccess() 메서드는 외부에서 호출되었지만 innerAccess() 메서드는 AccessData에 포함되어있기 때문에 <span style="background-color: #fff5b1">자신의 private 필드와 메서드에 모두 접근 가능하다.</span>
<br>

## 접근 제어자 사용 - 클래스 레벨
- 클래스 레벨의 접근 제어자는 `public`, `default`만 사용 가능하다.
- `public` 클래스는 반드시 파일명과 이름이 같아야 한다. 
- 하나의 자바 파일에 `public` 클래스는 하나만, `default` 클래스는 무한정 만들 수 있다.
<br>

## 캡슐화
- <span style="background-color: #fff5b1">캡슐화(Encapsulation)</span> : 데이터와 해당 데이터를 처리하는 메서드를 하나로 묶어서 외부에서의 접근을 제한하는 것이다.
- 객체의 데이터(속성)는 모두 숨기고, 객체의 기능은 내부에서 사용하는 기능들을 숨겨 필요한 기능만 노출하는 게 좋은 캡슐화이다. 
  ~~~java
  package access;

  public class BankAccount {
    private int balance; // 객체의 데이터는 private로 숨긴다. 
                        // BankAccount가 제공하는 메서드를 통해서만 접근 가능하다. 

    public BankAccount() {
      balance = 0;
    }

    public void deposit(int amount) {
      if (isAmountValid(amount)) {
        balance += amount;
      } else {
        System.out.println("유효하지 않은 금액입니다.");
      }
    }

    public void withdraw(int amount) {
      if (isAmountValid(amount) && balance - amount >= 0) {
        balance -= amount;
      } else {
        System.out.println("유효하지 않은 금액이거나 잔액이 부족합니다.");
      }
    }

    public int getBalance() {
      return balance;
    }

    // private 메서드로 내부에서만 필요한 기능이다.
    private boolean isAmountValid(int amount) {
      return amount > 0; // 
    }
  }
  ~~~

  ~~~java
  package access;

  public class BankAccountMain {
    public static void main(String[] args) {
      BankAccount account = new BankAccount();
      account.deposit(1000);
      account.withdraw(3000);
      System.out.println("balance = " + account.getBalance());
    }
  }
  ~~~
<br>
<br>
<br>

# 7. 자바 메모리 구조와 static

## 자바 메모리 구조

<center><img width="606" alt="image" src="https://github.com/yaejinkong/yaejinkong.github.io/assets/127467781/9c711e57-8ac4-4307-8811-f7359744612f"></center><br>

- <span style="background-color: #fff5b1">메서드 영역 (Method Area)</span> : 프로그램을 실행하는데 필요한 공통 데이터를 관리하며, 프로그램의 모든 영역에서 공유한다. 
  - 클래스 정보 : 클래스의 실행코드, 필드, 메서드, 생성자 코드 등 모든 실행 코드가 존재한다.
  - static 영역 : `static` 변수들을 보관한다.
  - 런타임 상수 풀 : 프로그램을 실행하는데 필요한 공통 리터럴 상수를 보관한다. 
- <span style="background-color: #fff5b1">스택 영역 (Stack Area)</span> : 자바가 실행되면 하나의 실행 스택이 생성되고, 스택 영역에 스택 프레임이 쌓인다.
  - 스택 프레임에는 지역 변수, 중간 연산 결과, 메서드 호출 정보 등을 포함한다. 
  - 메서드가 종료되면 해당 스택 프레임이 제거된다.
- <span style="background-color: #fff5b1">힙 영역 (Heap Area)</span> : 객체(인스턴스)와 배열이 생성되는 영역이다.
  - 가비지 컬렉션(GC)이 이루어지는 영역이며, 참조되지 않는 객체(인스턴스)는 GC에 의해 메모리에서 제거된다.
  - 힙 영역 안에서만 객체끼리 서로 참조하는 경우에도 GC의 대상이 된다.
  - 각 스레드별로 하나의 실행 스택이 생성된다.
  - 인스턴스의 메서드를 호출하면 실제로는 메서드 영역에 있는 코드를 불러서 수행한다.

<br>

## 스택과 큐 자료구조
- 스택 (Stack)
  - 후입 선출 (LIFO) : 나중에 넣은 것이 가장 먼저 나온다.
  - <span style="background-color: #fff5b1">자바는 스택 영역을 사용해서 메서드 호출과 지역 변수 (매개변수 포함)을 관리한다.</span>
- 큐 (Queue)
  - 선입 선출 (FIFO) : 가장 먼저 넣은 것이 가장 먼저 나온다.
<br>

## static 변수
- static 변수, 정적 변수, 클래스 변수라고 한다.
- `static` 키워드를 사용하면 <span style="background-color: #fff5b1">공용으로 함께 사용하는 변수</span>를 만들 수 있다.
- `static`이 붙은 멤버 변수는 <span style="background-color: #fff5b1">메서드 영역에서 관리</span>하며, 인스턴스 영역에는 생성되지 않는다.
- `static`이 붙은 정적 변수에 접근하려면 <span style="background-color: #fff5b1">클래스명 + .(dot) + 변수명</span>으로 접근하면 된다.
- 자신의 클래스에 있는 정적 변수라면 클래스명을 생략할 수 있다.
<br>

## 멤버 변수(필드)의 종류
  ~~~java
  public class Data3 {
    public String name; // 인스턴스 변수 
    public static int count; // 클래스 변수

    public Data3(String name) {
      this.name = name;
      count++;
    }
  }
  ~~~

- 인스턴스 변수 : `static`이 붙지 않은 멤버 변수
  - 인스턴스를 생성해야 사용 가능하며, 인스턴스에 소속되어 있다.
  - 인스턴스 변수는 인스턴스를 만들 때마다 새로 만들어진다.
  - 인스턴스 변수는 힙 영역을 사용한다. 힙 영역은 GC가 발생하기 전까지 생존해서 보통 지역 변수보다 생존 주기가 길다.
    - 지역 변수 : 스택 영역에 있는 스택 프레임 안에 보관된다. 
    - 지역변수는 메서드가 종료되면서 스택 프레임이 제거될 때 지역변수도 함께 제거되기 때문에 생존 주기가 짧다.

- 클래스 변수 : `static`이 붙은 멤버 변수
  - 클래스 변수, 정적 변수, static 변수라고 한다.
  - 인스턴스와 무관하게 클래스에 바로 접근해서 사용할 수 있고, 클래스 자체에 소속되어 있다.
  - 클래스 변수는 여러 곳에서 공유하는 목적으로 사용되며, 자바 프로그램을 시작할 때 1개가 만들어진다. 
  - <span style="background-color: #fff5b1">메서드 영역의 static 영역에 보관된다.</span>
    - 해당 클래스가 JVM에 로딩되는 순간 생성되고 JVM이 종료될 때까지 생명주기가 이어진다. 가장 긴 생명주기를 가진다.
<br>

## static 메서드
  ~~~java
  package static2;

  public class DecoUtil2 {
    public static String deco(String str) {
      String result = "*" + str + "*";
      return result1;
    }
  }
  ~~~

  ~~~java
  package static2; 

  public class DecoMain2 {
    public static void main(String[] args) {
      String s = "hello java";
      String deco = DecoUtil2.deco(s);

      System.out.println("befor: " + s);
      System.out.println("after: " + deco);
    }
  }
  ~~~

- `static`이 붙은 정적 메서드는 클래스 메서드라고도 한다.
- 정적 메서드는 정적변수와 같이 <span style="background-color: #fff5b1">인스턴스 생성 없이 클래스 명 + .(dot) + 메서드 명을 통해서 바로 호출 가능</span>하다.
  - `static`이 붙지 않은 인스턴스 메서드는 인스턴스를 생성해야 호출할 수 있다. 
- 클래스 내부의 기능을 사용할 때, 정적 메서드는 `static`이 붙은 정적 메서드나 정적 변수만 사용할 수 있다. 
  - 클래스 내부의 기능을 사용할 때, 정적 메서드는 인스턴스 변수, 인스턴스 메서드를 사용할 수 없다.
    - 사용 시 컴파일 오류가 발생한다. 
- 모든 곳에서 `static`을 호출할 수 있다.
- 객체 생성의 필요 없이 메서드의 호출만으로 필요한 기능을 수행할 때 주로 사용한다.
<br>

## 멤버 메서드의 종류
- 인스턴스 메서드 : `static`이 붙지 않은 멤버 메서드
- 클래스 메서드 : `static`이 붙은 멤버 메서드
  - 클래스 메서드, 정적 메서드, static 메서드라고 부른다.
<br>

## static import
- `static import` 기능을 사용하면 클래스 명을 생략하고 메서드를 호출할 수 있다.
- 정적 변수에도 사용 가능하다. 

  ~~~java 
  package static2;

  public class DecoData {
    private int instanceValue;
    private static int staticValue;
    public static void staticCall() {
      //instanceValue++; // 컴파일 에러
      //instanceMethod(); // 컴파일 에러
      staticValue++;
      staticMethod(); 
    }

    public void instanceCall() {
      instanceValue++;
      instanceMethod();
      staticValue++;
      staticMethod();
    }

    private void instanceMethod() {
      System.out.println("instanceValue=" + instanceValue);
    }

    private static void staticMethod() {
      System.out.println("staticValue=" + staticValue);
    }
  }
  ~~~

  ~~~java
  package static2;

  // import static static2.DecoData.staticCall;
  import static static2.DecoData.*; // DecoData 클래스의 모든 정적 메서드에 적용

  public class DecoDataMain {
    public static void main(String[] args) {
      System.out.println("1. 정적호출");
      staticCall(); // 클래스명 생략 가능
      ...
    }
  }
  ~~~
<br>

## main() 메서드
- main() 메서드는 `static` 이기 때문에 인스턴스(객체) 생성 없이 작동한다.
- main() 메서드와 같은 클래스에서 호출하는 메서드도 정적 메서드로 선언해서 사용한다. 

  ~~~java
  public class ValueDataMain {
    public static void main(String[] args) {
      ValueData valueData = new ValueData();
      add(valueData);
    }

    static void add(ValueData valueData) {
      valueData.value++;
      System.out.println("숫자 증가 value=" + valueData.value);
    }
  }
  ~~~

# 8. final
<br>
<br>

## final
- `final` 키워드는 더 이상 변경할 수 없다는 뜻이다.
- `final`은 변수, 클래스, 메서드를 포함한 여러 곳에 붙을 수 있다.
<br>

## final 변수와 상수
- 지역 변수에 `final`이 붙으면 최초 한 번만 값을 할당할 수 있다.
- 매개변수에 `final`이 붙으면 메서드 내부에서 매개변수의 값을 변경할 수 없다. 
  ~~~java
  package final1; 

  public class FinalLocalMain {
    public static void main(String[] args) {
      final int data1;
      data1 = 10; 
      // data1 = 20; // 최초 한 번만 할당 가능, 컴파일 오류

      final int data2 = 10;
      data2 = 20; // 컴파일 오류

      method(10); 
    }

    //final 매개변수
    static void method(final int parameter) {
      // parameter = 20; // 매개변수의 값 변경 불가, 컴파일 오류
    }
  }
  ~~~
<br>

- `final`을 필드(멤버 변수)에 사용할 경우 해당 필드는 생성자를 통해서 한 번만 초기화될 수 있다.

  ~~~java
  package final1; 

  public class ConstructInit {
      final int value;

      public ConstructInit(int value) {
        this.value = value; // 한 번만 초기화 가능
      }
  }

  public class FieldInit {
    static final int CONST_VALUE = 10; 
    final int value = 10;
  }
  ~~~

  ~~~java
  package final1; 

  public class FinalFieldMain {
    public static void main(String[] args) {
      //final 필드 - 생성자 초기화
      ConstructInit constructInit1 = new ConstructInit(10);
      ConstructInit constructInit2 = new ConstructInit(20);
      System.out.println(constructInit1.value); // 10
      System.out.println(constructInit2.value); // 20

      // final 필드 - 필드 초기화
      FieldInit fieldInit1 = new FieldInit();
      FieldInit fieldInit2 = new FieldInit();
      FieldInit fieldInit3 = new FieldInit();
      // 모든 인스턴스가 같은 값을 사용, 메모리 낭비
      System.out.println(fieldInit1.value); // 10
      System.out.println(fieldInit2.value); // 10
      System.out.println(fieldInit3.value); // 10 

      // 상수 - 단 하나만 존재, 중복과 메모리 비효율 문제 해결
      System.out.println(FieldInit.CONST_VALUE);
    }
  }
  ~~~

## 상수(Constant)
- 상수 : 단 하나만 존재하며, 변하지 않는 고정된 값
- 자바 상수는 `static final` 키워드를 사용한다.
- 대문자를 사용하고 구분은 _(언더스코어)로 한다.
- 필드를 직접 접근해서 사용한다. 

  ~~~java
  package final1;

  public class Constant {
    public static final int MAX_USERS = 1000;
  }
  ~~~

  ~~~java
  package final1;

  public class ConstantMain2 {
    public static void main(String[] args) {
      System.out.println("프로그램 최대 참여자 수 " + Constant.MAX_USERS);
      int currentUserCount = 999;
      process(currentUserCount++);
      process(currentUserCount++);
      process(currentUserCount++);
    }

    private static void process(int currentUserCount) {
      System.out.println("참여자 수: " + currentUserCount);
      if (currentUserCount > Constant.MAX_USERS) { // 상수 사용
        System.out.println("대기자로 등록합니다.");
      } else {
        System.out.println("게임에 참가합니다.");
      }
    }
  }
  ~~~

  - 프로그램 최대 참여자 수를 변경해야 하면 `Constant.MAX_USERS`의 상수 값만 변경 하면 된다.
<br>

## final 변수와 참조
- 기본형 변수에 `final` 사용시 값을 변경할 수 없다.
- 참조형 변수에 `final` 사용시 참조값을 변경할 수 없다.
  ~~~java
  package final1; 

  public class Data {
    public int value;
  }
  ~~~

  ~~~java
  package final1;

  public class FinalRefMain {
    public static void main(String[] args) {
      final Data data = new Data();
      // data = new Data(); // 참조값 변경 불가, 컴파일 오류
  
      // 참조형 변수인 data에 들어있는 value는 final이 아니기 때문에 변경 가능
      data.value = 10;
      data.value = 20;
    }
  }
  ~~~

## final 클래스와 메서드
- final 클래스
  - 확장될 수 없다. (상속 X)
  - 다른 클래스가 final로 선언된 클래스를 상속받을 수 없다.
- final 메서드
  - 오버라이드 될 수 없다. (오버라이딩 X)
  - 상속받은 하위 클래스에서 메서드를 변경할 수 없다.

<br>
<br>
<br>

# 9. 상속

## 상속 관계
- 상속 : <span style="background-color: #fff5b1">기존 클래스(부모 클래스)의 필드와 메서드를 새로운 클래스(자식 클래스)가 재사용 하는 것</span>
- `extends` 키워드를 사용한다.
- <span style="background-color: #fff5b1">단일 상속</span> : 자바는 `extends` 대상은 하나만 선택할 수 있다. 
- 부모 클래스는 자식 클래스에 접근할 수 없다.
- 자식 클래스는 부모 클래스에 접근할 수 있다. (부모 클래스의 기능을 물려 받음)
<br>

  ~~~java
  package extends1;

  public class Car {
    public void move() {
      System.out.println("차를 이동합니다.");
    }
  }
  ~~~

  ~~~java
  package extends1; 

  public class ElectricCar extends Car {
    public void charge() {
      System.out.println("충전합니다.");
    }
  }
  ~~~

  ~~~java
  package extends1;

  public class GasCar extends Car {
    public void fillUp() {
      System.out.println("기름을 주유합니다.");
    }
  }
  ~~~

  ~~~java
  package extends1;

  public class CarMain {
    public static void main(String[] args) {
      ElectricCar electricCar = new ElectricCar();
      electricCar.move();
      electricCar.charge();

      GasCar gasCar = new GasCar();
      gasCar.move();
      gasCar.fillUp();
    }
  }
  ~~~

  <center><img width="626" alt="image" src="https://github.com/yaejinkong/yaejinkong.github.io/assets/127467781/f0cb7f5e-0521-4bfd-9013-66a70f522901"></center>
  
- 상속 관계를 사용하면 부모 클래스도 함께 포함해서 생성된다.
  - `new ElectricCar()`을 호출하면 ElectricCar과 Car 두 가지 클래스 정보가 공존하는 참조값 x001을 생성한다.
- 상속 관계에서 메서드를 호출하면 호출자의 타입(클래스)을 기준으로 선택한다.
  - `electricCar.charge()`를 호출 시, 호출하는 변수의 클래스인 ElectricCar을 기준으로 인스턴스 내부에 같은 타입인 ElectricCar를 통해서 charge()를 호출한다.
- 상속 관계에서 자식 타입에 해당 기능이 없으면 부모 타입으로 올라가서 찾는다. 
  - 부모 타입에서도 해당 기능을 찾지 못하면 더 상위 부모에서 필요한 기능을 찾는다.
  - `electricCar.move()`를 호출 시, ElectriCar에는 move() 메서드가 없기 때문에 부모 타입인 Car로 올라가서 move() 메서드를 찾고 호출한다.
<br>

## 상속과 메서드 오버라이딩
- <span style="background-color: #fff5b1">메서드 오버라이딩 (Overriding)</span> : 부모에게서 상속 받은 기능을 자식이 재정의 하는 것
- `@Override` : 오버라이딩 조건을 만족시키지 않으면 컴파일 에러를 발생시킨다.

  ~~~java
  package extends1;

  public class Car {
    public void move() {
      System.out.println("차를 이동합니다.");
    }

    public void openDoor(){
      System.out.println("문을 엽니다.");
    }
  }
  ~~~

  ~~~java
  package extends1; 

  public class ElectricCar extends Car {
    @Override
    public void move() {
      System.out.println("전기차를 빠르게 이동합니다.");
    }

    public void charge() {
      System.out.println("충전합니다.");
    }
  }
  ~~~

  ~~~java
  package extends1;

  public class CarMain {
    public static void main(String[] args) {
      ElectricCar electricCar = new ElectricCar();
      electricCar.move(); // 전기차를 빠르게 이동합니다.

      GasCar gasCar = new GasCar();
      gasCar.move(); // 차를 이동합니다.
    }
  }
  ~~~

  <center><img width="630" alt="image" src="https://github.com/yaejinkong/yaejinkong.github.io/assets/127467781/26eb501b-1de0-4cba-a99a-0f52decd2b85"></center>

  - `electricCar.move()`를 호출하면 ElectricCar 타입에서 먼저 move() 메서드를 찾고 실행한다. 실행할 메서드를 이미 찾았으므로 부모 타입을 찾지 않는다.
<br>

## 오버로딩(Overloading)과 오버라이딩(Overriding)
- 메서드 오버로딩 : 메서드 이름이 같고 매개변수가 다른 메서드를 여러 개 정의한다.
- 메서드 오버라이딩 : 상속 관계의 하위 클래스에서 상위 클래스의 메서드를 재정의한다. 
<br>

## 메서드 오버라이딩 조건
- 메서드 이름 : 메서드 이름이 같아야 한다. 
- 메서드 매개변수 : 매개변수 타입, 순서, 개수가 같아야 한다.
- 반환 타입 : 반환 타입이 같아야 한다. 
- 접근 제어자 : 오버라이딩 메서드의 접근 제어자는 상위 클래스의 메서드보다 더 제한적이어서는 안된다.
- 예외 : 오버라이딩 메서드는 상위 클래스의 메서드보다 더 많은 체크 예외를 throws로 선언할 수 없다.
- static, final, private 키워드가 붙은 메서드는 오버라이딩 될 수 없다. 
- 생성자는 오버라이딩 할 수 없다.
<br>

## super - 부모 참조

  ~~~java
  package super1;

  public class Parent {
    public String value = "parent";

    public void hello() {
      System.out.println("Parent.hello");
    }
  }
  ~~~

  ~~~java
  package super1; 

  public class Child extends Parent {
    public String value = "child";

    @Override
    public void hello() {
      System.out.println("Child.hello");
    }

    public void call() {
      System.out.println("this value = " + this.value); // this 생략 가능
      System.out.println("super value = " + super.value);

      this.hello(); // this 생략 가능
      super.hello(); 
    }
  }
  ~~~

  ~~~java
  package super1;

  public class Super1Main {
    public static void main(String[] args) {
      Child child = new Child();
      child.call();
    }
  }
  ~~~

- 실행결과

  ```
  this value = child
  super value = parent
  Child.hello
  Parent.hello
  ```

  - `this` : 자기 자신의 참조이다. 생략 가능하다. 
  - `super` : 부모 클래스에 대한 참조를 나타낼 수 있다.
    - 부모 클래스에 있는 기능을 사용할 수 있다. 
<br>

## super - 생성자
- 상속 관계를 사용시
  - 자식 클래스의 생성자에서 부모 클래스의 생성자를 반드시 호출해야 한다.
  - 부모의 생성자를 호출할 때 `super(...)`를 사용한다.
  - 부모의 생성자가 기본 생성자(매개변수가 없는 생성자)인 경우, `super()`를 생략 가능하다. 
- 초기화는 최상위 부모부터 이루어진다.
- 자식 클래스의 생성자 첫 줄에 `this(...)`를 사용하더라도 반드시 한 번은 `super(...)`를 호출해야 한다. 

  ~~~java
  package super2;

  public class ClassA { 
    public ClassA() {
      System.out.println("ClassA 생성자");
    }
  }
  ~~~

  ~~~java
  package super2; 

  public class ClassB extends ClassA {
    public ClassB(int a) {
      super(); // 기본 생성자 생략 가능
      System.out.println("ClassB 생성자 a= " + a);
    } 

    public ClassB(int a, int b) {
      super(); // 기본 생성자 생략 가능
      System.out.println("ClassB 생성자 a= " + a + " b= " + b);
    }
  }
  ~~~

  ~~~java
  package super2;

  public class ClassC extends ClassB {
    public ClassC() {
      super(10,20);
      System.out.println("ClassC 생성자");
    }
  }
  ~~~
- 생성자는 하나만 호출할 수 있다. 
  - ClassC는 ClassB의 두 생성자 중 ClassB(int a, int b)를 선택했다.
<br>
<br>
<br>

<blockquote>김영한의 실전 자바 기본편을 참고하였습니다</blockquote>
















  
  




  







