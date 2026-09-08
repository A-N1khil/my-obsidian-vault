The Two-Pointers Technique is a simple yet powerful strategy where you use two indices (pointers) that traverse a data structure - such as an array, list, or string - either toward each other or in the same direction to solve problems more efficiently


### When to Use Two Pointers:

1. Sorted Input  If the array or list is already sorted (or can be sorted), two pointers can efficiently find pairs or ranges. Example: Find two numbers in a sorted array that add up to a target.

2. ****Pairs or Subarrays :****  When the problem asks about two elements, subarrays, or ranges instead of working with single elements. Example: Longest substring without repeating characters, maximum consecutive ones, checking if a string is a palindrome.

3. ****Sliding Window Problems :**** When you need to maintain a window of elements that grows/shrinks based on conditions. Example: Find the smallest subarray with sum ≥ K, move all zeros to the end while maintaining order.

4. ****Linked Lists (Slow–Fast pointers) :**** Detecting cycles, finding the middle node, or checking the palindrome property. Example: Floyd’s Cycle Detection Algorithm (Tortoise and Hare).

## Tortoise and Hare

**Floyd's cycle-finding algorithm**, or the Hare-Tortoise algorithm, is a pointer algorithm that uses only two pointers, moving through the sequence at different speeds. This algorithm is used to find a loop in a linked list. It uses two pointers, one moving twice as fast as the other one. The faster one is called the fast pointer, and the other one is called the slow pointer.

While traversing the linked list, one of these things will occur-
1. The Fast pointer may reach the end (NULL), which shows that there is no loop in the linked list.
2. The Fast pointer again catches the slow pointer at some time; therefore, a loop exists in the linked list.

#### Finding the Middle of a Linked List

We can use the Tortoise and Hare algorithm to find the middle of the linked list.
1. Initialize both slow and fast pointers at the head.
2. Move slow by one step and fast by two steps each iteration.
3. When fast reaches the end (or null), slow will be at the middle.
4. For even nodes, slow automatically ends at the second middle.
```java
ListNode slow = head;  
ListNode fast = head;  
ListNode prev = null;  
  
// Start traversing using the Two Pointer method  
while (fast != null && fast.next != null) {  
    prev = slow;  
    slow = slow.next;  
    fast = fast.next.next;  
}  
  
System.out.println(slow.val);
```

## Finding Subsequence

```java
boolean isSubsequence(String word, String source) {  
    int wordIndex = 0;  
    for (int i = 0; i < source.length() && wordIndex < word.length(); i++) {  
       if (word.charAt(wordIndex) == source.charAt(i)) {  
          wordIndex++;  
       }  
    }  
    return wordIndex == word.length();  
}
```