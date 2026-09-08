## TreeSets
1. Works as a Set $\Rightarrow$ avoid duplicates
2. Keeps **sorting the elements**
3. Searching, insertion, and deletion operations take $\mathbf{O(\log n)}$ time
4. Does not allow null elements

### Operations
#### Navigation
`first()` and `last()` return the smallest and the largest element in the TreeSet.
```java
TreeSet<Integer> treeSet = new TreeSet<>();  
treeSet.add(5);  
treeSet.add(6);  
treeSet.add(7);  
treeSet.add(8);  
treeSet.add(9);  
treeSet.add(10);  
  
System.out.println(treeSet);  
  
// First  
System.out.println(treeSet.first()); // 5  
  
// Last  
System.out.println(treeSet.last()); // 5
```

#### Searching
1. **ceiling(e)** returns the smallest element greater than or equal to `e`.  
2. **floor(e)** returns the largest element less than or equal to `e`.  
3. **higher(e)** returns the smallest element strictly greater than `e`.  
4. **lower(e)** returns the largest element strictly less than `e`.
```java
// Higher  
System.out.println(treeSet.higher(7)); // 8  
// Lower  
System.out.println(treeSet.lower(7)); // 6  
// Ceiling  
System.out.println(treeSet.ceiling(7)); // 7  
// Floor  
System.out.println(treeSet.floor(7)); // 7
```
