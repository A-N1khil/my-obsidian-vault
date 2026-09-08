A child is running up a ****staircase**** with ****n**** steps and can hop either ****1 step, 2 steps, or 3 steps**** at a time. The task is to implement a method to ****count how many possible ways**** the child can run up the stairs.

****Examples:**** 

> ****Input:**** 4  
> ****Output:**** 7  
> ****Explanation:**** There are seven ways: {1, 1, 1, 1}, {1, 2, 1}, {2, 1, 1}, {1, 1, 2}, {2, 2}, {3, 1}, {1, 3}.
> 
> ****Input:**** 3  
> ****Output:**** 4  
> ****Explanation:**** There are four ways: {1, 1, 1}, {1, 2}, {2, 1}, {3}.

There are ****n**** stairs, and a person is allowed to jump to the next stair, skip one stair, or skip two stairs. So there are n stairs. So if a person is standing at ****i-th**** stair, the person can move to ****i+1, i+2, i+3-th**** stair. A recursive function can be formed where at current ****index i**** , the function is recursively called for $i+1$, $i+2$, and $i+3$<sup>th</sup> stair.   
There is another way of forming the recursive function. To reach a stair i, a person has to jump either from $i-1$, $i-2$ or $i-3$<sup>th</sup> stair.

A recursive approach would consider overlapping problems like the [[Fibonacci Numbers]] series and cost $O(3^n)$ time. Considering a [[Dynamic Programming]] approach, we can solve it in $O(n)$ time

```java
public int countNth(int n) {  
    if (n == 0) return 0;  
    if (n == 1) return 1;  
    if (n == 2) return 2;  
  
    int[] dp = new int[n + 1];  
    dp[0] = 1;  
    dp[1] = 1;  
    dp[2] = 2;  
  
    for (int i = 3; i <= n; i++) {  
       dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 3];  
    }  
  
    return dp[n];  
}
```
