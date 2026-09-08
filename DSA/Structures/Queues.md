## Priority Queue
A PriorityQueue in Java is a queue where elements are ordered based on their priority, rather than the order of insertion. By default, it uses natural ordering ([min-heap](https://www.geeksforgeeks.org/dsa/introduction-to-min-heap-data-structure/)), but a custom comparator can be used to define different priorities.

- Elements are processed based on priority rather than insertion order.
- Supports standard queue operations like add(), poll(), and peek().
- Automatically grows as elements are added, and null insertion is not possible.
- Uses a [heap data structure](https://www.geeksforgeeks.org/dsa/heap-data-structure/) internally to ensure efficient insertion and removal of the highest-priority element.

### Adding and Removing
```java
PriorityQueue<Integer> pq = new PriorityQueue<>();  
  
pq.add(1);  
pq.offer(2);  
System.out.println(pq.peek());  // Prints 1 but does not remove it
System.out.println(pq.poll());  // Prints 1 and removes it
System.out.println(pq); // [2]
```

### Using a Comparator
We can add a custom Comparator during the initialization
```java
PriorityQueue<Integer> pqRev = new PriorityQueue<>(Collections.reverseOrder());  
  
pqRev.add(1);  
pqRev.offer(2);  
System.out.println(pqRev.peek());  // 2
System.out.println(pqRev.poll());  // 2, and removes it
System.out.println(pqRev); // [1]
```
