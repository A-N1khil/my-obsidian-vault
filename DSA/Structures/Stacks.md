A stack is a linear data structure that follows a particular order of object insertion and removal. For stacks, the order is **LAST IN FIRST OUT (LIFO)**. The element that is inserted last is the element that is removed first.

## Implementation (Java)
### 1. Stack Class
- It is a legacy collection from early Java versions. It is outdated and rarely used in modern Java
- It's synchronized and thread-safe, which can be slower in single-threaded applications like doing data structures and CP problems.
- Synchronized by default, which might add unnecessary overhead if thread-safety isn't required.

### 2. ArrayDeque Class
- Faster performance for single-threaded scenarios.
- Resizable array-backed without the overhead of synchronization.
- More flexible, as it can also be used as a queue.
- Cannot store `null` elements (throws `NullPointerException` if attempted).