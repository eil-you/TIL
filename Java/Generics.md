# Java: 제네릭 (Generic) 이해하기

제네릭(Generic)은 자바에서 **타입 안전성(Type Safety)**을 보장하기 위해 도입된 매우 중요한 개념입니다. 클래스나 메서드에서 사용할 내부 데이터 타입을 컴파일 시점에 미리 지정하는 기법입니다.

---

## 1. 제네릭이 왜 필요한가요?

제네릭이 없던 시절에는 여러 타입을 담기 위해 `Object` 타입을 사용했습니다. 이 경우 두 가지 문제점이 발생합니다.

1.  **번거로운 형변환**: 값을 꺼낼 때마다 실제 타입으로 강제 형변환을 해야 합니다.
2.  **런타임 에러 위험**: 잘못된 타입으로 형변환을 시도하면 `ClassCastException`이 발생하여 프로그램이 멈출 수 있습니다.

**제네릭을 사용하면?** 컴파일러가 타입을 체크해주어 잘못된 타입이 들어오는 것을 미리 막아줍니다.

---

## 2. 기본 문법

클래스나 메서드 이름 옆에 `< >` 기호를 붙여 사용하며, 관례적으로 아래와 같은 영문 대문자를 사용합니다.

- `<T>` : Type (대체로 사용)
- `<E>` : Element (주로 컬렉션의 요소)
- `<K>` : Key
- `<V>` : Value

### 클래스에서의 사용 예시
```java
// 제네릭 클래스 선언
public class Box<T> {
    private T content;

    public void set(T content) { this.content = content; }
    public T get() { return content; }
}

// 사용 시점에 타입 결정
Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String s = stringBox.get(); // 형변환 필요 없음!
```

---

## 3. 제네릭의 제한 (Wildcards)

가끔은 "어떤 타입이든 다 좋다"거나 "특정 클래스의 자식들만 받겠다"는 제한이 필요할 때 **와일드카드**를 사용합니다.

- `<?>`: 모든 타입 가능 (Unbounded Wildcard)
- `<? extends T>`: T 또는 T의 자식 타입만 가능 (Upper Bounded)
- `<? super T>`: T 또는 T의 부모 타입만 가능 (Lower Bounded)

---

## 4. 왜 기본 타입(int 등)은 사용하지 못하나요?

자바의 제네릭은 **타입 소거(Type Erasure)** 방식을 사용합니다. 
컴파일 시점에는 타입을 체크하지만, 실행 시점에는 제네릭 타입 정보가 사라지고 `Object`로 변환됩니다. 

`int`, `double` 같은 기본 타입은 `Object`의 자식 객체가 아니기 때문에 `Object`로 바뀔 수 없습니다. 따라서 반드시 **참조 타입(Wrapper Class)**인 `Integer`, `Double` 등을 사용해야 합니다.

---

### 요약
- **제네릭** = "타입을 파라미터화" 하는 것
- **장점**: 컴파일 시 타입 체크, 형변환 생략, 코드 재사용성 증가
- **주의**: 기본 타입(`int`)은 쓸 수 없으므로 래퍼 클래스(`Integer`)를 써야 함
