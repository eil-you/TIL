# 트랜잭션 (Transaction) 및 @Transactional 정리

트랜잭션은 데이터베이스의 상태를 변화시키기 위해 수행하는 **하나의 논리적 작업 단위**입니다. 여러 개의 작업을 하나로 묶어, 모두 성공하거나 모두 실패(All or Nothing)하도록 보장합니다.

---

## 1. 트랜잭션의 특징 (ACID)

트랜잭션이 안전하게 수행되려면 다음 4가지 조건을 만족해야 합니다.

1. **Atomicity (원자성)**
   - 트랜잭션 내의 모든 작업은 반드시 전체가 성공하거나, 전체가 실패해야 합니다. 하나라도 오류가 나면 전체를 취소(Rollback)합니다.
2. **Consistency (일관성)**
   - 트랜잭션 완료 후에도 DB는 정의된 규칙(제약 조건 등)을 항상 만족해야 합니다.
3. **Isolation (독립성/격리성)**
   - 둘 이상의 트랜잭션이 동시에 실행될 때, 서로의 연산에 끼어들 수 없습니다.
4. **Durability (지속성)**
   - 성공적으로 완료된 트랜잭션의 결과는 영구적으로 기록되어야 합니다. (시스템 장애가 나더라도 보존)

---

## 2. Spring의 @Transactional 어노테이션

Spring 프레임워크에서는 **선언적 트랜잭션** 방식을 사용하며, `@Transactional` 어노테이션 하나로 복잡한 트랜잭션 로직을 처리할 수 있게 해줍니다.

### 주요 역할
- **AOP(Aspect Oriented Programming)**를 통해 대상 메서드 호출 전후로 트랜잭션 시작(`begin`)과 완료(`commit` 또는 `rollback`) 처리를 자동으로 주입합니다.

### 주요 설정 옵션

1. **propagation (전파 설정)**
   - `REQUIRED` (기존 트랜잭션 있으면 참여, 없으면 새로 생성 - **기본값**)
   - `REQUIRES_NEW` (무조건 새로운 트랜잭션 생성)
2. **isolation (격리 수준)**
   - `READ_COMMITTED` (다른 트랜잭션이 커밋한 내용만 읽음 - **대부분 DB 기본값**)
   - 그 외 `READ_UNCOMMITTED`, `REPEATABLE_READ`, `SERIALIZABLE`
3. **readOnly (읽기 전용)**
   - `true` 설정 시 성능 최적화를 하며, 데이터의 변경(CUD)을 막습니다. (주로 조회 전용 메서드에 사용)
4. **rollbackFor (롤백 기준)**
   - 특정 예외가 발생했을 때 롤백할지 설정합니다.
   - **주의**: 기본적으로는 `RuntimeException`과 `Error`에서만 롤백되며, `Checked Exception`은 롤백되지 않으므로 설정이 필요할 수 있습니다.

---

## 3. @Transactional 사용 시 주의사항 (중요!)

1. **Proxy 내부 호출 문제**: 동일한 클래스 내에서 `@Transactional`이 붙지 않은 메서드가 붙은 메서드를 호출하면 트랜잭션이 적용되지 않습니다. (프록시 기반 AOP의 한계)
2. **public 메서드만 적용**: private 메서드에는 트랜잭션이 적용되지 않습니다.
3. **우선순위**: 클래스에 붙인 것보다 메서드에 구체적으로 붙인 설정이 우선권을 가집니다.

---

## 4. 예시 상황 (계좌 이체)
```java
@Transactional
public void transferMoney(Long fromId, Long toId, int amount) {
    // 1. 송금인 금액 차감
    memberRepository.decreaseBalance(fromId, amount);
    
    // 만약 여기서 에러가 발생한다면? @Transactional 덕분에 1번 작업도 취소됨!
    
    // 2. 수취인 금액 추가
    memberRepository.increaseBalance(toId, amount);
}
```
