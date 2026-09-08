Greedy algorithms are a class of algorithms that make locally optimal choices at each step with the hope of finding a global optimum solution.

- At every step of the algorithm, we make a choice that looks the best at the moment. To make the choice, we sometimes sort the array so that we can always get the next optimal choice quickly. We sometimes also use a priority queue to get the next optimal item.
- After making a choice, we check for constraints (if there are any) and keep picking until we find the solution.
- Greedy algorithms do not always give the best solution. For example, in coin change and 0/1 knapsack problems, we get the best solution using Dynamic Programming.
- Examples of popular algorithms where Greedy gives the best solution are [Fractional Knapsack](https://www.geeksforgeeks.org/dsa/fractional-knapsack-problem/), [Dijkstra's algorithm](https://www.geeksforgeeks.org/dsa/dijkstras-shortest-path-algorithm-greedy-algo-7/), [Kruskal's algorithm](https://www.geeksforgeeks.org/dsa/kruskals-minimum-spanning-tree-algorithm-greedy-algo-2/), [Huffman coding](https://www.geeksforgeeks.org/dsa/huffman-coding-greedy-algo-3/) and [Prim's Algorithm](https://www.geeksforgeeks.org/dsa/prims-minimum-spanning-tree-mst-greedy-algo-5/)

### Sorting the Array (Quick Approach)
Greedy approaches often require sorting the given array(s). In case of multiple arrays, try threading them to sort faster

```java
Thread t1 = new Thread(() -> Arrays.sort(g));  
Thread t2 = new Thread(() -> Arrays.sort(s));  
t1.start();  
t2.start();  
try {  
    t1.join();  
    t2.join();  
} catch (Exception e) {  
    // do nothing  
}
```