# 자바 기초: Stream API - filter()

자바 8에서 도입된 Stream API는 컬렉션 데이터를 처리하는 강력한 도구입니다. 그중 가장 핵심적인 중간 연산인 `filter()`에 대해 정리합니다.

---

## 1. filter()란?

`filter()`는 스트림 내의 요소들을 하나씩 검사하여 **특정 조건에 맞는 요소들만 골라내는 역할**을 합니다.

- **인자**: `Predicate<T>` (T타입 객체를 받아 boolean을 반환하는 함수형 인터페이스)
- **결과**: 조건이 `true`인 요소들로만 구성된 새로운 스트림을 반환합니다.

---

## 2. 기본 사용법

### 1) 리스트에서 조건 필터링
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Apricot");

// 'A'로 시작하는 과일만 골라내기
List<String> result = fruits.stream()
    .filter(s -> s.startsWith("A"))
    .collect(Collectors.toList());

System.out.println(result); // [Apple, Apricot]
```

### 2) 숫자형 데이터 필터링
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// 5보다 크고 짝수인 숫자만 골라내기
List<Integer> result = numbers.stream()
    .filter(n -> n > 5)
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());

System.out.println(result); // [6, 8, 10]
```

---

## 3. 배열에서 Stream 사용하기

배열은 컬렉션(List 등)처럼 `.stream()` 메서드를 직접 호출할 수 없지만, `Arrays.stream()` 또는 `Stream.of()`를 사용하여 스트림을 생성할 수 있습니다.

### 1) 방법
```java
String[] languages = {"Java", "Python", "C++", "JavaScript"};

// Arrays.stream() 사용
long count = Arrays.stream(languages)
    .filter(lang -> lang.startsWith("Java"))
    .count();

// Stream.of() 사용
Stream.of(languages).filter(s -> s.length() > 3).forEach(System.out::println);
```

### 2) 기본 타입 배열 스트림 (IntStream, LongStream 등)
기본 타입 배열(`int[]`, `long[]` 등)을 스트림으로 만들 경우, 래퍼 클래스(`Integer`)로 변환(Boxing)하지 않고 **기본 타입 그대로** 처리하는 특화된 스트림이 생성됩니다.

- **종류**: `IntStream`, `LongStream`, `DoubleStream`
- **장점**: 
    - **성능**: 오토박싱/언박싱이 발생하지 않아 메모리와 속도 면에서 효율적입니다.
    - **편의성**: `.sum()`, `.average()`, `.min()`, `.max()` 같은 숫자 전용 메서드를 바로 사용할 수 있습니다.

```java
int[] nums = {1, 2, 3, 4, 5};
int sum = Arrays.stream(nums)
                .filter(n -> n % 2 != 0)
                .sum(); // 홀수의 합계
```

### 3) 2차원 배열 처리 (flatMap)
`int[][]`와 같은 2차원 배열은 **"배열의 배열"** 구조입니다. 이를 하나의 스트림으로 합쳐서 처리하려면 `flatMap` 계열의 메서드가 필요합니다.

- **flatMapToInt**: `Stream<int[]>`를 각 요소가 `int`인 단일 `IntStream`으로 평평하게 펼쳐줍니다.

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5},
    {6, 7, 8}
};

// 모든 요소를 합치기
int totalSum = Arrays.stream(matrix)             // Stream<int[]> 반환
                     .flatMapToInt(Arrays::stream) // 각 행을 IntStream으로 펼침
                     .sum();

// 5보다 큰 요소만 필터링하여 리스트로 만들기
List<Integer> filtered = Arrays.stream(matrix)
                               .flatMapToInt(Arrays::stream)
                               .filter(n -> n > 5)
                               .boxed()
                               .collect(Collectors.toList());
