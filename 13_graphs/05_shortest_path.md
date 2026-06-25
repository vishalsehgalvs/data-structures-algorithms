# Shortest Path Algorithms: Dijkstra and BFS Explained

## What is the Shortest Path Problem?

Given a graph with nodes and edges, find the **minimum cost or minimum number of steps** to travel from one node to another.

Two main algorithms:
- **BFS** — for unweighted graphs (fewest hops)
- **Dijkstra** — for weighted graphs (minimum cost)

---

## When to Use BFS vs Dijkstra

| Feature | BFS | Dijkstra |
|---------|-----|---------|
| Graph type | Unweighted | Weighted (non-negative weights only) |
| Edge weights | All equal (weight = 1) | Different weights per edge |
| Data structure | Queue (FIFO) | Min-heap (priority queue) |
| Time complexity | O(V + E) | O((V + E) log V) |
| Use case | Fewest hops | Minimum cost path |

If every road costs the same, BFS is enough. If roads have different distances, use Dijkstra.

---

## BFS for Shortest Path in Unweighted Graphs

BFS explores level by level. Level 0 = source, level 1 = one hop away, level 2 = two hops, etc. The **first time** BFS reaches a node = shortest path to it.

### Example Graph

```
0 -- 1 -- 3
|         |
2 ------- 4

Edges: 0-1, 0-2, 1-3, 2-4, 3-4
```

#### Python

```python
from collections import deque

def bfs_shortest_path(src, n, adj):
    dist = [-1] * n             # -1 = not visited
    dist[src] = 0               # distance to source is 0
    queue = deque([src])

    while queue:
        node = queue.popleft()
        for neighbor in adj[node]:
            if dist[neighbor] == -1:        # not yet visited
                dist[neighbor] = dist[node] + 1
                queue.append(neighbor)

    for i in range(n):
        print(f"Distance from {src} to {i} = {dist[i]}")


# Build adjacency list (undirected)
n = 5
adj = [[] for _ in range(n)]
for u, v in [(0,1),(0,2),(1,3),(2,4),(3,4)]:
    adj[u].append(v)
    adj[v].append(u)

bfs_shortest_path(0, n, adj)
# Distance from 0 to 0 = 0
# Distance from 0 to 1 = 1
# Distance from 0 to 2 = 1
# Distance from 0 to 3 = 2
# Distance from 0 to 4 = 2
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

void bfsShortestPath(int src, int n, vector<vector<int>>& adj) {
    vector<int> dist(n, -1);    // -1 = not visited
    dist[src] = 0;
    queue<int> q;
    q.push(src);

    while (!q.empty()) {
        int node = q.front(); q.pop();

        for (int neighbor : adj[node]) {
            if (dist[neighbor] == -1) {         // not yet visited
                dist[neighbor] = dist[node] + 1;
                q.push(neighbor);
            }
        }
    }

    for (int i = 0; i < n; i++)
        cout << "Distance from " << src << " to " << i << " = " << dist[i] << "\n";
}

int main() {
    int n = 5;
    vector<vector<int>> adj(n);
    // Undirected edges
    for (auto [u, v] : vector<pair<int,int>>{{0,1},{0,2},{1,3},{2,4},{3,4}}) {
        adj[u].push_back(v); adj[v].push_back(u);
    }
    bfsShortestPath(0, n, adj);
    return 0;
}
// Time: O(V + E) | Space: O(V)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #1091 — Shortest Path in Binary Matrix (BFS shortest path pattern)
    // Returns distance array from src to all nodes (-1 if unreachable)
    vector<int> shortestPath(int n, vector<vector<int>>& edges, int src) {
        vector<vector<int>> adj(n);
        for (auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);          // undirected
        }

        vector<int> dist(n, -1);
        dist[src] = 0;
        queue<int> q;
        q.push(src);

        while (!q.empty()) {
            int node = q.front(); q.pop();

            for (int neighbor : adj[node]) {
                if (dist[neighbor] == -1) {     // first visit = shortest path
                    dist[neighbor] = dist[node] + 1;
                    q.push(neighbor);
                }
            }
        }
        return dist;
    }
};
// Time: O(V + E) | Space: O(V)
```

---

## Dijkstra's Algorithm for Weighted Graphs

**Idea:** Like a greedy traveler — always process the nearest unvisited node next. Uses a **min-heap** so the smallest known distance is always processed first.

**Key step — Relaxation:** if `dist[u] + weight < dist[v]`, update `dist[v]` and push to heap.

### Example Graph (Directed + Weighted)

```
0 --4-- 1
|       |
8       2
|       |
2 --3-- 3
         \
          6-- 4
```

Dijkstra walkthrough from node 0:
| Step | Pop | dist update |
|------|-----|------------|
| 1 | (0, node 0) | node 1 = 4, node 2 = 8 |
| 2 | (4, node 1) | node 3 = 4+2 = 6 |
| 3 | (6, node 3) | node 4 = 6+6 = 12 |
| 4 | (8, node 2) | node 3 via 8+3=11 > 6, skip |
| 5 | (12, node 4) | done |

