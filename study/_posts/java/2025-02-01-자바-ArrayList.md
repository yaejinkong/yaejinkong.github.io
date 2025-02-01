---
layout: post
title:  "[Java] ArrayList"
date:   2025-02-01 19:22:06 +0900
categories: 
            - java
tags:          
toc: true
related_posts:
  - study/_posts/java/2025-01-17-자바-제네릭.md
  - study/_posts/java/2025-01-18-자바-제네릭(2).md
  - study/_posts/java/2025-01-19-자바-제네릭(3).md
---
* this unordered seed list will be replaced by the toc
{:toc}

# 23. ArrayList

## ☑️ 배열

### 배열의 특징 1 - index

- 배열에서 자료를 찾을 때는 **`인덱스(index)`** 를 사용하면 한 번의 계산으로 위치를 찾을 수 있다.
    
    ![Image](https://github.com/user-attachments/assets/9f8ab9c7-b750-48db-ae55-82473ecd6965)
    
- 메모리 위치 찾는 공식 : **`배열의 시작 참조 + (자료의 크기 * 인덱스 위치)`**
- 예를 들어, **`arr[2]`** 의 자료를 찾고자 하면
    - 배열의 시작 위치인 **`x100`**
    - **`int`** 타입의 자료의 크기인 **`4byte`**  *****  ****인덱스 번호인 **`2`**
    - 둘을 더한 값인 **`x108`** 에 자료가 위치한다.
- 데이터가 많아도 **한 번의 연산**으로 자료의 위치를 찾을 수 있다! → **`O(1)`**

### 배열의 특징 2 - 검색

- 배열에 들어있는 데이터를 찾기 위해서는 데이터를 하나씩 확인해야 한다.
- 배열의 크기가 클수록 오랜 시간이 걸린다.
    - 배열의 크기가 2인 경우 : `arr[0]` , `arr[1]` → 연산을 2번 한다.
    - 배열의 크기가 10인 경우 : `arr[0]` , `arr[1]` … `arr[9]` → 연산을 10번 한다.
- 배열의 크기가 **`n`** 인 경우, 연산은 **`n`** 만큼 필요하다. → **`O(n)`**

### 배열의 특징 3 - 데이터 추가

- 배열의 **`첫 번째 위치`**에 데이터를 추가하는 경우
    - 첫 번째 위치를 찾기 위해 인덱스를 사용한다. → **`O(1)`**
    - 모든 데이터를 배열의 크기만큼 한 칸씩 오른쪽으로 밀어야 한다. → **`O(n)`**
    - 새로운 데이터를 첫 번째 인덱스에 추가한다.
    - **`O(1 + n)`** → **`O(n)`**
- 배열의 **`중간 위치`**에 데이터를 추가하는 경우
    - 배열의 위치를 찾는데 인덱스를 사용한다. → **`O(1)`**
    - 해당 인덱스부터 데이터들을 한 칸씩 오른쪽으로 밀어야 한다. → **`O(n/2)`**
    - 새로운 데이터를 해당 인덱스에 추가한다.
    - **`O(1 + n/2)`** → **`O(n)`**
- 배열의 **`마지막 위치`**에 데이터를 추가하는 경우
    - 배열의 마지막 위치를 찾는데 인덱스를 사용한다. → **`O(1)`**
    - 새로운 데이터를 마지막 인덱스에 추가한다.
    - **`O(1)`**

### 배열의 한계

- 배열은 처음 생성하는 시점에서 배열의 크기를 정해야 한다.
- 데이터를 추가하려면 직접 오른쪽으로 한 칸씩 데이터를 밀어야 한다.
- 따라서, 동적으로 길이를 조절할 수 있고, 데이터를 추가하기 위해 **`List(리스트)`** 를 사용한다.

## ☑️ 리스트

### 배열 vs 리스트

- **배열**은 순서가 있고 중복을 허용하지만, 크기가 **`정적으로 고정된다`**.
- **리스트**는 순서가 있고 중복을 허용하지만, 크기가 **`동적으로 변할 수 있다`**.
- 즉, 배열보다 리스트가 보다 **`유연한 자료구조`**임을 알 수 있다.

### ArrayList 구현

```java
import java.util.Arrays;

public class ArrayList<E> {

    private static final int DEFAULT_CAPACITY = 5;

    private Object[] elementData;
    private int size = 0;

    public ArrayList() {
        elementData = new Object[DEFAULT_CAPACITY];
    }

    public ArrayList(int initialCapacity) {
        elementData = new Object[initialCapacity];
    }

    public int size() {
        return size;
    }

    public void add(E e) {
        if (size == elementData.length) {
            grow();
        }

        elementData[size] = e;
        size++;
    }

    public void add(int index, E e) {
        if (size == elementData.length) {
            grow();
        }

        shiftRightFrom(index);
        elementData[size] = e;
        size++;
    }

    private void shiftRightFrom(int index) {
        for (int i = size; i > index; i--) {
            elementData[i] = elementData[i - 1];

        }
    }

    private void grow() {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity * 2;

        Object[] newArr = Arrays.copyOf(elementData, newCapacity);
        elementData = newArr;
    }

    @SuppressWarnings("unchecked")
    public E get(int index) {
        return (E) elementData[index];
    }

    public E set(int index, E element) {
        E oldValue = get(index);
        elementData[index] = element;
        return oldValue;
    }

    public E remove(int index) {
        E oldValue = get(index);
        shiftLeftFrom(index);

        size--;
        elementData[size] = null;
        return oldValue;
    }

    private void shiftLeftFrom(int index) {
        for (int i = index; i < size - 1; i++) {
            elementData[i] = elementData[i + 1];
        }
    }

    public int indexOf(E o) {
        for (int i = 0; i < size; i++) {
            if (o.equals(elementData[i])) {
                return i;
            }
        } return -1;
    }

    public String toString() {
        return Arrays.toString(Arrays.copyOf(elementData, size))
                + " size = " + size + ", capacity = " + elementData.length;
    }
}
```

- 강의에서 배열을 활용하여 **`ArrayList`**를 직접 만들어본 코드이다.
    - 배열을 사용한 리스트라고 해서 **`ArrayList`**라고 부른다.
- 하나씩 살펴보도록 하겠다.

### ArrayList - Object 배열

- `private Object[] elementData;`
- 제네릭은 타입이레이저에 의해 **런타임** 시에 **타입 정보가 사라진다.**
    - 아래 글에서 상세하게 설명한다.
    
    [[Java] 제네릭 (Generic) 3](https://yaejinkong.github.io/study/java/2025-01-19-%EC%9E%90%EB%B0%94-%EC%A0%9C%EB%84%A4%EB%A6%AD(3)/#%EF%B8%8F%ED%83%80%EC%9E%85-%EC%9D%B4%EB%A0%88%EC%9D%B4%EC%A0%80)
    

**컴파일 전**

```java
private E[] elementData;
private static final int DEFAULT_CAPACITY = 5;

public ArrayList() {
    elementData = new E[DEFAULT_CAPACITY];
}
```

- 위는 생성자에 **제네릭 타입 매개변수**를 사용한 예시이다.

```java
private String[] elementData;
private static final int DEFAULT_CAPACITY = 5;

public ArrayList() {
    elementData = new String[DEFAULT_CAPACITY];
}
```

- 컴파일 전에는 제네릭에 **`String`** 타입이 그대로 전달된다.

**컴파일 후**

```java
private Object[] elementData;
private static final int DEFAULT_CAPACITY = 5;

public ArrayList() {
    elementData = new Object[DEFAULT_CAPACITY];
}
```

- 컴파일 후에는 타입 정보가 사라져 **`Object`** 로 변환된다.
- 제네릭을 기반으로 배열을 생성하는 코드는 작동하지 않고, **`컴파일 오류`** 가 발생한다.
- 따라서 **생성자에는 제네릭의 타입 매개변수를 사용할 수 없으며,** 배열을 생성할 때 **`Object`** 배열을 사용해야 한다.

**제네릭 타입 적용**

```java
Object[] elementData;

void add(E e) {
		elementData[size] = e;
		...
}

E get(int index) {
		return (E) elementData[index];
}
```

- 제네릭은 리스트의 데이터를 입력받고, 반환하는 곳의 타입을 고정할 때는 사용한다.

```java
Object[] elementData;

void add(String e) {
		elementData[size] = e;
		...
}

String get(int index) {
		return (String) elementData[index];
}
```

- 만약 타입 매개변수에 **`String`** 으로 지정한다면 다음과 같다.
- **`add()`**  를 통해 **`elementData`** 배열에는 항상 **`String`** 타입으로 보관된다.
- **`get()`** 를 통해 **`elementData`** 배열에서 데이터를 꺼낼 때는 항상 **`String`** 타입으로 **다운캐스팅** 해준다.

### ArrayList - 데이터 추가

**add(E e)**

```java
public void add(E e) {
    if (size == elementData.length) {
        grow();
    }

    elementData[size] = e;
    size++;
}
```

- 리스트에 데이터를 추가한다.
- 만약 **`size`** 가 배열의 크기와 동일하다면 **`grow()`** 메서드를 이용하여 배열의 크기를 **`2배`** 로 늘려준다.
    
    ```java
    private void grow() {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity * 2;
    
        Object[] newArr = Arrays.copyOf(elementData, newCapacity);
        elementData = newArr;
    }
    ```
    
    - 예제의 단순화를 위해 **`2배`** 를 증가시켰지만, 보통은 **`50%`** 정도 증가시킨다고 한다.
    - **`Arrays.copyOf(기존배열, 새로운 길이)`** : 새로운 길이로 배열을 생성하고, 기존 배열을 복사한다.
    - 새로운 배열인 `newArr`로 참조값을 변경한다.
    - 기존 배열은 **`GC`** 의 대상이 된다.

- 새로운 데이터를 추가하고 리스트의 크기인 **`size`** 를 증가시킨다.
    - 리스트의 크기 즉, 실제 데이터가 입력된 크기는 **`size`** 로 표현하고, 배열의 크기는 **`capacity`** 로 표현한다.
- 기존 데이터를 이동하지 않기 때문에 **`O(1)`**

**add(index, 데이터)**

```java
public void add(int index, E e) {
    if (size == elementData.length) {
        grow();
    }

    shiftRightFrom(index);
    elementData[size] = e;
    size++;
}
```

- 원하는 **`index`** 위치에 데이터를 추가한다.
- **`shiftRightFrom(index)`** 메서드를 통해 **`index`** 위치부터 데이터를 한 칸씩 먼저 이동한다.
    
    ```java
    private void shiftRightFrom(int index) {
        for (int i = size; i > index; i--) {
            elementData[i] = elementData[i - 1];
        }
    }
    ```
    
- 새로운 데이터를 추가하고 **`size`** 를 증가시킨다.
- **`index`** 의 위치에 따라 **시간복잡도**가 달라진다.
    - **`마지막 위치`**에 추가 시 기존 데이터를 이동하지 않기 때문에 **`O(1)`** 이 소요된다.
    - **`맨 앞`** 또는 **`중간 위치`** 에 추가 시 배열의 인덱스를 찾는데 **`O(1)` +** 데이터를 이동하는데 **`O(n)`** 즉, **`O(n)`** 이 소요된다.
    

### ArrayList - 데이터 삭제

**remove(index)**

```java
public E remove(int index) {
    E oldValue = get(index);
    shiftLeftFrom(index);

    size--;
    elementData[size] = null;
    return oldValue;
}
```

- **`get(index)`** 메서드로 **`index`** 위치에 있는 데이터를 찾는다.
    
    ```java
    public E get(int index) {
        return (E) elementData[index];
    }
    ```
    

- **`shiftLeftFrom(index)`** 메서드를 통해 **`index`** 위치부터 데이터를 한 칸씩 왼쪽으로 이동한다.
    
    ```java
    private void shiftLeftFrom(int index) {
        for (int i = index; i < size - 1; i++) {
            elementData[i] = elementData[i + 1];
        }
    }
    ```
    

- **`size`** 를 줄인 후, 마지막 요소를 **`null`** 로 설정하여 메모리 누수를 방지한다.
- **`index`** 의 위치에 따라 **시간복잡도**가 달라진다.
    - **`마지막 요소`** 를 제거 시 기존 데이터를 이동하지 않기 때문에 **`O(1)`** 이 소요된다.
    - **`맨 앞의 요소`** 또는 **`중간 요소`** 를 제거 시 배열의 인덱스를 찾는데 **`O(1)` +** 데이터를 이동하는데 **`O(n)`** 즉, **`O(n)`** 이 소요된다.

### ArrayList - 데이터 검색

**indexOf(E e)**

```java
public int indexOf(E o) {
    for (int i = 0; i < size; i++) {
        if (o.equals(elementData[i])) {
            return i;
        }
    } return -1;
}
```

- 리스트를 돌면서 주어진 데이터와 같은 데이터가 있는 경우, 해당 인덱스의 위치를 반환한다.
- 데이터가 없으면 **`-1`** 를 반환한다.
- 리스트를 순차탐색 하기 때문에 **`O(n)`** 이 소요된다.

### ArrayList - 데이터 변경

**set(index, element)**

```java
public E set(int index, E element) {
    E oldValue = get(index);
    elementData[index] = element;
    return oldValue;
}
```

- **`index`** 위치에 있는 요소를 **`element`** 로 변경한다.
- 구조를 설명하는데 초점이 맞춰져 있기 때문에 예외 관련한 부분은 따로 설정되어 있진 않다.

### ArrayList 단점

- **메모리 낭비의 가능성**
    - **`ArrayList`** 는 내부적으로 배열을 사용하므로, 크기를 미리 알지 못하면 필요 이상으로 메모리가 낭비된다.
    - 사용되지 않는 배열의 뒷부분이 메모리 낭비를 초래한다.
- **중간 데이터 삽입/삭제 시 비효율성**
    - 중간에 데이터를 삽입하거나 삭제하면 이후 데이터들을 한 칸씩 이동해야 한다.
    - 최악의 경우 **`O(n)`** 의 시간복잡도를 가진다.
    - 데이터 개수가 많을수록 성능이 저하된다.
- **배열 크기 조정 시 성능 저하**
    - **`ArrayList`** 의 크기를 초과하여 데이터를 추가하면, 새로운 배열을 생성하고 기존 데이터를 복사해야 한다.
    - 이 과정은 **`O(n)`** 시간이 걸리며, 큰 데이터를 다룰 경우 성능이 저하된다.
- 이러한 단점을 해결하기 위해 **`LinkedList`** 를 사용할 수 있다.


  
<br>
<br>
<br>

<blockquote>김영한의 실전 자바 중급 2편을 참고하였습니다</blockquote>










  

  