```

---

## 4. 핵심 동작 원리 및 특징 (면접 포인트)

### 1) 중간 연산 (Intermediate Operation)
스트림 연산은 **중간 연산**과 **최종 연산**으로 나뉩니다. `filter()`는 중간 연산에 속하며, 연산 결과로 다시 스트림을 반환하므로 여러 번 연결(Chaining)할 수 있습니다.

### 2) 지연 연산 (Lazy Evaluation)
가장 중요한 포인트입니다. `filter()` 같은 중간 연산은 최종 연산(예: `collect`, `forEach`)이 호출되기 전까지는 실제로 실행되지 않습니다. 
- **장점**: 불필요한 계산을 피할 수 있어 성능 최적화에 유리합니다.

### 3) 선언적 프로그래밍
"어떻게(How) 반복문을 돌릴까"가 아니라 **"무엇을(What) 필터링할까"**에 집중합니다. `if`문과 `for`문 중첩을 제거하여 가독성을 높여줍니다.

---

## 5. filter() vs map() 비교

| 구분 | filter() | map() |
| :--- | :--- | :--- |
| **목적** | 요소를 **걸러냄** (Filtering) | 요소를 **변환함** (Transforming) |
| **결과 개수** | 원래보다 줄어들거나 같음 | 원래와 동일함 |
| **반환 타입** | 스트림의 요소 타입이 변하지 않음 | 스트림의 요소 타입이 변할 수 있음 |

---

## 6. 면접 준비 - 심화 질문

### Q. 기본 타입 배열(int[])과 리스트(List<Integer>)의 스트림 처리 차이는?
**A.** `int[]`는 `IntStream`을 사용하므로 데이터가 기본 타입(`int`) 그대로 처리됩니다. 반면 `List<Integer>`는 `Stream<Integer>`를 사용하므로 내부적으로 객체 타입인 `Integer`를 다룹니다. 성능 측면에서는 **오토박싱 비용이 없는 `IntStream`이 훨씬 유리**하며, 합계(`sum()`)나 평균(`average()`) 같은 연산도 직접 지원하여 편리합니다.

### Q. Stream API의 지연 연산(Lazy Evaluation)이란?
**A.** 중간 연산인 `filter()` 등은 최종 연산이 수행되기 전까지는 데이터를 처리하지 않고 대기합니다. 이를 통해 결과가 필요한 시점에 딱 필요한 만큼만 연산을 수행함으로써 메모리와 CPU 자원을 효율적으로 사용할 수 있습니다.

### Q. map()과 flatMap()의 차이는 무엇인가요?
**A.** `map()`은 요소 하나를 다른 요소 하나로 **1:1 변환**할 때 사용합니다. 반면 `flatMap()`은 요소 하나를 여러 개의 요소로 펼친 뒤(1:N), 이를 **하나의 스트림으로 합쳐서(Flattening)** 반환할 때 사용합니다. 위 예시처럼 2차원 배열을 1차원 구조로 만들어 처리할 때 필수적입니다.

---

### 🎙️ 기술 면접 예상 질문

**Q. Stream API의 지연 연산(Lazy Evaluation)에 대해 설명하고, filter()에서 이것이 어떻게 이득을 주는지 말해보세요.**
> **A.** 중간 연산인 `filter()`는 최종 연산이 수행되기 전까지는 데이터를 처리하지 않고 대기합니다. 이를 통해 스트림의 모든 요소를 미리 다 계산하는 것이 아니라, 결과가 필요한 시점에 딱 필요한 만큼만 연산을 수행함으로써 메모리와 CPU 자원을 효율적으로 사용할 수 있습니다.


1. .count()를 사용한 예시는 반환값이 long이므로 int로 변환해야 합니다.
```java 
int answer = (int)Arrays.stream(array)
                    .filter(a -> a == n)
                    .count();
```

3. **IntStream.rangeClosed()**를 활용한 특정 범위 숫자 생성
- `for`문 없이 특정 범위의 숫자를 생성하여 스트림으로 처리할 때 매우 유용합니다.
- `range(start, end)`: end 미포함 / `rangeClosed(start, end)`: end 포함

```java
// 0부터 n까지의 숫자 중 홀수만 골라 배열로 변환
int[] oddNumbers = IntStream.rangeClosed(0, n)
                            .filter(value -> value % 2 == 1)
                            .toArray();