#### Python

```python
import heapq

def dijkstra(src, n, adj):
    # adj[u] = list of (weight, neighbor)
    dist = [float('inf')] * n
    dist[src] = 0
    heap = [(0, src)]           # (distance, node)

    while heap:
        d, u = heapq.heappop(heap)

        if d > dist[u]:         # outdated entry — skip
            continue

        for weight, v in adj[u]:
            new_dist = dist[u] + weight
            if new_dist < dist[v]:          # relaxation step
                dist[v] = new_dist
                heapq.heappush(heap, (dist[v], v))

    for i in range(n):
        print(f"Shortest distance from {src} to {i} = {dist[i]}")


# Build adjacency list: adj[u] = [(weight, v)]
n = 5
adj = [[] for _ in range(n)]
for u, v, w in [(0,1,4),(0,2,8),(1,3,2),(2,3,3),(3,4,6)]:
    adj[u].append((w, v))   # directed

dijkstra(0, n, adj)
# Shortest distance from 0 to 0 = 0
# Shortest distance from 0 to 1 = 4
# Shortest distance from 0 to 2 = 8
# Shortest distance from 0 to 3 = 6
# Shortest distance from 0 to 4 = 12
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;

typedef pair<int,int> pii;  // {weight, neighbor}

void dijkstra(int src, int n, vector<vector<pii>>& adj) {
    vector<int> dist(n, INT_MAX);
    dist[src] = 0;

    // Min-heap: {distance, node}
    priority_queue<pii, vector<pii>, greater<pii>> pq;
    pq.push({0, src});

    while (!pq.empty()) {
        int d = pq.top().first;
        int u = pq.top().second;
        pq.pop();

        if (d > dist[u]) continue;  // outdated heap entry — skip

        for (auto& [weight, v] : adj[u]) {
            if (dist[u] + weight < dist[v]) {   // relaxation
                dist[v] = dist[u] + weight;
                pq.push({dist[v], v});
            }
        }
    }

    for (int i = 0; i < n; i++)
        cout << "Shortest distance from " << src << " to " << i << " = " << dist[i] << "\n";
}

int main() {
    int n = 5;
    vector<vector<pii>> adj(n);     // {weight, neighbor}
    adj[0].push_back({4, 1}); adj[0].push_back({8, 2});
    adj[1].push_back({2, 3});
    adj[2].push_back({3, 3});
    adj[3].push_back({6, 4});

    dijkstra(0, n, adj);
    return 0;
}
// Time: O((V + E) log V) | Space: O(V + E)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #743 — Network Delay Time (Dijkstra from single source)
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        vector<vector<pair<int,int>>> adj(n + 1);   // 1-indexed
        for (auto& t : times)
            adj[t[0]].push_back({t[1], t[2]});      // {neighbor, weight}

        vector<int> dist(n + 1, INT_MAX);
        dist[k] = 0;

        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
        pq.push({0, k});    // {distance, node}

        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();

            if (d > dist[u]) continue;          // outdated entry

            for (auto [v, w] : adj[u]) {
                if (dist[u] + w < dist[v]) {    // relaxation
                    dist[v] = dist[u] + w;
                    pq.push({dist[v], v});
                }
            }
        }

        int maxDist = *max_element(dist.begin() + 1, dist.end());
        return (maxDist == INT_MAX) ? -1 : maxDist;
    }
};
// Time: O((V + E) log V) | Space: O(V + E)
```

---

## Complexity Summary

| Algorithm | Time | Space | Graph Type |
|-----------|------|-------|-----------|
| BFS shortest path | O(V + E) | O(V) | Unweighted |
| Dijkstra | O((V + E) log V) | O(V + E) | Weighted (non-negative) |

---

## Common Mistakes to Avoid

- Using Dijkstra on graphs with **negative weights** — use Bellman-Ford instead.
- **Forgetting** the `if d > dist[u]: continue` check — without it you process stale heap entries.
- Using BFS on **weighted graphs** — BFS only works when all edges have equal weight.
- Not initializing `dist` to **infinity** — always start with `INT_MAX` or `float('inf')`.

---

## Key Takeaways

- **BFS** finds shortest path in unweighted graphs by exploring level by level. O(V + E).
- **Dijkstra** finds shortest path in weighted graphs using a min-heap + relaxation. O((V + E) log V).
- **Relaxation** is the core of Dijkstra: if going through the current node gives a cheaper path to a neighbor, update and push to heap.
- Dijkstra does **not** work with negative weights.

---

## FAQ

**Can BFS handle weighted graphs?**

No. BFS treats all edges as weight 1. Use Dijkstra for weighted graphs.

**Does Dijkstra work with negative edge weights?**

No. Use the Bellman-Ford algorithm for graphs with negative weights.

**Why does Dijkstra use a min-heap?**

The min-heap always returns the node with the smallest known distance next. This greedy choice ensures each node is finalized correctly on first pop, making the algorithm efficient and correct.
