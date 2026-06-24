# Graph Data Structure: Intro and Representations

## What is a Graph?

Imagine a map of cities connected by roads. Each city is a point, and each road is a connection between two cities. That mental picture is exactly what a graph is in computer science.

A **graph** is a data structure made up of **nodes** (also called vertices) and **edges** (connections between nodes). Unlike trees, graphs have no strict parent-child hierarchy — any node can connect to any other node.

Graphs are everywhere: social networks, web pages linked to other pages, GPS navigation, and the internet itself.

---

## Graph Terminology

| Term | Definition |
|------|-----------|
| **Vertex (Node)** | A single point in the graph (e.g. a city) |
| **Edge** | A connection between two vertices (e.g. a road) |
| **Degree** | Number of edges connected to a vertex |
| **Path** | A sequence of vertices connected by edges |
| **Cycle** | A path that starts and ends at the same vertex |
| **Connected Graph** | Every vertex can be reached from every other vertex |
| **Disconnected Graph** | Some vertices cannot reach others |

---

## Types of Graphs

### Directed vs Undirected

| Feature | Undirected | Directed (Digraph) |
|---------|-----------|-------------------|
| Edge direction | No direction (both ways) | Has direction (one way) |
| Example | Facebook friends | Twitter followers |
| Edge notation | (A, B) same as (B, A) | (A, B) ≠ (B, A) |

### Weighted vs Unweighted

- **Weighted** — each edge has a cost/value (e.g. distance in km between cities).
- **Unweighted** — all edges are equal, no cost assigned.

---

## Graph Representations

### Adjacency Matrix

A 2D array of size V × V. `matrix[i][j] = 1` if there is an edge between vertex `i` and vertex `j`. For weighted graphs, store the weight instead of 1.

```
Graph (undirected):
Vertices: 0, 1, 2, 3
Edges: 0-1, 0-2, 1-3, 2-3

Matrix:
     0  1  2  3
  0 [0, 1, 1, 0]
  1 [1, 0, 0, 1]
  2 [1, 0, 0, 1]
  3 [0, 1, 1, 0]

matrix[0][1] = 1 → edge between 0 and 1
matrix[0][3] = 0 → no edge between 0 and 3
```

Symmetric for undirected graphs: `matrix[i][j] == matrix[j][i]`.

**Pros:** Edge existence check is O(1). Simple to implement.
**Cons:** Uses O(V²) space even for sparse graphs.

#### Python

```python
# Adjacency Matrix for an undirected graph
# Vertices: 0, 1, 2, 3  |  Edges: 0-1, 0-2, 1-3, 2-3

V = 4
# Initialize V x V matrix with zeros (no edges)
matrix = [[0] * V for _ in range(V)]

# Add undirected edges (set both directions)
def add_edge(matrix, u, v):
    matrix[u][v] = 1    # edge from u to v
    matrix[v][u] = 1    # edge from v to u (undirected)

add_edge(matrix, 0, 1)
add_edge(matrix, 0, 2)
add_edge(matrix, 1, 3)
add_edge(matrix, 2, 3)

# Print the matrix
for i, row in enumerate(matrix):
    print(f"Vertex {i}: {row}")

# Output:
# Vertex 0: [0, 1, 1, 0]
# Vertex 1: [1, 0, 0, 1]
# Vertex 2: [1, 0, 0, 1]
# Vertex 3: [0, 1, 1, 0]
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Add undirected edge to adjacency matrix
void addEdge(vector<vector<int>>& matrix, int u, int v) {
    matrix[u][v] = 1;   // edge from u to v
    matrix[v][u] = 1;   // edge from v to u (undirected)
}

int main() {
    int V = 4;
    // Initialize V x V matrix with 0 (no edges)
    vector<vector<int>> matrix(V, vector<int>(V, 0));

    addEdge(matrix, 0, 1);
    addEdge(matrix, 0, 2);
    addEdge(matrix, 1, 3);
    addEdge(matrix, 2, 3);

    // Print matrix
    for (int i = 0; i < V; i++) {
        cout << "Vertex " << i << ": ";
        for (int j = 0; j < V; j++)
            cout << matrix[i][j] << " ";
        cout << "\n";
    }
    return 0;
}
// Space: O(V^2) | Edge check: O(1)
```

#### C++ (LeetCode class style)

