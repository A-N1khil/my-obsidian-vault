Rabin–Karp solves the same basic problem as KMP:
> Given a **text** and a **pattern**, find occurrences of the pattern inside the text.

But the approach is completely different.
- **KMP:** uses prefix/suffix structure to avoid repeating comparisons.
- **Rabin–Karp:** uses **[[Hashing|hashing]]** to avoid comparing every substring character-by-character.

The central idea is:
> Instead of comparing two strings, compare their **hashes**.

## 1. The basic idea

Suppose:
```
text    = "abcxabcd"
pattern = "abc"
```

Naively, we'd check every length-3 substring:
```
abc  ← compare with "abc"
bcx
cxa
xab
abc  ← compare with "abc"
bcd
```

Rabin–Karp instead computes:
```
hash("abc")
```

and then hashes every window of length `3`:
```
hash("abc")
hash("bcx")
hash("cxa")
hash("xab")
hash("abc")
hash("bcd")
```

Then:
```
windowHash == patternHash
```

means:
> This window **might** be our pattern.

We then verify the actual characters because hashes can collide.

## 2. Rolling Hash

Suppose we know:

```
hash("abc")
```

and our window moves:

```
abcx
^^^
```

to:

```
abcx
 ^^^
```

We need:

```
hash("bcx")
```

Instead of calculating `"bcx"` from scratch, we:

```
1. Remove 'a'
2. Shift remaining characters
3. Add 'x'
```

in **O(1)**.

That's a rolling hash.

Think of it as a [[Sliding Window|sliding window]] where we maintain a hash instead of something like a sum.

## 3. The Hash Formula
### Initial Hash
$$
H = \left(c_0B^{m-1} + c_1B^{m-2} + \dots + c_{m-1}B^0 \right) \mod M
$$
where 
- $c_i$ is ASCII of the $i^{th}$ character of the string, 
- $B$ is the base (usually $256$),
- $m$ is the length of the pattern

### Sliding the Hash
$$
H_{new} = \left[\left(H_{old} - c_0B^{m-1}\right)B + c_m\right] \mod M
$$
Let us consider the following window of `aaba` 
The current hash would be, assuming $B=10$
$$
H_{old} = a\times10^3 + a\times10^2 + b\times10^1 + a
$$
The new window is now `abac`. So, we have to remove `a` and add `c`. If we look $(H_{old} - c_0B^{m-1})$ part of the equation, we get
$$
H_{new} = H_{old} - a\times10^3
$$
Leaving us with,
$$
H_{new} = a\times10^2 + b\times10^1 + a
$$
But, according to the Hash formula, we need the first character to be of the power $m-1$. Hence, we multiply the removal term by $B$
$$
\begin{split}
H_{new} &= \left(a\times10^2 + b\times10^1 + a\right) \times 10 \\
&= a\times10^3 + b\times10^2 + a\times10
\end{split}
$$
and, now we add the last character, i.e. the new character in the window
$$
H_{new} = a\times10^3 + b\times10^2 + a\times10 + c
$$
## Code
```java
public static int rabinKarp(String text, String pattern) {
    int n = text.length();
    int m = pattern.length();

    if (m == 0) return 0;
    if (m > n) return -1;

    long base = 256;
    long mod = 1_000_000_007L;

    long patternHash = 0;
    long windowHash = 0;

    // base^(m-1)
    long highestPower = 1;

    for (int i = 0; i < m - 1; i++) {
        highestPower = (highestPower * base) % mod;
    }

    // Calculate initial hashes
    for (int i = 0; i < m; i++) {
        patternHash =
            (patternHash * base + pattern.charAt(i)) % mod;

        windowHash =
            (windowHash * base + text.charAt(i)) % mod;
    }

    for (int i = 0; i <= n - m; i++) {

        // Hashes match
        if (patternHash == windowHash) {

            // Verify because hash collisions are possible
            boolean match = true;

            for (int j = 0; j < m; j++) {
                if (text.charAt(i + j) != pattern.charAt(j)) {
                    match = false;
                    break;
                }
            }

            if (match) {
                return i;
            }
        }

        // Roll the window
        if (i < n - m) {
            windowHash =
                (windowHash
                    - text.charAt(i) * highestPower % mod
                    + mod)
                % mod;

            windowHash =
                (windowHash * base + text.charAt(i + m))
                % mod;
        }
    }

    return -1;
}
```