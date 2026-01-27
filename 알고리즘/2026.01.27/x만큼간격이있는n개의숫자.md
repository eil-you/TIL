# x만큼 간격이 있는 n개의 숫자

## 1. 문제 설명
함수 solution은 정수 x와 자연수 n을 입력 받아, x부터 시작해 x씩 증가하는 숫자를 n개 지니는 리스트를 리턴해야 합니다. 다음 제한 조건을 보고, 조건을 만족하는 함수, solution을 완성해주세요.

### 매개변수
- int x : x부터 시작하는 숫자
- int n : x씩 증가하는 숫자의 갯수

### 반환값
- long[] : x부터 시작해 x씩 증가하는 숫자를 n개 지니는 리스트

### 제약조건
- -10000000  < x < 10000000
- 0 < n < 10000

### 입출력 예시
| x | n | result |
| --- | --- | --- |
| 2 | 5 | [2, 4, 6, 8, 10] |
| 4 | 3 | [4, 8, 12] |


## 2. 코드 구현
```java
    public ling[] solution(int x, int n){
        long[] answer = new long[n];
        for(int i = 0; i < n; i++){
            answer[i] = (long) x * (i + 1);
        }
        return answer;
    }
```