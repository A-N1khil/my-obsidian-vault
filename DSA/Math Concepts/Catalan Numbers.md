**Catalan numbers*** are a sequence of natural numbers that appear in various counting problems, often related to recursive structures. They are named after the French-Belgian mathematician Eugène Charles Catalan.

These numbers have applications in [combinatorial](https://www.geeksforgeeks.org/engineering-mathematics/combinatorics/) mathematics, such as counting paths, tree structures, and polygon triangulations.

Catalan numbers arise in several combinatorial problems, including:
- Parenthesization: The number of ways to correctly parenthesize n + 1 factors (e.g., for multiplying expressions like (a⋅b)⋅(c⋅d)).
	- Application: Used in compilers, syntax checking, and expression evaluation.
- Binary Trees: The number of distinct binary trees with n internal nodes.
	- Application: In data structures like AVL trees, Red-Black trees, and splay trees.
- Triangulations: The number of ways to triangulate a polygon with n + 2 sides.
	- Application: Computer graphics, mesh generation, and computational geometry.
- Paths: The number of paths along the edges of a grid that do not pass above the main diagonal.
	- Application: Compiler theory, token matching, context-free grammars.
- Generating Combinations: Catalan numbers are used in combinatorial generation algorithms for generating combinations, partitions, and tree structures.
	- Application: Used in AI, automated testing, and combinatorial optimization problems.
- Matrix Chain Multiplication Orderings: The number of ways to fully parenthesize a product of n + 1 matrices is given by the n-th Catalan number.
	- Application: Optimizing matrix multiplication in dynamic programming algorithms.

## Formula for generating a Catalan Number
$$
C_n = \frac{1}{n+1}\binom{2n}{n} = \frac{2n!}{(n+1)n!}
$$
where, $C_n$ denotes the $n+1^{\text{th}}$ Catalan Number

## Code
```java
public long binomialCoefficient(int n, int k) {  
    long res = 1;  
  
    // Since C(n, k) = C(n, n-k)  
    if (k > n - k)  
       k = n - k;  
  
    // Calculate value of [n*(n-1)*---*(n-k+1)] /  
    // [k*(k-1)*---*1]    for (int i = 0; i < k; ++i) {  
       res *= (n - i);  
       res /= (i + 1);  
    }  
  
    return res;  
}  
  
public long catalanNumber(int n) {  
    long c = binomialCoefficient(2 * n, n);  
    return c / (n + 1);  
}
```