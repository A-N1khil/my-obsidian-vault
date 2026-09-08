Given a positive integer ****n****, find the ****nth**** Fibonacci number.

The [Fibonacci series](https://www.geeksforgeeks.org/dsa/fibonacci-series/) is a sequence where a term is the sum of the previous two terms. The first two terms of the Fibonacci sequence are 0, followed by 1. The Fibonacci sequence: 0, 1, 1, 2, 3, 5, 8, 13, 21.

## Recursive Solution
```java
static int nthFibonacci(int n){
    // base case
	if (n <= 1) {
		return n;
	}
	// sum of the two preceding
	// Fibonacci numbers
	return nthFibonacci(n - 1) + nthFibonacci(n - 2);   
```

### Problem with recursion
****Time Complexity:**** $O(2^n)$, because each state requires answers from the previous two states, and thus calls the function for both of those states recursively.  
****Auxiliary Space:**** $O(n)$, due to the recursion stack

## Using Dynamic Programming
The idea is to optimize the recursive solution by storing the results of already solved subproblems in a memoization table. Whenever the same subproblem appears again, we reuse the stored result instead of recomputing it. This avoids repeated calculations and improves the overall efficiency, reducing the time complexity from exponential to linear.

```mermaid
flowchart TB

A["fib(5)"]

B["fib(4)"]

subgraph S1["overlapping subproblem"]

direction TB

C["fib(3)"] --> S3 & E["fib(1)"]

end

subgraph S2["overlapping subproblem"]

direction TB

F["fib(3)"] --> S4 & H["fib(1)"]

end

subgraph S3["overlapping subproblem"]

direction TB

N["fib(2)"] --> O["fib(1)"] & P["fib(0)"]

end

subgraph S4["overlapping subproblem"]

direction TB

Q["fib(2)"] --> R["fib(1)"] & S["fib(0)"]

end

A --> B & S2

B --> S1

classDef os1 fill:#90EE90,stroke:#2E8B57,stroke-width:2px,color:#000;

classDef os2 fill:#ffcc80,stroke:#ef6c00,stroke-width:2px;

  

class S3,S4 os2

class S1,S2 os1
```
[[Dynamic Programming]] involves remembering the previous results for faster computation time

```java
public int fibonacci(int n) {  
    if (n <= 1) {  
       return n;  
    }  
    int prev1 = 0, prev2 = 1, curr = 0;  
    for (int i = 2; i <= n; i++) {  
       curr = prev1 + prev2;  
       prev1 = prev2;  
       prev2 = curr;  
    }  
    return curr;  
}
```
