# 메모리 누수(Memory Leak)와 해결 방법

자바는 가비지 컬렉터(GC)가 자동으로 메모리를 관리해주지만, **실제로는 사용하지 않는데 참조가 살아있어서 GC가 수거하지 못하는 경우** 메모리 누수가 발생합니다.

---

## 1. 메모리 누수가 발생하는 주요 경우

### 1) Static 변수의 남용
`static` 변수는 클래스가 로드될 때 생성되어 프로그램이 종료될 때까지 메모리에 남아 있습니다. 
- **문제**: 대량의 데이터를 담은 컬렉션(List, Map 등)을 `static`으로 선언하고 요소를 삭제하지 않으면 계속 메모리를 점유합니다.

### 2) 닫히지 않은 리소스 (Resource Leaks)
- **문제**: DB 연결, Network 소켓, 파일 입출력 스트림 등을 사용한 후 `close()`를 호출하지 않으면 메모리가 반환되지 않습니다.

### 3) 컬렉션에 잘못된 참조 유지
- **문제**: 캐시 목적으로 사용한 `HashMap` 등에 객체를 넣고, 더 이상 필요 없음에도 명시적으로 제거(`remove`)하지 않으면 계속 남아 있습니다.

### 4) 내부 클래스에서의 외부 클래스 참조
- **문제**: 비정적 내부 클래스(Non-static inner class)는 외부 클래스의 참조를 가집니다. 내부 객체가 살아있는 한 외부 클래스 객체도 GC가 수거하지 못합니다.

---

## 2. 메모리 누수 방지 및 해결책

### 1) 리소스 관리는 `try-with-resources` 사용
자바 7부터 도입된 문법으로, 리소스 사용 후 자동으로 `close()`를 호출해줍니다.
```java
try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
    // 비즈니스 로직
} catch (IOException e) {
    e.printStackTrace();
} // 여기서 자동으로 close() 호출됨
```

### 2) WeakReference (약한 참조) 활용
캐시를 구현할 때는 `WeakHashMap`을 사용하는 것이 좋습니다. 
- Key에 대한 참조가 사라지면 GC가 자동으로 해당 엔트리를 메모리에서 삭제합니다.

### 3) Static 컬렉션 지양 및 관리
- 가급적 클래스 변수(`static`)보다는 인스턴스 변수를 활용합니다.
- 꼭 `static`을 써야 한다면 반드시 데이터를 정기적으로 비워주는(`clear`) 로직을 포함해야 합니다.

### 4) 내부 클래스는 `static`으로 선언
외부 클래스의 멤버를 참조할 필요가 없다면 내부 클래스를 `static`으로 선언하세요. 외부 클래스에 대한 숨은 참조가 사라져 메모리 누수를 막을 수 있습니다.

### 5) 도구 활용 (Monitoring)
- **VisualVM**, **JProfiler**, **Eclipse Mat**와 같은 프로파일링 도구를 사용하여 어떤 객체가 메모리를 많이 점유하고 있고, 왜 GC가 안 되는지 분석합니다.

---

## 3. 자바의 참조 종류와 메모리 관리

자바에는 가비지 컬렉터와 상호작용하는 4가지 참조 방식이 있습니다. 특히 `SoftReference`와 `WeakReference`는 메모리 효율적인 캐시를 만들 때 핵심적입니다.

### 1) 강한 참조 (Strong Reference)
우리가 흔히 쓰는 방식입니다.
```java
Object obj = new Object();
```
- **GC 동작**: 참조가 조금이라도 살아있다면 **절대 수거하지 않습니다.** 메모리가 부족하면 `OutOfMemoryError`를 던질지언정 수거 안 합니다.

### 2) 부드러운 참조 (Soft Reference)
```java
SoftReference<Object> soft = new SoftReference<>(new Object());
```
- **GC 동작**: 메모리가 충분할 때는 수거하지 않다가, **메모리가 정말 부족해져서 OutOfMemory가 날 것 같을 때**만 수거합니다.
- **용도**: 메모리 상태에 민감한 **캐시**를 구현할 때 적합합니다.

### 3) 약한 참조 (WeakReference)
```java
WeakReference<Object> weak = new WeakReference<>(new Object());
```
- **GC 동작**: **GC가 발생할 때마다 무조건 수거**해갑니다. 해당 객체를 가리키는 '강한 참조'가 없다면 바로 사라집니다.
- **용도**: `WeakHashMap`처럼 키값이 사라지면 밸류도 자동으로 정리되어야 하는 경우나, 수명이 짧은 객체 캐싱에 좋습니다.

### 4) 팬텀 참조 (Phantom Reference)
- 객체가 메모리에서 실제로 제거되기 직전에 어떤 작업을 수행해야 할 때 사용합니다. (가장 복잡하고 드물게 쓰임)

---

## 4. Soft vs Weak 어떤 상황에 써야 할까?

| 구분 | SoftReference | WeakReference |
| :--- | :--- | :--- |
| **수거 시점** | **메모리가 부족할 때만** 수거 | **GC가 돌 때마다** 수거 |
| **생존 가능성** | 상대적으로 높음 | 매우 낮음 |
| **추천 상황** | **대용량 캐시** (이미지 등) | **메타데이터/짧은 캐시** (WeakHashMap 등) |