```cpp
class Graph {
public:
    Graph(int vertices) : V(vertices), matrix(vertices, vector<int>(vertices, 0)) {}

    // Add undirected edge — O(1)
    void addEdge(int u, int v) {
        matrix[u][v] = 1;   // u → v
        matrix[v][u] = 1;   // v → u (undirected)
    }

    // Check if edge exists — O(1)
    bool hasEdge(int u, int v) {
        return matrix[u][v] == 1;
    }

    void print() {
        for (int i = 0; i < V; i++) {
            cout << "Vertex " << i << ": ";
            for (int j = 0; j < V; j++) cout << matrix[i][j] << " ";
            cout << "\n";
        }
    }

private:
    int V;
    vector<vector<int>> matrix;
};
// Space: O(V^2)
```

---

### Adjacency List

Stores a list of neighbors for each vertex. Array of lists — each index holds vertices directly connected to it.

```
Same graph as above using adjacency list:

Vertex 0 → [1, 2]
Vertex 1 → [0, 3]
Vertex 2 → [0, 3]
Vertex 3 → [1, 2]
```

**Pros:** Space efficient for sparse graphs. Iterating over neighbors is fast.
**Cons:** Edge existence check takes O(degree) instead of O(1).

#### Python

```python
# Adjacency List for an undirected graph
# Vertices: 0, 1, 2, 3  |  Edges: 0-1, 0-2, 1-3, 2-3

V = 4
adj = [[] for _ in range(V)]   # list of empty lists for each vertex

def add_edge(adj, u, v):
    adj[u].append(v)    # v is a neighbor of u
    adj[v].append(u)    # u is a neighbor of v (undirected)

add_edge(adj, 0, 1)
add_edge(adj, 0, 2)
add_edge(adj, 1, 3)
add_edge(adj, 2, 3)

# Print adjacency list
for i in range(V):
    print(f"Vertex {i} --> {adj[i]}")

# Output:
# Vertex 0 --> [1, 2]
# Vertex 1 --> [0, 3]
# Vertex 2 --> [0, 3]
# Vertex 3 --> [1, 2]
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Add undirected edge to adjacency list
void addEdge(vector<vector<int>>& adj, int u, int v) {
    adj[u].push_back(v);    // v is a neighbor of u
    adj[v].push_back(u);    // u is a neighbor of v (undirected)
}

int main() {
    int V = 4;
    vector<vector<int>> adj(V);     // V empty neighbor lists

    addEdge(adj, 0, 1);
    addEdge(adj, 0, 2);
    addEdge(adj, 1, 3);
    addEdge(adj, 2, 3);

    // Print adjacency list
    for (int i = 0; i < V; i++) {
        cout << "Vertex " << i << " --> ";
        for (int neighbor : adj[i])
            cout << neighbor << " ";
        cout << "\n";
    }
    return 0;
}
// Space: O(V + E) | Iterate neighbors: O(degree)
```

#### C++ (LeetCode class style)

```cpp
class Graph {
public:
    Graph(int vertices) : V(vertices), adj(vertices) {}

    // Add undirected edge — O(1)
    void addEdge(int u, int v) {
        adj[u].push_back(v);    // v neighbors u
        adj[v].push_back(u);    // u neighbors v
    }

    // Get all neighbors of a vertex — O(degree)
    vector<int>& getNeighbors(int u) {
        return adj[u];
    }

    void print() {
        for (int i = 0; i < V; i++) {
            cout << "Vertex " << i << " --> ";
            for (int nb : adj[i]) cout << nb << " ";
            cout << "\n";
        }
    }

private:
    int V;
    vector<vector<int>> adj;
};
// Space: O(V + E)
```

---

## Adjacency Matrix vs Adjacency List

| Property | Adjacency Matrix | Adjacency List |
|----------|-----------------|---------------|
| Space | O(V²) | O(V + E) |
| Check edge exists | O(1) | O(degree) |
| Add edge | O(1) | O(1) |
| Iterate neighbors | O(V) | O(degree) |
| Best for | Dense graphs | Sparse graphs |

In most real-world problems graphs are **sparse**, so **adjacency list** is the go-to choice.

---

## Building a Graph from Input

In coding problems you usually receive the number of vertices and a list of edges. This is the standard pattern:

#### Python

