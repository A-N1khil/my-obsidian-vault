### 1. What is a Graph?

A graph consists of:

$G=(V,E)$

where **V** is the set of vertices/nodes, and **E** is the set of edges connecting them. For example:
```
0 ----- 1
|       |
|       |
2 ----- 3
```

Here:
```java
V = {0, 1, 2, 3}

E = {
    (0,1),
    (0,2),
    (1,3),
    (2,3)
}
```

Unlike arrays, linked lists, or trees, graphs don't necessarily have a starting point or hierarchical structure. A **tree is actually a special type of graph**: connected and containing no cycles.

## 2. Directed vs Undirected Graphs

### Undirected
```
0 ----- 1
```

The connection works both ways:
```
0 → 1
0 ← 1
```

So an edge `(0,1)` means both nodes are neighbors. When constructing an adjacency list:
```java
// Main.java

graph.get(0).add(1);
graph.get(1).add(0);
```

This second line is one of the easiest things to accidentally forget in CP.

### Directed

```
0 -----> 1
```

Now, 0 can reach 1, but 1 cannot necessarily reach 0. So `graph.get(0).add(1)`. We **do not** add the reverse edge. This distinction becomes important later because algorithms like cycle detection behave differently for directed and undirected graphs.
## 3. Weighted vs Unweighted

An unweighted graph:
```
0 ----- 1
 \
  \
   2
```

means we're only concerned with whether edges exist. A weighted graph associates some value with each edge:
```
     5
0 -------- 1
 \
  \ 10
   \
    2
```

Weights could represent distance, cost, time, latency, etc. Conceptually:

```
0 → (1, 5)
0 → (2, 10)
```

In Java, we could represent an edge as:
```java
// Edge.java

class Edge {
    int to;
    int weight;

    Edge(int to, int weight) {
        this.to = to;
        this.weight = weight;
    }
}

// Main.java
List<List<Edge>> graph;
```

## 4. Degree

For an undirected graph:
```
    1
    |
2---0---3
```

Node `0` has $\text{degree}(0) = 3$. Degree simply means **how many edges touch that vertex**. For directed graphs, we distinguish between:
- indegree  = number of incoming edges
- outdegree = number of outgoing edges

Example:
```
1 ───→ 0 ───→ 3
      ↑
      |
      2
```

For `0`:
```
indegree  = 2
outdegree = 1
```


# 5. Paths and Cycles

A **path** is a sequence of connected vertices.
```
0 --- 1 --- 2 --- 3
```

One path from `0` to `3` is:
```
0 → 1 → 2 → 3
```

A **cycle** occurs when you can follow edges and return to where you started.
```
0 ----- 1
 \     /
  \   /
    2
```

For example:
```
0 → 1 → 2 → 0
```
is a cycle. Cycle detection will become one of our major DFS applications.

# 6. Connected Components

Consider:
```
0 --- 1       3 --- 4
|
2                 5
```

Assuming `5` is isolated, we have:
```
Component 1:
0, 1, 2

Component 2:
3, 4

Component 3:
5
```

Therefore:
```
connected components = 3
```

This leads to one of the most important graph patterns. Suppose we run:

```java
dfs(0);
```

We visit:
```
0, 1, 2
```

But we never reach:
```
3, 4, 5
```

So for disconnected graphs, we usually write:
```java
// Main.java

for (int node = 0; node < n; node++) {
    if (!visited[node]) {
        dfs(node);
    }
}
```

Every time we encounter a new unvisited node:
```java
// Main.java

int components = 0;

for (int node = 0; node < n; node++) {
    if (!visited[node]) {
        components++;
        dfs(node);
    }
}
```

That pattern is worth remembering. You'll see it constantly.

---

# 7. Representing Graphs

There are three representations you should know:
1. Edge List
2. Adjacency Matrix
3. Adjacency List

For CP, **adjacency lists will be your default**.
## Edge List

Suppose:
```
0 --- 1
|     |
2 --- 3
```

Store the edges directly:
```java
// Main.java

int[][] edges = {
    {0, 1},
    {0, 2},
    {1, 3},
    {2, 3}
};
```

Very simple. But suppose I ask:
> What are all the neighbors of node 2?

We potentially have to scan the entire edge list. So finding neighbors is expensive: $O(E)$. Edge lists are useful when algorithms operate directly on edges. **Kruskal's MST algorithm**, which we'll learn later, is a great example.

## Adjacency Matrix

For:
```
0 --- 1
|
2
```

we create:
```
    0  1  2

0   0  1  1
1   1  0  0
2   1  0  0
```

Meaning:
```
matrix[u][v] = 1

if edge u-v exists
```

Java:
```java
// Main.java

int[][] graph = new int[n][n];

graph[0][1] = 1;
graph[1][0] = 1;

graph[0][2] = 1;
graph[2][0] = 1;
```

Checking whether an edge exists is excellent:
```java
graph[u][v]
```

Complexity: $O(1)$. But space is: $O(V^2)$. Imagine:
```
V = 100,000
```

Then you'd theoretically need $1000000^2 = 10^{10}$ matrix entries. Not fun.

## Adjacency List

This is the representation you should get comfortable writing quickly. For:
```
0 --- 1
|
2 --- 3
```

we store:
```
0 → [1, 2]
1 → [0]
2 → [0, 3]
3 → [2]
```


```java
// Main.java

int n = 4;

List<List<Integer>> graph = new ArrayList<>();

for (int i = 0; i < n; i++) {
    graph.add(new ArrayList<>());
}
```

Now add edges:
```java
// Main.java

graph.get(0).add(1);
graph.get(1).add(0);

graph.get(0).add(2);
graph.get(2).add(0);

graph.get(2).add(3);
graph.get(3).add(2);
```

Then:
```java
graph.get(2)
```

gives:
```java
[0, 3]
```

which are exactly the neighbors of `2`.

### Building an Adjacency List From Input

In competitive programming, you're commonly given:
```java
n = 5

edges = [
    [0,1],
    [0,2],
    [1,3],
    [2,4]
]
```

You'll repeatedly write something like:
```java
// Main.java

static List<List<Integer>> buildGraph(int n, int[][] edges) {

    List<List<Integer>> graph = new ArrayList<>();

    for (int i = 0; i < n; i++) {
        graph.add(new ArrayList<>());
    }

    for (int[] edge : edges) {
        int u = edge[0];
        int v = edge[1];

        graph.get(u).add(v);
        graph.get(v).add(u);
    }

    return graph;
}
```

Result:
```
0 → [1, 2]
1 → [0, 3]
2 → [0, 4]
3 → [1]
4 → [2]
```

This little conversion is something you'll do **constantly**.