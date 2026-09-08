KMP solves:

> Find a `pattern` inside a `text` without unnecessarily rechecking characters.

For example:

```
Text:    a a b a a b a a b
Pattern: a a b a
```

KMP runs in:

O(n+m)O(n+m)

where `n` is the text length, and `m` is the pattern length.

The algorithm has **two phases**:

```
1. Build LPS for pattern       O(m)
2. Search pattern in text      O(n)
```

### Prefix

A **prefix** is a substring that **starts at the beginning** of the string.

For:

```
"aaba"
```

Prefixes are:

```
"a"
"aa"
"aab"
"aaba"
```

### Suffix

A **suffix** is a substring that **ends at the end** of the string.

```
"a"
"ba"
"aba"
"aaba"
```

### But LPS uses proper prefixes

A **proper prefix** cannot be the entire string itself.

So for `"aaba"`:

```
Proper prefixes:
"a"
"aa"
"aab"

Proper suffixes:
"a"
"ba"
"aba"
```

Common prefix and suffix:

```
"a"
```

Length = `1`.

Therefore:

```
LPS("aaba") = 1
```

And for every substring `pattern[0...i]`:

```
pattern:  a  a  b  a
index:    0  1  2  3
LPS:      0  1  0  1
```

The key definition to remember is:

> **`LPS[i]` = length of the longest proper prefix of `pattern[0...i]` that is also a suffix of `pattern[0...i]`.**

They do **not** need to be separate/non-overlapping. For example, in `"aaa"`, `"aa"` is both a proper prefix and suffix, so its LPS is `2`

## Building the LPS
```java
private static int[] buildLPS(String pattern) {
    int[] lps = new int[pattern.length()];

    int len = 0;
    int i = 1;

    while (i < pattern.length()) {

        if (pattern.charAt(i) == pattern.charAt(len)) {

            len++;
            lps[i] = len;
            i++;

        } else if (len > 0) {

            len = lps[len - 1];

        } else {

            lps[i] = 0;
            i++;
        }
    }

    return lps;
}
```

### Why do we use `lps[j - 1]` and not `lps[j]`?

This is an easy place to get confused.

When a mismatch occurs at:

```
pattern[j]
```

that character **did not match**.

The successfully matched portion is:

```
pattern[0 ... j-1]
```

So we need the LPS of the last successfully matched character:

```
lps[j - 1]
```

not:

```
lps[j]
```

Think:

```
Pattern:

0 1 2 3 4 5
a a b a a b
| | | | | X
          ↑
          j

Matched portion:
0 ........ j-1
```

Therefore:

```
j = lps[j - 1];
```

## KMP Code
```java
public static int kmp(String text, String pattern) {
    if (pattern.isEmpty()) {
        return 0;
    }

    int[] lps = buildLPS(pattern);

    int i = 0;
    int j = 0;

    while (i < text.length()) {

        if (text.charAt(i) == pattern.charAt(j)) {
            i++;
            j++;

            if (j == pattern.length()) {
                return i - j;
            }

        } else if (j > 0) {

            j = lps[j - 1];

        } else {

            i++;
        }
    }

    return -1;
}
```

## Time Complexity
LPS construction:

```
O(m)
```

Search:

```
O(n)
```

The clever part is that although `j` can move backward:

```
j = lps[j - 1];
```

the **text pointer `i` never moves backward**, making it a pseudo-[[Two Pointer Method]].

```
Text:

a b c d e f g ...
→ → → → → → →

i only moves forward
```

The fallback operations on `j` are also bounded across the search, giving:
$$
O(n+m)O(n+m)
$$
rather than naïve:
$$
O(nm)O(nm)
$$