```

4. **평균 구하기 (average)**
- `IntStream`, `DoubleStream` 등 기초 타입 스트림은 평균을 구해주는 `.average()` 메서드를 제공합니다.
- 반환값이 `OptionalDouble`이므로 `.getAsDouble()` 등으로 값을 꺼내서 사용합니다.

```java
public double solution(int[] numbers) {
    return Arrays.stream(numbers).average().getAsDouble();
}
```

5. **배열 뒤집기 (Reverse)**
- 자바 스트림에는 직접적인 `.reverse()`가 없으므로, 박싱(`boxed`) 후 리스트로 변환하여 `Collections.reverse()`를 사용하거나, 다시 `mapToInt`를 통해 배열로 변환합니다.

```java
// int[] -> List<Integer> -> 뒤집기 -> int[]
List<Integer> list = Arrays.stream(numList).boxed().collect(Collectors.toList());
Collections.reverse(list);
return list.stream().mapToInt(Integer::intValue).toArray();
```

6. **특정 규칙으로 문자열 변환 (외계행성의 나이)**
- 숫자의 각 자릿수를 순회하며 아스키(ASCII) 값을 조작해 문자로 변환한 뒤 다시 하나로 합칠 때 유용합니다.
- `chars()`는 문자의 아스키 값을 `IntStream`으로 반환하며, `mapToObj`와 `Collectors.joining()`을 조합해 최종 문자열을 만듭니다.

```java
public String solution(int age) {
    // 예: age가 23이면 "2", "3"의 아스키 값에 조작을 가해 "cd"로 변환
    return String.valueOf(age).chars()
                 .mapToObj(operand -> String.valueOf((char) (49 + operand)))
                 .collect(Collectors.joining());
}
```

7. **요소의 순위(Rank) 구하기**
- 배열의 각 요소가 전체에서 몇 번째로 큰지(또는 작은지) 순위를 매길 때 사용합니다.
- 중첩 스트림을 사용하면 한 줄로 구현 가능하지만, 성능상의 주의가 필요합니다.

```java
public int[] solution(int[] e) {
    return Arrays.stream(e)
            .map(i -> Arrays.stream(e)
                            .boxed()
                            .sorted(Comparator.reverseOrder())
                            .collect(Collectors.toList())
                            .indexOf(i) + 1)
            .toArray();
}
```
> **⚠️ 주의 (성능)**: 위 코드는 바깥쪽 루프($N$) 안에서 매번 정렬($N \log N$)과 검색($N$)을 수행하므로 시간 복잡도가 **$O(N^2 \log N)$**입니다. 데이터가 많을 경우 매우 느려질 수 있습니다.

**[최적화 방식 - $O(N \log N)$]**
```java
public int[] solution(int[] e) {
    // 정렬을 미리 한 번만 수행
    List<Integer> sortedList = Arrays.stream(e)
            .boxed()
            .sorted(Comparator.reverseOrder())
            .collect(Collectors.toList());

    return Arrays.stream(e)
            .map(i -> sortedList.indexOf(i) + 1)
            .toArray();
}
```

---

---

---

---

2. **참조 타입 스트림을 기본 타입 배열로 변환**
- `Stream<Integer>`를 `int[]`로 변환하려면 `.mapToInt(Integer::intValue).toArray()` 또는 `.toArray(Integer[]::new)`를 사용해야 합니다.

```java
// 예시: 숫자를 2배로 만들고 배열로 변환
int[] doubled = Arrays.stream(numbers).map(i -> i * 2).toArray(); 
// (주의: 위 예시는 IntStream이므로 바로 toArray() 가능, 
// 만약 boxed() 상태라면 mapToInt 과정이 필요함)
```

---

## 7. 실전 사례 분석: 최빈값 구하기 (Finding the Mode)

알고리즘 문제에서 자주 사용되는 '최빈값 구하기' 로직을 스트림으로 구현할 때, 가독성과 효율성을 모두 챙기는 방법을 분석합니다.

### 1) 복잡한 한 줄 코드 (Original)
```java
public int solution(int[] array) {
    List<Map.Entry<Integer, List<Integer>>> list = new ArrayList<>(
        Arrays.stream(array).boxed()
        .collect(Collectors.groupingBy(o -> o)).entrySet()
    ).stream()
    .sorted((t0, t1) -> Integer.compare(t1.getValue().size(), t0.getValue().size()))
    .collect(Collectors.toList());

    return list.size() > 1 && list.get(0).getValue().size() - list.get(1).getValue().size() == 0 ? -1 : list.get(0).getKey();
}
```

### 2) 3단계 분석 및 쪼개기
위 복잡한 로직은 크게 3가지 과정으로 나뉩니다.

1.  **그룹화(Grouping)**: `groupingBy(o -> o)`를 사용하여 같은 숫자끼리 리스트로 묶습니다.
2.  **정렬(Sorting)**: `sorted()`를 사용하여 리스트의 크기(빈도 수) 기준 내림차순 정렬합니다.
3.  **결과 도출(Logic)**: 1순위와 2순위의 빈도수가 같다면 -1, 아니라면 1순위의 키 값을 반환합니다.

### 3) 메모리 최적화 코드 (Recommended)
기존 코드는 `List<Integer>`를 만들어 모든 데이터를 담아두기 때문에 메모리 낭비가 큽니다. 단순히 **개수(`counting()`)**만 세는 것이 훨씬 효율적입니다.

```java
public int solution(int[] array) {
    // 1. 빈도수 계산 (숫자 -> 개수) - Collectors.counting() 활용
    Map<Integer, Long> counts = Arrays.stream(array)
            .boxed()
            .collect(Collectors.groupingBy(i -> i, Collectors.counting()));

    // 2. 빈도수 기준 내림차순 정렬
    List<Map.Entry<Integer, Long>> sortedList = counts.entrySet().stream()
            .sorted(Map.Entry.<Integer, Long>comparingByValue().reversed())
            .collect(Collectors.toList());

    // 3. 최빈값 중복 체크 및 결과 반환
    if (sortedList.size() > 1 && sortedList.get(0).getValue().equals(sortedList.get(1).getValue())) {
        return -1;
    }
    
    return sortedList.get(0).getKey();
}
```

### 💡 면접 포인트: 왜 counting()이 더 좋은가?
- **메모리 효율**: `List<Integer>`는 실제 데이터 객체들을 모두 힙 메모리에 유지해야 하지만, `counting()`은 내부적으로 숫자를 카운트하는 `long` 변수 하나만 사용하므로 메모리 사용량이 훨씬 적습니다.
- **가독성**: `Map.Entry.comparingByValue()`를 사용하면 이전보다 훨씬 직관적으로 정렬 로직을 작성할 수 있습니다.
