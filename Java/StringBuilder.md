# StringBuilder 작동 원리

## 1. 개요
자바에서 문자열을 처리할 때 `String`은 **불변(Immutable)** 객체입니다. 따라서 문자열을 수정하거나 합칠 때마다 새로운 객체가 생성되어 메모리 낭비와 성능 저하를 유발합니다. 이를 해결하기 위해 **가변(Mutable)** 성격을 가진 `StringBuilder`를 사용합니다.

## 2. 주요 특징
- **가변성 (Mutability)**: 동일한 메모리 버퍼 안에서 데이터를 직접 수정합니다.
- **비동기화 (Non-synchronized)**: `StringBuffer`와 달리 멀티스레드 동기화를 보장하지 않아 단일 스레드에서 속도가 더 빠릅니다.
- **동적 배열 기반**: 내부적으로 `char[]` (Java 9 이후 `byte[]`) 배열을 사용하여 문자를 저장합니다.

## 3. 핵심 작동 원리

### 3.1 내부 버퍼 (Internal Buffer)
`StringBuilder`는 내부에 다음과 같은 가변 배열 필드를 가집니다.
```java
abstract class AbstractStringBuilder {
    byte[] value; // Java 9 이후 byte[] 사용 (Latin1/UTF16 최적화)
    int count;    // 현재 저장된 문자의 개수
    // ...
}
```

### 3.2 append() 과정과 동적 확장 (Resizing)
문자열을 추가할 때 다음 과정을 거칩니다.

1.  **용량 확인**: 현재 버퍼(`value.length`)에 추가하려는 문자열을 담을 공간이 충분한지 확인합니다.
2.  **공간 확보 (Ensure Capacity)**: 공간이 부족하면 기존 크기보다 더 큰 새로운 배열을 만듭니다.
    - 보통 **(기존 용량 * 2) + 2**의 크기로 확장합니다.
3.  **데이터 복사**: `System.arraycopy()`를 사용하여 기존 배열의 내용을 새 배열로 복사합니다.
4.  **문자 추가**: 새 배열에 추가하고자 하는 데이터를 채웁니다.

### 3.3 성능 포인트
- **O(1) 추가**: 대부분의 경우 `append()`는 배열 끝에 값을 추가하므로 시간 복잡도는 O(1)입니다.
- **O(N) 확장**: 용량이 꽉 차서 배열을 새로 만들고 복사할 때만 O(N)이 소요됩니다.

## 4. String vs StringBuilder vs StringBuffer

| 특성 | String | StringBuilder | StringBuffer |
| :--- | :--- | :--- | :--- |
| 가변성 | 불변 (Immutable) | **가변 (Mutable)** | **가변 (Mutable)** |
| 동기화 | 해당 없음 | **비보장 (Fast)** | **보장 (Thread-safe)** |
| 메모리 | 합칠 때마다 새 객체 | 버퍼 재사용 | 버퍼 재사용 |
| 추천 상황 | 변하지 않는 문자열 | **단일 스레드 반복 수정** | **멀티 스레드 반복 수정** |

## 5. 사용 예시
```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World"); // 새로운 객체 생성 없이 내부 배열 수정
sb.insert(5, " Java");
System.out.println(sb.toString()); // Hello Java World
```

---
> **Tip**: 처음부터 들어갈 데이터의 양을 대략 알고 있다면 `new StringBuilder(512)`와 같이 **초기 용량(Capacity)**을 지정해 주는 것이 배열 재할당 횟수를 줄여 성능 최적화에 도움이 됩니다.