- **이미지 뷰어 예시**:
  - 사용자에게 보여줄 이미지를 캐싱할 때, 일일이 지우기 귀찮지만 메모리는 아끼고 싶다면? -> **SoftReference** (메모리 꽉 차기 전까진 유지)
  - 특정 객체에 대한 부가 정보(설정값 등)를 보관하는데, 그 객체가 사라지면 정보도 바로 지워져야 한다면? -> **WeakReference**

---

### 💡 요약
- **Soft**: "메모리 부족하면 그때 지워줘" (캐시에 유리)
- **Weak**: "다음 청소 시간에 무조건 치워줘" (더 즉각적인 해제)

---

## 5. 심화: 내부 클래스의 외부 참조와 메모리 누수

"내부 클래스가 외부 클래스를 참조한다"는 말이 왜 위험한지 코드로 이해해 봅시다.

### 1) 위험한 코드 (비정적 내부 클래스)
```java
public class Outer {
    private byte[] heavyData = new byte[100 * 1024 * 1024]; // 100MB 데이터

    public class Inner {
        // 별다른 코드 없음
    }

    public Inner getInner() {
        return new Inner();
    }
}
```
위 코드에서 `Inner` 클래스는 `static`이 붙지 않았습니다. 자바 컴파일러는 이럴 때 `Inner` 객체 안에 **몰래 `Outer` 객체의 주소를 저장**해둡니다.

- 만약 다른 곳에서 `Inner inner = outer.getInner();`로 객체를 받아서 유지한다면?
- `inner`는 고작 몇 바이트 안 되지만, **숨겨진 참조 때문에 100MB짜리 `Outer` 객체도 메모리에서 못 나가게 꽉 붙잡고 있게 됩니다.**

### 2) 안전한 코드 (정적 내부 클래스)
```java
public class Outer {
    private byte[] heavyData = new byte[100 * 1024 * 1024];

    // static을 붙이면 외부 객체와 연결 고리가 끊깁니다.
    public static class Inner {
        // 이제 Outer를 붙잡고 있지 않습니다.
    }
}
```
`static`을 붙이면 내부 클래스는 더 이상 외부 객체의 주소를 갖지 않습니다. 이제 `Inner` 객체가 아무리 오래 살아있어도 `Outer` 객체는 필요 없어지는 순간 GC가 시원하게 수거해갈 수 있습니다.

---

### 💡 요약
- **비정적 내부 클래스**: "나는 엄마(외부 클래스) 없이는 못 살아!" (엄마를 메모리에 계속 붙잡아둠)
- **정적(static) 내부 클래스**: "나는 독립했어!" (서로 메모리에 영향을 주지 않음)

---

## 6. 캐시 관리와 WeakHashMap을 이용한 해결 프로세스

프로그램 성능을 위해 데이터를 메모리에 저장해두는 **캐시(Cache)**는 메모리 누수가 가장 빈번하게 발생하는 곳입니다. 일반적인 `HashMap`을 캐시로 사용할 때의 문제점과 `WeakHashMap`의 해결 방식을 알아봅시다.

### 1) 일반 HashMap의 문제점 (Strong Reference)
일반적인 `HashMap`은 Key와 Value에 대해 **강한 참조(Strong Reference)**를 유지합니다.
- 만약 캐시 안에 있는 특정 Key 객체가 프로그램의 다른 곳에서는 더 이상 사용되지 않더라도, `HashMap`이 그 Key를 꽉 붙잡고 있기 때문에 GC는 해당 객체를 절대 수거하지 않습니다.
- 결국 사용자가 명시적으로 `map.remove(key)`를 호출하지 않는 한, 메모리는 계속 쌓이게 됩니다.

### 2) WeakHashMap의 동작 원리
`WeakHashMap`은 Key에 대해 **약한 참조(Weak Reference)**를 사용합니다.
- **동작 프로세스**:
  1. `WeakHashMap`에 데이터(Key, Value)를 넣습니다.
  2. 어느 시점에 해당 **Key**를 참조하던 프로그램 내의 모든 '강한 참조'가 사라집니다. (예: 변수에 null 대입)
  3. **Garbage Collection(GC)**이 발생합니다.
  4. GC는 "이 Key는 이제 약한 참조밖에 없네?"라고 판단하고 메모리에서 회수합니다.
  5. `WeakHashMap`은 내부적으로 이를 감지하여 해당 Key와 연결된 **Value까지 자동으로 제거**합니다.

### 3) 실제 코드 예시
```java
public class CacheExample {
    public static void main(String[] args) {
        WeakHashMap<Integer, String> cache = new WeakHashMap<>();
        Integer key = new Integer(100); // 강한 참조 생성
        
        cache.put(key, "Very Heavy Data");
        System.out.println("캐시 크기 (GC 전): " + cache.size()); // 1

        key = null; // 강한 참조 해제! (이제 WeakHashMap의 약한 참조만 남음)
        
        System.gc(); // 가비지 컬렉션 유도
        
        // 잠시 후 확인하면 데이터가 자동으로 지워진 것을 볼 수 있음
        System.out.println("캐시 크기 (GC 후): " + cache.size()); // 0
    }
}
```

### 4) 캐시 전략 요약
- **데이터 유지가 중요하다면?**: `SoftReference` 기반의 캐시(메모리가 모자랄 때만 삭제).
- **자동 정리가 중요하다면?**: `WeakHashMap` (참조가 끊기면 즉시 삭제).
- **현재 실무 트렌드**: 직접 구현하기보다는 `Google Guava`나 `Caffeine` 같은 전문 라이브러리를 사용하여 더 세밀한 캐시 정책(시간 기반 만료 등)을 적용합니다.
