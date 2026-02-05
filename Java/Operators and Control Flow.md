# 자바 기초: 연산자와 제어문 (Operators & Control Flow)

자바 프로그래밍의 가장 기본이 되는 논리적 구성 요소인 연산자와 프로그램의 흐름을 제어하는 제어문에 대해 정리합니다.

---

## 1. 연산자 (Operators)

연산자는 데이터를 가공하여 새로운 값을 만들어내는 기호들입니다.

### 1) 산술 연산자
가장 기본적인 수학 계산을 수행합니다.
- `+` (더하기), `-` (빼기), `*` (곱하기), `/` (나누기), `%` (나머지)
- **주의**: 정수끼리의 나눗셈(`5 / 2`)은 결과가 `2`입니다. (소수점 버림) 실수 결과를 얻으려면 하나 이상의 피연산자를 실수형으로 바꿔야 합니다 (`5 / 2.0` -> `2.5`).

### 2) 비교 연산자
두 값을 비교하여 `boolean`(`true`/`false`) 값을 반환합니다.
- `==` (같음), `!=` (다름)
- `>`, `<`, `>=` (크거나 같음), `<=` (작거나 같음)

### 3) 논리 연산자
여러 조건을 조합할 때 사용합니다.
- `&&` (AND): 모든 조건이 true일 때만 true.
- `||` (OR): 하나라도 true이면 true.
- `!` (NOT): 결과의 반대 (true -> false).

### 4) 증감 연산자
변수의 값을 1씩 증가시키거나 감소시킵니다.
- `++i` (전위형): 값을 먼저 증가시키고 연산에 사용.
- `i++` (후위형): 현재 값을 연산에 사용하고 나중에 증가.

### 5) 삼항 연산자
간단한 `if-else` 문을 한 줄로 표현할 때 사용합니다.
- `(조건식) ? 참일_때_값 : 거짓일_때_값;`

---

## 2. 제어문 (Control Flow Statements)

프로그램의 실행 순서를 결정하는 문장들입니다.

### 1) 조건문 (if, switch)

#### if-else 문
가장 보편적인 조건문입니다.
```java
if (score >= 90) {
    System.out.println("A등급");
} else if (score >= 80) {
    System.out.println("B등급");
} else {
    System.out.println("C등급 이하");
}
```

#### switch 문
변수의 값에 따라 실행할 코드를 결정합니다.
```java
switch (day) {
    case "MONDAY":
        System.out.println("월요병 조심!");
        break; // break가 없으면 다음 case까지 실행됨(Fall-through)
    default:
        System.out.println("평범한 하루");
}
```
> **Java 12+ Switch Expression**: 가독성이 좋아진 화살표(`->`) 문법과 값을 반환하는 기능이 추가되었습니다.
> ```java
> String result = switch(grade) {
>     case "A" -> "Excellent";
>     case "B" -> "Good";
>     default  -> "Unknown";
> };
> ```

### 2) 반복문 (for, while)

#### for 문
정해진 횟수만큼 반복할 때 유리합니다.
```java
for (int i = 0; i < 5; i++) {
    System.out.println("반복 중: " + i);
}

// 향상된 for문 (Enhanced For) - 배열이나 컬렉션 순회 시 유용
int[] nums = {1, 2, 3};
for (int num : nums) {
    System.out.println(num);
}
```

#### while / do-while 문
조건이 참인 동안 계속 반복합니다.
```java
int count = 0;
while (count < 5) {
    count++;
}

// do-while은 조건과 상관없이 무조건 1번은 실행됩니다.
do {
    System.out.println("최소 한 번은 실행");
} while (false);
```

### 3) 반복 제어 (break, continue)
- `break`: 가장 가까운 반복문을 즉시 탈출합니다.
- `continue`: 현재 반복의 나머지 코드를 생략하고 다음 반복(증감식/조건식)으로 넘어갑니다.

---

## 3. 핵심 포인트 (Q&A)

### Q. `==` 연산자로 문자열을 비교하면 안 되나요?
A. 네, 안 됩니다. `==`는 참조 타입의 경우 **주소값**을 비교합니다. 문자열의 **내용**을 비교하려면 반드시 `.equals()` 메서드를 사용해야 합니다.

### Q. Short-Circuit Evaluation(단락 회로 평가)이 무엇인가요?
A. `&&` 연산에서 앞의 조건이 `false`이면 뒤의 조건은 아예 확인하지 않고 결과를 `false`로 확정 짓는 효율적인 동작 방식입니다. (`||`는 앞이 `true`이면 뒤를 무시)

---

### 💡 오늘의 연습 과제
1. `score` 변수가 90점 이상이면 "Pass", 아니면 "Fail"을 출력하는 코드를 **삼항 연산자**로 작성해 보세요.
2. 1부터 10까지의 숫자 중 **짝수만** 출력하는 반복문을 작성해 보세요. (힌트: `%` 연산자와 `continue` 사용)
