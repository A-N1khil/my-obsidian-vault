### Reversing a Linked List
```mermaid
flowchart TD

subgraph Step_0["Initial"]
    P0["prev = null"]
    C0["curr"]
    N0["next"]

    A0["1"] --> B0["2"] --> C00["3"] --> X0["null"]

    C0 -.-> A0
    N0 -.-> B0
end

subgraph Step_1["After reversing 1"]
    P1["prev"]
    C1["curr"]
    N1["next"]

    A1["1"] --> X1["null"]
    B1["2"] --> C11["3"] --> X11["null"]

    P1 -.-> A1
    C1 -.-> B1
    N1 -.-> C11
end

subgraph Step_2["After reversing 2"]
    P2["prev"]
    C2["curr"]
    N2["next"]

    B2["2"] --> A2["1"] --> X2["null"]
    C22["3"] --> X22["null"]

    P2 -.-> B2
    C2 -.-> C22
    N2 -.-> X22
end

subgraph Step_3["Final"]
    P3["prev (new head)"]

    C33["3"] --> B3["2"] --> A3["1"] --> X3["null"]

    P3 -.-> C33
end
```

```java
ListNode curr = head, prev = null, next;  
  
while (curr != null) {  
    // Save the next node  
    next = curr.next;  
    // Reverse the current node  
    curr.next = prev;  
    // Move nodes one step ahead  
    prev = curr;  
    curr = next;  
}  
  
return prev;
```