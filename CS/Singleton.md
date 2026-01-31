# 싱글톤 패턴 (Singleton Pattern)

싱글톤 패턴은 디자인 패턴 중 하나로, 특정 클래스의 **인스턴스가 오직 하나만 생성**되도록 보장하고, 어디서든 그 인스턴스에 접근할 수 있도록 하는 패턴입니다.

---

## 1. 사용 이유 (장점)
- **메모리 효율**: 인스턴스를 한 번만 생성하므로 메모리 낭비를 방지할 수 있습니다.
- **데이터 공유**: 전역 인스턴스이기 때문에 다른 클래스들이 데이터를 공유하기 쉽습니다.
- **공통 자원 관리**: 데이터베이스 커넥션 풀, 로그 기록기, 설정 정보 관리 등 공통 자원을 관리할 때 유용합니다.

## 2. 단점 및 주의사항
- **의존성 증가**: 싱글톤 인스턴스가 너무 많은 일을 하거나 많은 데이터를 공유하면 클래스 간 결합도가 높아집니다 (SOLID 원칙 중 DIP 위반 가능성).
- **테스트 어려움**: 전역 상태를 가지므로 단위 테스트 시 독립적인 테스트가 어려울 수 있습니다.
- **멀티스레드 환경**: 여러 스레드에서 동시에 접근할 경우 인스턴스가 여러 개 생길 수 있는 위험이 있어 동기화 처리가 필요합니다.

---

## 3. Java 구현 방식

### 1) Eager Initialization (이른 초기화)
클래스 로딩 시점에 인스턴스를 생성하는 방식입니다. 간단하지만 사용하지 않아도 메모리를 점유한다는 단점이 있습니다.
```java
public class Singleton {
    private static final Singleton instance = new Singleton();

    private Singleton() {} // 생성자를 private으로 제한

    public static Singleton getInstance() {
        return instance;
    }
}
```

### 2) Lazy Initialization with Double-Checked Locking
인스턴스가 필요한 시점에 생성하며, 멀티스레드 환경에서도 안전하게 설계된 방식입니다.
```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

### 3) Bill Pugh Singleton Implementation (권장)
Inner Static Class를 사용하는 방식으로, 가장 많이 사용되고 권장되는 방식 중 하나입니다. 지연 로딩(Lazy Loading)과 스레드 안전성(Thread-Safe)을 모두 보장합니다.
```java
public class Singleton {
    private Singleton() {}

    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

---

## 4. 정리
- **단 하나의 인스턴스**만 유지해야 할 때 사용합니다.
- 현대적인 프레임워크(예: Spring)에서는 컨테이너가 빈(Bean)을 기본적으로 싱글톤으로 관리해주므로, 직접 구현하기보다는 프레임워크의 기능을 활용하는 경우가 많습니다.
