# 람다식(Lambda)과 메서드 참조(Method Reference)

Java 8에서 도입된 기능으로, 함수형 프로그래밍 스타일을 지원하며 코드를 간결하게 만듭니다.

---

## 1. 람다식 (Lambda Expression)
메서드를 하나의 식(Expression)으로 표현한 익명 함수입니다.

### 1) 특징
- 메서드 이름과 반환 타입이 없어지므로 **익명 함수**라고도 부릅니다.
- **함수형 인터페이스**(@FunctionalInterface)를 구현할 때 사용합니다.
- 코드가 간결해지고 가독성이 높아집니다.

### 2) 문법 규칙
- `(매개변수) -> { 실행문; }`
- 매개변수가 하나라면 괄호 `()` 생략 가능.
- 실행문이 하나라면 중괄호 `{}`와 세미콜론 `;` 생략 가능.
- 실행문이 `return`문만 있는 경우 `return` 키워드 생략 권장.

---

## 2. 메서드 참조 (Method Reference)
람다식이 단순히 하나의 메서드만을 호출할 때, 이를 더 간략하게 표현하는 방법입니다.

### 1) 문법
- `클래스명::메서드명` 또는 `참조변수::메서드명`

### 2) 4가지 유형

| 유형 | 람다 표현식 | 메서드 참조 |
| :--- | :--- | :--- |
| **정적(Static) 메서드 참조** | `(x) -> ClassName.method(x)` | `ClassName::method` |
| **특정 객체의 인스턴스 메서드 참조** | `(x) -> obj.method(x)` | `obj::method` |
| **임의 객체의 인스턴스 메서드 참조** | `(obj, x) -> obj.method(x)` | `ClassName::method` |
| **생성자 참조** | `(x) -> new ClassName(x)` | `ClassName::new` |

---

## 3. 예제 비교

### 람다식 vs 메서드 참조
```java
// 람다식
Function<String, Integer> f1 = (String s) -> Integer.parseInt(s);

// 메서드 참조 (정적 메서드)
Function<String, Integer> f2 = Integer::parseInt;
```

### 리스트 출력 예제
```java
List<String> names = Arrays.asList("Tom", "Jerry", "Mickey");

// 람다식
names.forEach(name -> System.out.println(name));

// 메서드 참조 (인스턴스 메서드)
names.forEach(System.out::println);
```

---

## 4. 함수형 인터페이스 (Functional Interface)
람다식을 사용하기 위한 타겟 타입입니다.

- **@FunctionalInterface**: 추상 메서드가 단 하나만 존재하는 인터페이스임을 보장하는 어노테이션.
- **표준 함수형 인터페이스**:
    - `Runnable`: 매개변수 X, 반환 X (`void run()`)
    - `Supplier<T>`: 매개변수 X, 반환 O (`T get()`)
    - `Consumer<T>`: 매개변수 O, 반환 X (`void accept(T t)`)
    - `Function<T, R>`: 매개변수 O, 반환 O (`R apply(T t)`)
    - `Predicate<T>`: 매개변수 O, 반환 Boolean (`boolean test(T t)`)
