# 문자열 내 p와 y의 개수

## 1. 문제 설명

이 문제에는 표준 입력으로 영어 알파벳으로 이루어진 단어가 주어집니다.
p와 y의 개수가 일치하면 true, 일치하지 않으면 false를 리턴하는 함수, solution을 완성해주세요.

### 매개변수
- String word : 알파벳으로 이루어진 단어

### 반환값
- boolean : p와 y의 개수가 일치하면 true, 일치하지 않으면 false

## 2. 코드 구현
```java
    public boolean solution(String word) {
        int pCount = 0;
        int yCount = 0;
        word = word.toLowerCase();
        for (char c : word.toCharArray()) {
            if (c == 'p') {
                pCount++;
            } else if (c == 'y') {
                yCount++;
            }
        }
        return pCount == yCount;
    }
```