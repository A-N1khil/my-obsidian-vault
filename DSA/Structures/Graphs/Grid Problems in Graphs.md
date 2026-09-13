# 1. Grid Problems Are Graph Problems

Now we get to one of the most important CP ideas. Suppose LeetCode gives:
```
1 1 0 0
1 1 0 1
0 0 1 1
```

It doesn't look like:
```
0 → [1,2]
1 → [0]
```

But it's still a graph. Each cell can be treated as a **vertex**. For example $(0,0)$ can connect to:
```
(-1,0)   up
(1,0)    down
(0,-1)   left
(0,1)    right
```

Assuming those coordinates are valid. So instead of explicitly constructing:
```java
List<List<Integer>> graph;
```

we simply calculate neighbors when we need them. This is called an **implicit graph**.

# 2. Direction Array Pattern

You'll use this constantly:
```java
int[][] directions = {
    {-1, 0}, // up
    {1, 0},  // down
    {0, -1}, // left
    {0, 1}   // right
};
```

Given `int row` and `int col`. Neighbors are:
```java
for (int[] direction : directions) {
    int newRow = row + direction[0];
    int newCol = col + direction[1];

}
```

Then check boundaries:
```java
if (
    newRow >= 0 &&
    newRow < rows &&
    newCol >= 0 &&
    newCol < cols
) {
    // valid neighbor
}
```

This replaces an adjacency list.

# 3. [[Depth-First Search|DFS]] on a Grid

Consider the classic **Number of Islands** problem.
```
1 1 0 0
1 0 0 1
0 0 1 1
```

An island is a connected component of `1`s. That means the problem is secretly:
> Count connected components.

Which we already know how to solve.
```java
static void dfs(char[][] grid, int row, int col) {

    if (
        row < 0 ||
        row >= grid.length ||
        col < 0 ||
        col >= grid[0].length ||
        grid[row][col] != '1'
    ) {
        return;
    }

    grid[row][col] = '0';

    dfs(grid, row - 1, col);
    dfs(grid, row + 1, col);
    dfs(grid, row, col - 1);
    dfs(grid, row, col + 1);
}
```

Then:
```java
int islands = 0;
for (int row = 0; row < grid.length; row++) {
    for (int col = 0; col < grid[0].length; col++) {
        if (grid[row][col] == '1') {
            islands++;
            dfs(grid, row, col);
        }
    }
}
```

Compare this with connected components:
```java
for (int node = 0; node < n; node++) {
    if (!visited[node]) {
        components++;
        dfs(node);
    }
}
```

They're essentially the same algorithm. That's the pattern-recognition part of graph problems.

## Why Change `'1'` to `'0'`?

Here:
```java
grid[row][col] = '0';
```

We're using the input itself as our `visited` array. Normally:
```java
visited[node] = true;
```

Grid version:
```java
grid[row][col] = '0';
```

means **"I've already processed this land cell".** If modifying the input isn't allowed, use:
```java
boolean[][] visited = new boolean[rows][cols];
```
instead.

# 4. [[Breadth-First Search|BFS]] on a Grid

For BFS, we need to store coordinates in the queue. A simple Java representation:
```java
Queue<int[]> queue = new ArrayDeque<>();
queue.offer(new int[]{row, col});
```

Then:
```java
while (!queue.isEmpty()) {
    int[] cell = queue.poll();
    int row = cell[0];
    int col = cell[1];

    for (int[] direction : directions) {
        int newRow = row + direction[0];
        int newCol = col + direction[1];
        if (
            newRow >= 0 &&
            newRow < rows &&
            newCol >= 0 &&
            newCol < cols &&
            !visited[newRow][newCol]
        ) {
            visited[newRow][newCol] = true;
            queue.offer(
                new int[]{newRow, newCol}
            );
        }
    }
}
```

This is the basic template for a huge number of grid problems.

# 5. Shortest Path in a Grid

Suppose:
```
S . # .
# . # .
. . . E
```

where:
```
S = start
E = end
. = traversable
# = blocked
Moves = up/down/left/right
Cost per move = 1
```

This is simply an **unweighted graph**. Therefore:
```
Shortest path
      +
each move costs 1
      ↓
     BFS
```

You don't need to explicitly construct a graph.

Queue:
```java
[(row, col)]
```

Distance:
```java
int[][] distance = new int[rows][cols];
```

and:
```java
distance[newRow][newCol] = distance[row][col] + 1;
```

Same BFS as before.

# 6. Multi-Source BFS

Now for a very useful variation. Suppose:
```
0 0 0 0
0 X 0 X
0 0 0 0
```

and we want the distance of every cell from its **nearest `X`**. We could run BFS separately from every `X`. But that's wasteful. Instead, put **all sources into the queue initially**:
```java
Queue<int[]> queue = new ArrayDeque<>();
for (...) {
    if (grid[row][col] == 'X') {
        queue.offer(new int[]{row, col});
        distance[row][col] = 0;
    }
}
```

Then run ordinary BFS. Conceptually:
```
        BFS waves

   ← ← X → →

         ↑
         ↓

   ← ← X → →
```

Both sources expand simultaneously. Whichever reaches a cell first gives its shortest distance.

## Rotting Oranges

This is the classic multi-source BFS problem. Imagine:
```
2 1 1
1 1 0
0 1 2
```

where:
```
0 = empty
1 = fresh orange
2 = rotten orange
```

Initially, there are **two rotten oranges**. Both start spreading at the same time. So we put both into the queue:
```
Queue initially:

[(0,0), (2,2)]
```

Then BFS level 1 represents `minute 1`, and level 2 represents `minute 2`, and so on. That's why this isn't:
```
run BFS from first rotten orange
then BFS from second
```

It's:
```
Put ALL rotten oranges into the queue
             ↓
          One BFS
```

Whenever a problem says:
> "These things all start spreading simultaneously..."

your brain should start considering **multi-source BFS**.

# 7. BFS by Levels

Sometimes you explicitly need to know when one BFS level finishes. Use:
```java
while (!queue.isEmpty()) {
    int size = queue.size();
    for (int i = 0; i < size; i++) {
        int node = queue.poll();
        // process node
        for (int neighbor : graph.get(node)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
    // One complete BFS level finished
}
```

Why save:
```java
int size = queue.size();
```

before the loop? Because while processing the current level, we're adding the **next level** into the same queue.

Example:
```
Queue before level:
[1, 2]

size = 2
```

- Process `1`: `queue: [2, 3, 4]`
- Process `2`: `queue: [3, 4, 5]`

Stop after exactly two nodes. Therefore:
```
1,2     ← current level
3,4,5   ← next level
```

Very useful for time-step / spread problems.

# 8. Grid Complexity

For `rows = M, cols = N`, there are $M\times N$ possible vertices. Each cell has at most four neighbors. Therefore, DFS/BFS takes $$\boxed{O(MN)}$$

time. Visited/queue/recursion stack can also require $$\boxed{O(MN)}$$
space in the worst case.