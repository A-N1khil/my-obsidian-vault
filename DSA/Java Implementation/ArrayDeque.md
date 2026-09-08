ArrayDeque is a resizable-array implementation of the [[Deque]] interface in Java. It allows elements to be added or removed from both ends efficiently, and it does not allow null element insertion.

- ArrayDeque grows dynamically.
- It generally provides faster operations than LinkedList, as it is more cache-friendly and does not require the overhead of next/previous reference management.
- Operations like addFirst(), addLast(), removeFirst(), removeLast() are done in O(1) amortized time.
- ArrayDeque is not Thread-Safe

## Initialization
```java
ArrayDeque<Integer> deque = new ArrayDeque<>();  // Default capacity of 16 elements
ArrayDeque<Integer> deque1 = new ArrayDeque<>(Collections.emptyList()); // initialize with a list
ArrayDeque<Integer> deque2 = new ArrayDeque<>(21); // Initialize with a specific limit
```

## Adding, Accessing and Removing Elements
```java
Deque<Integer> deque = new ArrayDeque<>();  
  
deque.add(1); // [1]  
deque.offer(2); // [1, 2]  
  
deque.addFirst(3); // [3, 1, 2]  
deque.addLast(4); // [3, 1, 2, 4]  
  
System.out.println(deque.getFirst()); // 3  
System.out.println(deque.getLast()); // 4  
  
System.out.println(deque.pop()); // 3, deque becomes [1, 2, 4]  
System.out.println(deque.poll()); // 1, deque becomes [2, 4]  
System.out.println(deque.pollFirst()); // 2, deque becomes [4]  
  
deque.offerLast(5); // [4, 5]  
System.out.println(deque.pollLast()); // 5, deque becomes [4]
```

## Iteration
```java
for (Integer integer : deque) {  
    System.out.println(integer);  
}  
// [1, 2, 3, 4]  
  
for (Iterator<Integer> iterator = deque.descendingIterator(); iterator.hasNext();) {  
    Integer integer = iterator.next();  
    System.out.println(integer);  
}  
// [4, 3, 2, 1]
```

## Time Complexity of Operations

1. Insertion operations happen in $O(1)$ amortized time.
2. Searching operations like `contains()` take $O(n)$ time.