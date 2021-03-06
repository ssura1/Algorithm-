Graph
======

* 그래프 G(V,E)는 어떤 자료나 개념을 포함하는 정점(Vertex)들의 집합 V와 이들을 연결하는 간선(Edge)들의 집합 E로 구성된 자료구조이다.
* 그래프는 정점과 간선들로 정의되며, 정점의 위치 정보나 간선의 순서 등은 그래프의 정의에 포함되지 않는다.
* 방향 그래프(Directed Graph)는 각 간선이 방향이라는 속성을 갖는다.   
  방향 그래프에서 두 정점 u,v가 있을 때 u에서 v로 가는 간선과 v에서 u로 가는 간선은 서로 다른 간선이다.
* 방향 그래프와 반대로 방향이 없는 그래프는 무향 그래프(Undirected Graph)라 한다.   
  또다른 그래프의 종류로는 간선이 가중치를 갖는 가중치 그래프(Weighted Graph)가 있다.
* 경로(Path)란 끝과 끝이 연결된 간선들을 순서대로 나열한 것이다.
* 인접 리스트(Adjacency List) 표현은 그래프의 각 정점마다 해당 정점에서 나가는 간선의 목록을 저장해서 그래프를 표현한다.   
  이를 예를 들면 아래와 같은 형태가 된다.
```cpp
vector<list<int>> adjacent;
```
* 이 때 adjacent[i]는 정점 i와 간선을 통해 연결된 정점들의 개수를 저장하는 연결 리스트이다.
* 인접 리스트 표현의 큰 단점은 두 정점이 주어질 때 이 정점이 연결되어 있는지를 알기 위해서는 연결 리스트를 일일히 분석해야 한다는 점이다.   
  이와 같은 연산의 속도를 높이기 위해 고안된 그래프 표현 방식이 인접행렬(Adjacency Matrix) 표현 방식이다.   
  그래프의 인접행렬 표현은 V*V 크기의 행렬, 즉 2차원 배열을 이용해 그래프의 간선 정보를 저장한다. 따라서 가장 간단한 형태의   
  인접 행렬 표현은 단순히 2차원 bool값 배열이 된다.
```cpp
vector<vector<bool>> adjacent;
```
* 위에서 adjacent[i,j]는 정점 i에서 정점 j로 가는 간선 있는지를 나타내는 bool값 변수이다. 만약 가중치 그래프를   
  인접 행렬로 표현하려면 각 간선의 정보를 bool값이 아닌 정수나 실수 변수로 두면 된다.
* 인접행렬 표현의 가장 큰 장점은 정점의 번호 u,v가 주어졌을 때 두 정점을 잇는 간선이 있는지를 한 번의 배열 접근만으로 확인할 수   
  있다는 점이다. 이를 인접리스트 표현으로 확인하려면 연결 리스트를 처음부터 읽어가면서 각 원소를 일일히 확인해야 한다.
* 반면 인접 행렬은 `V*V` 크기의 2차원 배열을 사용하기 때문에, 실제 간선의 개수와 관계없이 항상 `O(V*V)` 크기의 공간을 사용하는 문제점이 있다.   
  인접 리스트 표현은 V개의 연결 리스트에 실제 간선의 수 만큼 원소가 들어 있으므로, 전체 `O(V + E)`의 공간만을 사용한다.
<hr/>

<h2>그래프의 깊이 우선 탐색(DFS)</h2>

* 깊이 우선 탐색(Depth-First Search, DFS)는 그래프의 모든 정점을 발견하는 가장 단순하고 고전적인 방법이다.   
  현재 정점과 인접한 간선들을 하나씩 검사하다가, 아직 방문하지 않은 정점으로 향하는 간선이 있다면 그 간선을 무조건 따라간다.
* 아래 코드는 그래프의 DFS를 구현한 코드이다.
```cpp
// 그래프의 인접 리스트 표현
vector<vector<int>> adj;
// 각 정점을 방문했는지의 여부를 나타낸다.
vector<bool> visited;

// 깊이 우선 탐색의 구현
void dfs(int here) {
    cout << "DFS Visits" << here << '\n';
    for(int i = 0; i < adj[here].size(); i++) {
        int there = adj[here][i];
        // 아직 방문한적이 없다면 방문한다.
        if(!visited[there]) {
            dfs(there);
        }
        // 더 이상 방문할 정점이 없으니, 재귀 호출을
        // 종료하고 이전 정점으로 돌아간다.
    }

    // 모든 정점을 방문한다.
    void dfsAll() {
        // visited를 모두 false로 초기화
        visited = vector<bool>(adj.size(), false);
        // 모든 정점을 순회하면서 아직 방문한 적이 없다면 방문한다.
        for(int i = 0; i < adj.size(); i++) {
            if(!visited[i])
                dfs(i);
        }
    }
}
```
<hr/>

<h3>DFS의 시간 복잡도</h3>

