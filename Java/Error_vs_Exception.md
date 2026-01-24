# 에러(Error)와 예외(Exception) 정리

Java에서 프로그램 실행 중 발생하는 오류 상황인 `Error`와 `Exception`의 차이, 그리고 예외의 두 종류인 `Checked`와 `Unchecked`에 대해 정리합니다.

---

## 1. 에러(Error) vs 예외(Exception)

모든 예외와 에러는 `Throwable` 클래스를 상속받습니다.

### 1) 에러 (Error)
- **정의**: 시스템 레벨에서 발생하는 심각한 문제로, 애플리케이션 코드 내에서 **복구할 수 없는** 오류입니다.
- **특징**: JVM 실행에 문제가 생긴 것이므로 개발자가 미리 예측하거나 처리하기 어렵습니다.
- **예시**: `OutOfMemoryError` (메모리 부족), `StackOverflowError` (재귀 호출 깊이 초과).

### 2) 예외 (Exception)
- **정의**: 애플리케이션 레벨에서 발생하는 문제로, 개발자가 적절한 코드를 통해 **복구하거나 처리할 수 있는** 오류입니다.
- **특징**: `try-catch` 문을 사용하여 프로그램을 정상 흐름으로 돌릴 수 있습니다.
- **예시**: `NullPointerException`, `IOException`.

---

## 2. Checked Exception vs Unchecked Exception

예외(`Exception`)는 다시 두 가지 종류로 나뉩니다.

| 구분 | Checked Exception | Unchecked Exception |
| :--- | :--- | :--- |
| **상속 계층** | `RuntimeException`을 상속받지 않음 | `RuntimeException`을 상속받음 |
| **처리 시점** | **컴파일 시점**에 확인 | **실행 시점(Runtime)**에 확인 |
| **예외 처리** | **반드시** 처리해야 함 (`try-catch` 또는 `throws`) | 명시적인 처리를 강제하지 않음 |
| **트랜잭션** | 기본적으로 롤백되지 않음 (설정 필요) | 기본적으로 **롤백함** |
| **대표 예시** | `IOException`, `SQLException` | `NullPointerException`, `IndexOutOfBoundsException` |

### 1) Checked Exception (체크 예외)
- 컴파일러가 "이 예외가 발생할 수 있으니 대책을 세워라"라고 체크해주는 예외입니다.
- 외부 자원(파일, DB, 네트워크)을 사용하는 경우 주로 발생합니다.
- 처리하지 않으면 컴파일 에러가 발생합니다.

### 2) Unchecked Exception (언체크 예외)
- 컴파일러가 처리를 강제하지 않는 예외입니다.
- 주로 개발자의 실수(로직 오류)로 인해 발생하는 경우가 많습니다.
- 실행 중(Runtime)에 발생하므로 런타임 예외라고도 부릅니다.

---

## 🚀 정리 요약
- **에러**는 시스템이 고장 난 거라 우리가 못 고친다.
- **예외**는 우리가 코드로 충분히 막거나 고칠 수 있다.
- **Checked**는 컴파일러가 깐깐하게 검사하므로 반드시 대비책을 써야 한다.
- **Unchecked**는 실행해 봐야 아는 것으로, 개발자가 평소에 로직을 잘 짜서 방지해야 한다.
