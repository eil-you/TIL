# Spring Bean 이란?

Spring 프레임워크에서 **Bean(빈)**은 Spring의 IoC(Inversion of Control) 컨테이너에 의해 관리되는 **자바 객체**를 의미합니다.

---

## 1. 핵심 개념
- **IoC 컨테이너가 관리**: 개발자가 `new` 연산자로 직접 객체를 생성하는 것이 아니라, Spring 컨테이너가 객체의 생성, 초기화, 서비스(사용), 소멸 등을 관리합니다.
- **제어의 역전(IoC)**: 객체의 생명주기 관리 권한이 개발자에서 컨테이너로 넘어간 것을 의미합니다.

## 2. Bean의 특징
1. **싱글톤(Singleton)이 기본 스코프**: 별도의 설정이 없으면 컨테이너 내에서 단 하나의 객체만 생성되어 공유됩니다.
2. **의존성 주입(DI)**: 필요한 Bean들끼리 서로 의존성을 컨테이너가 자동으로 연결해줍니다.

## 3. Bean 등록 방법

### 1) 컴포넌트 스캔 (Component Scan)
클래스 위에 어노테이션을 붙여 자동으로 빈으로 등록하는 방식입니다.
- `@Component`: 일반적인 빈으로 등록
- `@Service`: 비즈니스 로직 서비스 레이어용
- `@Repository`: DB 접근 레이어용
- `@Controller`: 웹 요청 처리 레이어용

### 2) 자바 설정 클래스 (Java Configuration)
`@Configuration` 클래스 내에서 직접 메서드를 통해 등록하는 방식입니다.
```java
@Configuration
public class AppConfig {
    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

---

## 4. 왜 Bean을 사용하는가? (장점)
- **객체 관리 용이**: 복잡한 의존 관계를 직접 코딩할 필요 없이 설정만으로 관리 가능합니다.
- **테스트 용이성**: 가짜 객체(Mock)를 주입하기 쉬워져 단위 테스트가 간편해집니다.
- **라이프사이클 콜백**: 객체가 생성되거나 사라질 때 특정 로직을 실행할 수 있습니다 (`@PostConstruct`, `@PreDestroy`).

## 5. 요약
자바 객체가 Spring 컨테이너 안에 들어가서 **프레임워크의 관리를 받기 시작하면** 그것을 **Bean**이라고 부릅니다.
