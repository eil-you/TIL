# k 번째 수

## 1. 문제 설명
배열 array의 i번째 숫자부터 j번째 숫자까지 자르고 정렬했을 때, k번째에 있는 수를 구하려 합니다.

예를 들어 array가 [1, 5, 2, 6, 3, 7, 4], i = 2, j = 5, k = 3이라면

array의 2번째부터 5번째까지 자르면 [5, 2, 6, 3]입니다.
1에서 나온 배열을 정렬하면 [2, 3, 5, 6]입니다.
2에서 나온 배열의 3번째 숫자는 5입니다.
배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.


### 매개변수 
- int[] array : 배열
- int[][] commands : 2차원 배열

### 반환값
- int[] : commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.


### 제약 조건
- array의 길이는 1 이상 100 이하입니다.
- array의 각 원소는 1 이상 100 이하입니다.
- commands의 길이는 1 이상 100 이하입니다.
- commands의 각 원소는 길이가 3입니다.


## 2. 문제 풀이
1. for-each문을 사용하여 commands의 모든 원소를 순회
2. commands[i][0]부터 commands[i][1]까지의 배열을 자른 후 commands[i][2]번째 원소를 찾음
3. Arrays.sort()를 사용하여 commands의 모든 원소를 정렬


## 3. 코드 구현
```java
    class Solution{
        public int[] solution(int[] array, int[][] commands){
            int[] answer = new int[commands.length];

            int index = 0;
            for(int[] command : commands){
                int i = command[0];
                int j = command[1];
                int k = command[2];

                int[] temp = new int[j-i+1];
                for(int l = 0; l<temp.length; l++){
                    temp[l] = array[l+i-1];
                }
                Arrays.sort(temp);
                answer[index++] = temp[k-1];
            }
            return answer;
        }

    }
```


## 4. 다른 풀이
```java
    class Solution{
        public int[] solution(int[] array, int[][] commands) {
            int[] answer = new int[commands.length];
            
            for (int n = 0; n < commands.length; n++) {
                int i = commands[n][0];
                int j = commands[n][1];
                int k = commands[n][2];
                
                // i-1번째 인덱스부터 j번째 인덱스 전까지 복사 (j 포함)
                int[] temp = Arrays.copyOfRange(array, i - 1, j);
                
                Arrays.sort(temp);
                answer[n] = temp[k - 1];
            }
            
            return answer;
        }

    }
```