```python
def build_graph(V, edges, directed=False):
    adj = [[] for _ in range(V)]    # V empty neighbor lists

    for u, v in edges:
        adj[u].append(v)            # u → v
        if not directed:
            adj[v].append(u)        # v → u (undirected only)

    return adj


# Example: undirected graph with 4 vertices
V = 4
edges = [(0, 1), (0, 2), (1, 3), (2, 3)]
adj = build_graph(V, edges)

for i, neighbors in enumerate(adj):
    print(f"{i}: {neighbors}")

# Output:
# 0: [1, 2]
# 1: [0, 3]
# 2: [0, 3]
# 3: [1, 2]
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Build adjacency list from edge list — undirected
vector<vector<int>> buildGraph(int V, vector<pair<int,int>>& edges) {
    vector<vector<int>> adj(V);         // V empty neighbor lists

    for (auto& [u, v] : edges) {
        adj[u].push_back(v);            // u → v
        adj[v].push_back(u);            // v → u (undirected)
    }
    return adj;
}

int main() {
    int V = 4;
    vector<pair<int,int>> edges = {{0,1}, {0,2}, {1,3}, {2,3}};
    auto adj = buildGraph(V, edges);

    for (int i = 0; i < V; i++) {
        cout << i << ": ";
        for (int nb : adj[i]) cout << nb << " ";
        cout << "\n";
    }
    return 0;
}
// Output:
// 0: 1 2
// 1: 0 3
// 2: 0 3
// 3: 1 2
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Build adjacency list from edge list (standard LeetCode graph pattern)
    vector<vector<int>> buildAdjList(int n, vector<vector<int>>& edges) {
        vector<vector<int>> adj(n);     // n empty neighbor lists

        for (auto& edge : edges) {
            int u = edge[0], v = edge[1];
            adj[u].push_back(v);        // u → v
            adj[v].push_back(u);        // v → u (undirected)
        }
        return adj;
    }
};
// This pattern appears in almost every LeetCode graph problem
```

---

## Directed Graph Representation

For a directed graph, when adding edge `u → v`, **only** add `v` to `adj[u]`. Do NOT add `u` to `adj[v]`.

#### Python

```python
# Directed graph: 0→1, 0→2, 1→3
V = 4
adj = [[] for _ in range(V)]

def add_directed_edge(adj, u, v):
    adj[u].append(v)    # only u → v, NOT v → u

add_directed_edge(adj, 0, 1)
add_directed_edge(adj, 0, 2)
add_directed_edge(adj, 1, 3)

for i in range(V):
    print(f"{i} --> {adj[i]}")

# Output:
# 0 --> [1, 2]
# 1 --> [3]
# 2 --> []
# 3 --> []
```

#### C++ (simple)

```cpp
// Directed adjacency list: edge u → v adds v to adj[u] only
void addDirectedEdge(vector<vector<int>>& adj, int u, int v) {
    adj[u].push_back(v);    // one direction only: u → v
}

int main() {
    int V = 4;
    vector<vector<int>> adj(V);

    addDirectedEdge(adj, 0, 1);     // 0 → 1
    addDirectedEdge(adj, 0, 2);     // 0 → 2
    addDirectedEdge(adj, 1, 3);     // 1 → 3

    for (int i = 0; i < V; i++) {
        cout << i << " --> ";
        for (int nb : adj[i]) cout << nb << " ";
        cout << "\n";
    }
    return 0;
}
// Output:
// 0 --> 1 2
// 1 --> 3
// 2 -->
// 3 -->
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Build directed adjacency list (used in topological sort, cycle detection)
    vector<vector<int>> buildDirectedGraph(int n, vector<vector<int>>& edges) {
        vector<vector<int>> adj(n);

        for (auto& edge : edges) {
            int u = edge[0], v = edge[1];
            adj[u].push_back(v);    // directed: u → v only
        }
        return adj;
    }
};
```

---

## Key Takeaways

- A graph has **vertices** and **edges**. It can be directed or undirected, weighted or unweighted.
- **Adjacency Matrix** — O(V²) space, O(1) edge check. Best for dense graphs.
- **Adjacency List** — O(V + E) space, fast neighbor iteration. Best for sparse graphs (most interview problems).
- For **directed** graphs, add only one direction per edge. For **undirected**, add both.
- The `buildAdjList` from edges pattern appears in almost every LeetCode graph problem.

---

## Frequently Asked Questions

**Is a tree a type of graph?**

Yes. A tree is a special graph that is connected, undirected, and has no cycles. Every tree is a graph, but not every graph is a tree.

**When should I use an adjacency matrix instead of a list?**

Use an adjacency matrix when your graph is dense (many edges) and you need to frequently check whether a specific edge exists in O(1) time. For most coding problems, the adjacency list is the better choice.

**Do graph vertices always start from 0?**

In most coding problems, vertices are zero-indexed (starting from 0). Some problems use 1-indexed vertices — always read the problem statement and use `V+1` for your array size if needed.
