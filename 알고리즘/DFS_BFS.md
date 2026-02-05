# BFS(Breadth-First Search) & DFS(Depth-First Search)

그래프나 트리 구조에서 모든 노드를 탐색하는 대표적인 두 가지 방법입니다.

---

## 1. BFS (Breadth-First Search, 너비 우선 탐색)

루트 노드(혹은 임의의 노드)에서 시작하여 **인접한 노드를 먼저 탐색**하는 방법입니다.

### 특징
- **탐색 방식**: 수평적 탐색 (가까운 노드부터 탐색)
- **자료구조**: **Queue (큐)** 사용 (FIFO)
- **장점**: 
  - 최단 경로를 보장합니다 (가중치가 없는 그래프에서).
  - 무한한 깊이의 그래프에서도 최단 경로를 찾을 수 있습니다.
- **단점**:
  - 노드가 많을수록 큐에 저장되는 노드가 많아져 메모리 소비가 큽니다.

### Java 구현 예시
```java
import java.util.*;

public void bfs(int startNode, List<List<Integer>> graph, boolean[] visited) {
    Queue<Integer> queue = new LinkedList<>();
    
    queue.add(startNode);
    visited[startNode] = true;
    
    while (!queue.isEmpty()) {
        int node = queue.poll();
        System.out.print(node + " ");
        
        for (int neighbor : graph.get(node)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.add(neighbor);
            }
        }
    }
}
```

---

## 2. DFS (Depth-First Search, 깊이 우선 탐색)

루트 노드(혹은 임의의 노드)에서 시작하여 **다음 분기로 넘어가기 전에 해당 분기를 완벽하게 탐색**하는 방법입니다.

### 특징
- **탐색 방식**: 수직적 탐색 (깊게 들어갔다가 다시 돌아옴)
- **자료구조**: **Stack (스택)** 또는 **재귀(Recursion)** 사용
- **장점**:
  - BFS에 비해 저장 공간의 수요가 적습니다 (탐색 중인 경로 상의 노드만 기억).
  - 목표 노드가 깊은 단계에 있을 경우 BFS보다 빠를 수 있습니다.
- **단점**:
  - 해가 없는 경로에 깊이 빠질 수 있습니다.
  - 얻어진 해가 최단 경로라는 보장이 없습니다.

### Java 구현 예시 (재귀 방식)
```java
public void dfs(int node, List<List<Integer>> graph, boolean[] visited) {
    visited[node] = true;
    System.out.print(node + " ");
    
    for (int neighbor : graph.get(node)) {
        if (!visited[neighbor]) {
            dfs(neighbor, graph, visited);
        }
    }
}
```

---

## 3. 비교 요약

| 특징 | BFS | DFS |
| :--- | :--- | :--- |
| **자료구조** | Queue (큐) | Stack / 재귀 |
| **탐색 기준** | 거리순 (너비) | 경로순 (깊이) |
| **최단 경로** | 보장함 | 보장하지 않음 |
| **메모리 효율** | 노드 밀집도에 따라 다름 | 보통 더 효율적 |
| **활용 예시** | 최단 거리 찾기, 최단 시간 | 모든 경우의 수, 사이클 검사 |

---

## 4. 문제 풀이 팁
- **최단 거리/최소 횟수**를 묻는다면? -> **BFS**
- **모든 노드를 방문**하거나 **경로의 특징**을 저장해야 한다면? -> **DFS**
- **백트래킹** 기법을 사용해야 한다면? -> **DFS**

---

## 5. 실전 문제 분석: 리코쳇 로봇

"최소 이동 횟수"를 구하는 전형적인 **BFS** 문제입니다.

### 핵심 포인트
1. **미끄러지는 이동**: 한 번의 이동으로 벽이나 장애물(`D`)에 부딪힐 때까지 계속 이동해야 합니다. 
   - `while` 루프를 사용하여 방향 이동 후 범위를 벗어나거나 장애물을 만나기 직전 칸까지 좌표를 업데이트합니다.
2. **멈춤 위치 기준 방문 처리**: 단순히 지나는 경로가 아니라, 로봇이 **멈춰 선 위치(Stop Point)**를 기준으로 `visited` 체크를 해야 불필요한 탐색을 방지합니다.
3. **목표 도달 불가능 판단**: BFS 탐색(Queue)이 빌 때까지 목표 지점(`G`)을 찾지 못하면 `-1`을 반환합니다.

### Java 풀이 구조
```java
import java.util.*;

class Node {
    int r, c, count;
    Node(int r, int c, int count) {
        this.r = r;
        this.c = c;
        this.count = count;
    }
}

public int solution(String[] board) {
    int n = board.length;
    int m = board[0].length();
    Queue<Node> queue = new LinkedList<>();
    boolean[][] visited = new boolean[n][m];

    // 1. 시작점(R) 위치 찾기 및 큐 삽입
    // ...

    while (!queue.isEmpty()) {
        Node curr = queue.poll();

        if (board[curr.r].charAt(curr.c) == 'G') return curr.count;

        for (int i = 0; i < 4; i++) {
            int nr = curr.r;
            int nc = curr.c;

            // 2. 미끄러지기 로직 (핵심)
            while (true) {
                int nextR = nr + dr[i];
                int nextC = nc + dc[i];
                if (nextR < 0 || nextR >= n || nextC < 0 || nextC >= m || board[nextR].charAt(nextC) == 'D') break;
                nr = nextR;
                nc = nextC;
            }

            // 3. 멈춘 위치가 처음 방문하는 곳이라면 큐에 추가
            if (!visited[nr][nc]) {
                visited[nr][nc] = true;
                queue.add(new Node(nr, nc, curr.count + 1));
            }
        }
    }

    return -1; // 4. 모든 경로 탐색 후 도달 불가 시 -1
}
```

