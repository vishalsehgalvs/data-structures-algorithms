# String Hashing Basics — Fast String Comparisons

> **One-line summary:**
> String hashing converts a string into a number (hash value) so you can compare strings or substrings in O(1) time instead of O(n), after an O(n) preprocessing step.

---

## Table of Contents

1. [What is String Hashing?](#1-what-is-string-hashing)
2. [Why Do We Need String Hashing?](#2-why-do-we-need-string-hashing)
3. [How a Hash Function Works](#3-how-a-hash-function-works)
4. [Polynomial Rolling Hash Formula](#4-polynomial-rolling-hash-formula)
5. [Mapping Characters to Numbers](#5-mapping-characters-to-numbers)
6. [Computing a Simple Hash](#6-computing-a-simple-hash)
7. [Prefix Hashing for Substrings](#7-prefix-hashing-for-substrings)
8. [Comparing Substrings Using Hashing](#8-comparing-substrings-using-hashing)
9. [Hash Collisions and How to Handle Them](#9-hash-collisions-and-how-to-handle-them)
10. [Hashing vs Direct Comparison](#10-hashing-vs-direct-comparison)
11. [Practical Applications](#11-practical-applications)
12. [Key Takeaways](#12-key-takeaways)
13. [FAQs](#13-faqs)

---

## 1. What is String Hashing?

Imagine you have two long books and you want to check if they are identical. Reading every page would take forever. What if you could generate a **short unique code** for each book and just compare those two codes?

That is exactly what string hashing does.

> **String hashing** converts a string into a single number called a **hash value** (or hash code). Instead of comparing characters one by one, you compare hash values — much faster.

```mermaid
flowchart LR
    A["String\n'abcde'"] --> B["Hash Function"]
    B --> C["Hash Value\n1234567"]
    D["String\n'abcde'"] --> E["Hash Function"]
    E --> F["Hash Value\n1234567"]
    C --> G{Equal?}
    F --> G
    G -- Yes --> H["Strings are equal ✓"]
    G -- No  --> I["Strings differ ✗"]
```

---

## 2. Why Do We Need String Hashing?

Comparing two strings of length `n` takes **O(n)** time. If you need to do this thousands of times across many substrings, it becomes very slow.

String hashing reduces each comparison to **O(1)** after an O(n) preprocessing step.

| Scenario                            | Without Hashing | With Hashing                      |
| ----------------------------------- | --------------- | --------------------------------- |
| One comparison                      | O(n)            | O(n) preprocessing + O(1) compare |
| 1000 comparisons on the same string | O(1000n)        | O(n) + O(1000)                    |
| Find duplicate substrings           | O(n³) naive     | O(n) with prefix hash             |

This is especially useful in pattern matching (Rabin-Karp), anagram variants, and substring equality checks.

---

## 3. How a Hash Function Works

A hash function takes a string as input and returns a number. The goal: produce **different numbers for different strings** as reliably as possible.

The most common approach in competitive programming is **polynomial rolling hashing**.

```
Input string  →  Hash Function  →  A single integer
"abc"         →  h(s)           →  1026
"xyz"         →  h(s)           →  (different number)
```

---

## 4. Polynomial Rolling Hash Formula

Each character is treated as a number and combined into a weighted sum using a prime base `p` and a large prime modulus `M`.

$$\text{hash}(s) = s[0] \cdot p^{n-1} + s[1] \cdot p^{n-2} + \ldots + s[n-1] \cdot p^{0} \pmod{M}$$

Or equivalently, building left to right with increasing powers:

$$\text{hash}(s) = \sum_{i=0}^{n-1} \text{val}(s[i]) \cdot p^{i} \pmod{M}$$

**Common choices:**

| Parameter | Value     | Why                                               |
| --------- | --------- | ------------------------------------------------- |
| `p`       | `31`      | Prime that fits 26 lowercase letters well         |
| `M`       | `10⁹ + 9` | Large prime — keeps numbers in safe integer range |

Using a **prime base** means each character maps to a unique weighted contribution. The **modulo** keeps the hash value from overflowing integer limits.

---

## 5. Mapping Characters to Numbers

For lowercase English letters, map `'a' → 1`, `'b' → 2`, ..., `'z' → 26`.

```python
char_value = ord(ch) - ord('a') + 1
# 'a' → 1,  'b' → 2,  'c' → 3, ...,  'z' → 26
```

```cpp
int char_value = ch - 'a' + 1;
```

> **Why not start at 0?** If `'a'` mapped to 0, then `"a"`, `"aa"`, `"aaa"` would all hash to 0, causing collisions. Starting at 1 avoids this.

---

## 6. Computing a Simple Hash

**Step-by-step for `"abc"` with `p = 31`, `M = 10⁹ + 9`:**

$$\text{hash} = 1 \cdot 31^0 + 2 \cdot 31^1 + 3 \cdot 31^2 = 1 + 62 + 961 = 1024$$

| Character | Value | Power     | Contribution |
| --------- | ----- | --------- | ------------ |
| `'a'`     | 1     | 31⁰ = 1   | 1            |
| `'b'`     | 2     | 31¹ = 31  | 62           |
| `'c'`     | 3     | 31² = 961 | 2883         |
| **Total** |       |           | **2946**     |

```python
# Python — Simple hash computation
def compute_hash(s):
    p = 31
    M = 10**9 + 9
    h = 0
    p_power = 1   # starts at p^0 = 1

    for ch in s:
        h = (h + (ord(ch) - ord('a') + 1) * p_power) % M
        p_power = (p_power * p) % M

    return h


print(compute_hash("abc"))   # Output: 2946
print(compute_hash("bca"))   # Output: different value
```

```cpp
// C++ — Simple hash computation
#include <iostream>
#include <string>

long long compute_hash(const std::string& s) {
    const long long p = 31;
    const long long M = 1e9 + 9;
    long long h = 0;
    long long p_power = 1;   // p^0 = 1

    for (char ch : s) {
        h = (h + (ch - 'a' + 1) * p_power) % M;
        p_power = (p_power * p) % M;
    }
    return h;
}

int main() {
    std::cout << compute_hash("abc") << std::endl;   // Output: 2946
    std::cout << compute_hash("bca") << std::endl;   // Different value
}
```

> Notice `"abc"` and `"bca"` produce **different hashes** even though they contain the same characters. Position matters in the formula — this distinguishes hashing from frequency counting.

---

## 7. Prefix Hashing for Substrings

The most powerful use of string hashing: compute the hash of **any substring in O(1)** after an O(n) setup.

This is the string equivalent of **prefix sums** — instead of cumulative totals, we build cumulative hashes.

### Building the Prefix Hash Array

```
s = "abcde"

prefix_hash[0] = 0
prefix_hash[1] = hash("a")
prefix_hash[2] = hash("ab")
prefix_hash[3] = hash("abc")
prefix_hash[4] = hash("abcd")
prefix_hash[5] = hash("abcde")
```

```python
# Python — Build prefix hash array
def build_prefix_hash(s):
    p = 31
    M = 10**9 + 9
    n = len(s)

    prefix_hash = [0] * (n + 1)   # prefix_hash[0] = 0
    p_power = [1] * (n + 1)       # p_power[i] = p^i % M

    for i in range(1, n + 1):
        prefix_hash[i] = (prefix_hash[i - 1] + (ord(s[i - 1]) - ord('a') + 1) * p_power[i - 1]) % M
        p_power[i] = (p_power[i - 1] * p) % M

    return prefix_hash, p_power


s = "abcde"
prefix_hash, p_power = build_prefix_hash(s)
print(prefix_hash)   # [0, 1, 63, 2946, ...]
```

```cpp
// C++ — Build prefix hash array
#include <vector>
#include <string>

std::pair<std::vector<long long>, std::vector<long long>>
build_prefix_hash(const std::string& s) {
    const long long p = 31;
    const long long M = 1e9 + 9;
    int n = s.length();

    std::vector<long long> prefix_hash(n + 1, 0);
    std::vector<long long> p_power(n + 1, 1);

    for (int i = 1; i <= n; i++) {
        prefix_hash[i] = (prefix_hash[i - 1] + (s[i - 1] - 'a' + 1) * p_power[i - 1]) % M;
        p_power[i] = (p_power[i - 1] * p) % M;
    }

    return {prefix_hash, p_power};
}
```

---

## 8. Comparing Substrings Using Hashing

To compare two substrings `s[l1..r1]` and `s[l2..r2]`, extract their hashes and compare. Since each substring starts at a different position, we normalise by multiplying by the appropriate power of `p` before comparing.

**The comparison formula:**

$$\text{hash}(s[l..r]) \cdot p^{l_2} \equiv \text{hash}(s[l_2..r_2]) \cdot p^{l_1} \pmod{M}$$

```python
# Python — Compare two substrings in O(1)
def are_substrings_equal(s, l1, r1, l2, r2):
    M = 10**9 + 9
    prefix_hash, p_power = build_prefix_hash(s)

    # Raw hash of each substring (not yet position-normalised)
    hash1 = (prefix_hash[r1 + 1] - prefix_hash[l1] + M) % M
    hash2 = (prefix_hash[r2 + 1] - prefix_hash[l2] + M) % M

    # Normalise by cross-multiplying with the other substring's power offset
    lhs = (hash1 * p_power[l2]) % M
    rhs = (hash2 * p_power[l1]) % M

    return lhs == rhs


s = "abcabc"
print(are_substrings_equal(s, 0, 2, 3, 5))   # Output: True  ("abc" == "abc")
print(are_substrings_equal(s, 0, 1, 2, 3))   # Output: False ("ab"  != "ca")
```

**Why `+ M` before `% M`?** The subtraction `prefix_hash[r+1] - prefix_hash[l]` can go negative due to modular arithmetic. Adding `M` before taking mod ensures the result stays positive.

---

## 9. Hash Collisions and How to Handle Them

A **collision** happens when two different strings produce the same hash value. It is rare but theoretically possible — like two different people having the same fingerprint.

### Double Hashing Technique

Compute **two independent hashes** with different bases and moduli. Two strings are considered equal only if **both** hashes match. This reduces collision probability to practically zero.

```python
# Python — Double hashing
def compute_double_hash(s):
    p1, M1 = 31, 10**9 + 9
    p2, M2 = 37, 10**9 + 7

    h1, h2 = 0, 0
    pow1, pow2 = 1, 1

    for ch in s:
        val = ord(ch) - ord('a') + 1
        h1 = (h1 + val * pow1) % M1
        h2 = (h2 + val * pow2) % M2
        pow1 = (pow1 * p1) % M1
        pow2 = (pow2 * p2) % M2

    return (h1, h2)   # return a pair — both must match for equality


print(compute_double_hash("abc"))   # (num1, num2)
print(compute_double_hash("abc"))   # Same pair — strings are equal
print(compute_double_hash("bca"))   # Different pair
```

```cpp
// C++ — Double hashing
#include <string>
#include <utility>

std::pair<long long, long long> compute_double_hash(const std::string& s) {
    const long long p1 = 31, M1 = 1e9 + 9;
    const long long p2 = 37, M2 = 1e9 + 7;

    long long h1 = 0, h2 = 0;
    long long pow1 = 1, pow2 = 1;

    for (char ch : s) {
        int val = ch - 'a' + 1;
        h1 = (h1 + val * pow1) % M1;
        h2 = (h2 + val * pow2) % M2;
        pow1 = (pow1 * p1) % M1;
        pow2 = (pow2 * p2) % M2;
    }

    return {h1, h2};
}
```

> **Rule of thumb:** Use single hashing for learning and most competitive programming. Use double hashing when correctness is critical or the problem has anti-hash tests.

---

## 10. Hashing vs Direct Comparison

| Criteria            | Direct Comparison   | String Hashing                  |
| ------------------- | ------------------- | ------------------------------- |
| Time per comparison | O(n)                | O(1) after preprocessing        |
| Preprocessing time  | None                | O(n)                            |
| Risk of error       | None                | Rare collision possible         |
| Best use case       | One-time comparison | Many comparisons on same string |
| Memory needed       | O(1)                | O(n) for prefix hash array      |

Use **direct comparison** when you only compare strings once. Use **hashing** when you need many substring comparisons on the same string efficiently.

---

## 11. Practical Applications

String hashing shows up in many real-world and competitive programming scenarios:

| Application                      | How Hashing Helps                                                  |
| -------------------------------- | ------------------------------------------------------------------ |
| **Rabin-Karp pattern search**    | Hash the pattern, slide a hash window across text — O(n+m) average |
| **Finding duplicate substrings** | Hash every substring, check for matching hashes                    |
| **Checking string rotations**    | A rotation of `s` is a substring of `s + s` — compare hashes       |
| **Plagiarism detection**         | Compare document segments as hash values                           |
| **Longest common substring**     | Binary search on length + hashing to check existence               |

In all these cases, hashing avoids repeated O(n) comparisons and brings overall complexity down significantly.

---

## 12. Key Takeaways

- **String hashing** maps a string to a number so comparisons become O(1) instead of O(n).
- The **polynomial rolling hash** formula uses a prime base `p` and a large prime modulus `M` to produce distinct hash values.
- Map characters as `'a' → 1, 'b' → 2, ..., 'z' → 26` — never start at 0 to avoid collisions on repeated characters.
- **Prefix hashing** precomputes cumulative hashes in O(n), enabling any substring hash to be retrieved in O(1) — just like prefix sums but for strings.
- **Hash collisions** are rare but possible. Use **double hashing** (two different base/modulus pairs) for near-zero collision probability.
- Common standard choices: `p = 31`, `M = 10⁹ + 9` for single hash; add `p = 37`, `M = 10⁹ + 7` for double hash.

---

## 13. FAQs

**Q: What base and modulus should I use?**  
Use `p = 31` and `M = 10⁹ + 9` as a standard starting point for lowercase letters. For double hashing, add a second pair: `p = 37` and `M = 10⁹ + 7`.

**Q: Is string hashing the same as using a HashMap?**  
Not exactly. A HashMap uses hashing internally to store key-value pairs. String hashing here means computing a numeric fingerprint of a string or substring for fast comparison. They share the same underlying concept but serve different purposes.

**Q: Can string hashing replace all direct string comparisons?**  
For most competitive programming problems, yes. However, since collisions are theoretically possible, in production code you should verify with a direct comparison when correctness is critical. Double hashing reduces collision risk to nearly zero in practice.

**Q: Why do we add `M` before taking modulo when computing substring hashes?**  
The subtraction `prefix_hash[r+1] - prefix_hash[l]` can produce a negative number in modular arithmetic. Adding `M` before `% M` guarantees the result is non-negative: `(a - b + M) % M` is always in `[0, M-1]`.

**Q: How does Rabin-Karp use string hashing?**  
Rabin-Karp computes the hash of the pattern, then slides a hash window of the same length across the text. At each position, it computes the window's hash in O(1) using a rolling update. If the hashes match, it verifies with a direct comparison. This gives O(n + m) average time for pattern searching.
