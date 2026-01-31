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
