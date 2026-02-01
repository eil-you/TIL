# JadenCase 문자열 만들기

## 1. 문제 설명

JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 단, 첫 문자가 알파벳이 아닐 때에는 이어지는 알파벳은 소문자로 쓰면 됩니다. (첫 번째 입출력 예 참고)
문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.

### 매개변수
- String s : 알파벳으로 이루어진 문자열

### 반환값
- String : JadenCase로 바꾼 문자열

### 제한 사항
- 1 <= s.length <= 200
- s는 알파벳과 공백으로 이루어진 문자열입니다.
- s에는 영문 대소문자와 공백만이 있습니다.
- 첫 번째 단어는 반드시 알파벳으로 시작합니다.

### 입출력 예
| s | result |
| --- | --- |
| "3people unFollowed me" | "3people Unfollowed Me" |
| "for the last week" | "For The Last Week" |

## 2. 문제 풀이
1. s.toCharArray()를 사용하여 문자 배열로 변환
2. for문을 돌면서 공백이면 isFirst를 true로 변경
3. 첫번째 문자면 대문자
4. 공백이 아니면 소문자

## 3. 코드 구현
```java
class solution{
    public String solution(String s){
        String answer = "";

        boolean isFirst = true;
        for(char c : s.toCharArray()){
            if(c == ' '){
                isFirst = true;
                answer += c;
            }else{
                if(isFirst){
                    answer += Character.isAlphabetic(c)? Character.toUpperCase(c): c;
                    isFirst = false;
                }else{
                    answer += Character.toLowerCase(c);
                }
            }
        }
        return answer;
    } 
}
```

## 4. 다른 풀이
```java
class Solution {
  public String solution(String s) {
        String answer = "";
        String[] sp = s.toLowerCase().split("");
        boolean flag = true;

        for(String ss : sp) {
            answer += flag ? ss.toUpperCase() : ss;
            flag = ss.equals(" ") ? true : false;
        }

        return answer;
  }
}
```