* DFS의 시간 복잡도는 어떤 그래프 표현 방식을 사용하느냐에 따라 달라진다. 위 코드처럼 인접 리스트를 사용해 그래프를 표현하면   
  `dfs()`는 한 정점마다 한 번씩 호출되므로, 정확히 V번 호출된다. `dfs()` 한번의 수행시간은 모든 인접 간선을 검사하는   
  for문에 의해 지배되는데, 모든 정점에 대해 `dfs()`를 수행하고 나면 모든 간선을 정확히 1번(방향 그래프의 경우) 또는   
  2번(무향 그래프의 경우) 확인함을 알 수 있다. 따라서 DFS의 시간 복잡도는 `O(V + E)`가 된다.   
  인접행렬을 사용하는 경우에도 `dfs()`의 호출 횟수는 그대로 V번인데, 인접행렬 사용 시에는 `dfs()`의 내부에서 다른   
  모든 정점을 순회하며 두 정점 사이에 간선이 있는지를 확인해야 하기 때문에 한번의 실행에 `O(V)`의 시간이 든다.   
  따라서 전체 시간 복잡도는 `O(V*V)`가 된다.
<hr/>

<h2>그래프의 너비 우선 탐색(BFS)</h2>

* 너비 우선 탐색(Breadth First Search, BFS)는 시작점에서 가까운 정점 순서대로 방문하는 탐색 알고리즘이다.   
  BFS는 각 정점을 방문할 때 마다 모든 인접 정점들을 검사한다. 이 중 처음보는 정점을 발견하면 검사 예정이라고   
  기록해둔 뒤, 별도의 위치에 저장한다. 인접한 정점을 모두 검사하고 나면, 지금까지 저장한 목록에서 다음 정점을   
  꺼내어 방문하게 된다. 따라서 BFS의 방문 순서는 정점의 목록에서 어떤 정점을 먼저 꺼내는지에 의해 결정된다.
* 아래는 인접리스트로 표현된 그래프를 BFS로 탐색하고, 정점들의 방문 순서를 반환하는 코드이다. `discovered[]`는   
  각 정점의 발견 여부를 저장한다.
```cpp
// 그래프의 인접리스트 표현
vector<vector<int>> adj;

// start에서 시작해 그래프를 BFS하고, 각 정점의 방문 순서 반환
vector<int> bfs(int start) {

    // 각 정점의 방문 여부
    vector<bool> discovered(adj.size(), false);

    // 방문할 정점들의 목록을 유지하는 큐
    queue<int> q;

    // 정점들의 방문 순서를 기록하는 벡터
    vector<int> order;

    discovered[start] = true;
    q.push(start);
    while(!q.empty()) {
        int here = q.front();
        q.pop();

        // here를 방문한다.
        order.push_back(here);

        // 모든 인접한 정점을 검사한다.
        for(int i = 0; i < adj[here].size(); i++) {
            int there = adj[here][i];
            // 처음 보는 정점이면 방문 목록에 집어넣는다.
            if(!discovered[there]) {
                q.push(there);
                discovered[there] = true;
            }
        }
    }
    return order;
}
```
* BFS는 DFS와 달리 발견과 방문이 같지 않다. 따라서 모든 정점은 다음 3개의 상태를 순서대로 거쳐가게 된다.
  1. 아직 발견되지 않은 상태.
  2. 발견됐지만 아직 방문되지는 않은 상태(큐에 저장돼있음)
  3. 방문된 상태
<hr/>

<h3>BFS의 시간 복잡도</h3>

* BFS의 시간 복잡도는 DFS와 다를 것이 없다. 모든 정점을 한 번씩 방문하며, 정점을 방문할 때마다 인접한 모든 간선을   
  검사하기 때문이다. 따라서 인접 리스트로 구현된 경우에는 `O(V+E)`, 인접 행렬로 구현된 경우에는 `O(V*V)`의 시간 복잡도를 갖는다.

<hr/>

<h2>DFS, BFS의 실제 구현 코드</h2>

* 전제 조건 : `bool visited[입력 최대 크기 + 1]`이 전역 변수이다.
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>
using namespace std;

bool visited[MAX + 1];

void dfs(int **adj, int vertex, int size) {

    // adj : 인접행렬식, vertex : dfs를 시작할 정점의 번호
    // size : 인접행렬이 N*N 크기일 때 N
    cout << vertex << " ";
    for(int i = 1; i <= size; i++) {
        if(adj[vertex][i] && !visited[i]) {
            dfs(adj, i, size);
        }
    }
}

void bfs(int **adj, int vertex, int size, queue<int> q) {

    // q : 방문해야할 정점들의 목록을 저장하는 큐
    visited[vertex] = true;
    q.push(vertex);

    while(!q.empty()) {
        vertex = q.front();
        q.pop();
        cout << vertex << " ";
        for(int i = 1; i <= size; i++) {
            if(adj[vertex][i] && !visited[i]) {
                q.push(i);
                visited[i] = true;
            }
        }
    }
}
```