Given an integer ****n****, find the number of valid parentheses expressions of length n.

****Examples :**** 

> ****Input:**** n = 2  
> ****Output:**** 1  
> ****Explanation:**** There is only one possible valid expression of length 2, "()"
> 
> ****Input:**** n = 4  
> ****Output:**** 2  
> ****Explanation:**** Possible valid expression of length 4 are "(())" and "()()"
> 
> ****Input:**** n = 6  
> ****Output:**** 5  
> ****Explanation:**** Possible valid expressions are "((()))", "()(())", "()()()", "(())()" and "(()())"

For a Recursive approach, we can see that
- $n$ should be even. An odd number of parentheses cannot form a valid combination
- If the total number of parentheses is $n$, then there should be $n/2$ open and close parentheses

Thus, building a recursive solution in this way can lead to a program with a time complexity of $O(2^n)$
