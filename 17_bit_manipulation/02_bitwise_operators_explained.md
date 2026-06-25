# Bitwise Operators Explained: Beginner-Friendly Guide

## What Are Bitwise Operators?

Bitwise operators work directly on the binary (bit-level) representation of numbers. Each operator acts on individual bit positions, just like logical operators act on true/false values — but for every bit simultaneously.

There are **six core operators**:

| Operator    | Symbol | Key Rule                              | Common Use                 |
|-------------|--------|---------------------------------------|----------------------------|
| AND         | `&`    | 1 only if **both** bits are 1         | Check and mask bits        |
| OR          | `\|`   | 1 if **either** bit is 1             | Set specific bits ON       |
| XOR         | `^`    | 1 only if bits are **different**      | Toggle bits, swap values   |
| NOT         | `~`    | Flips **all** bits                    | Invert a number            |
| Left Shift  | `<<`   | Multiply by 2 per shift               | Compute powers of 2        |
| Right Shift | `>>`   | Integer-divide by 2 per shift         | Extract bits, fast division|

---

## Bitwise AND (`&`)

Returns 1 only when **both** corresponding bits are 1.

| Bit A | Bit B | A & B |
|-------|-------|-------|
| 0     | 0     | 0     |
| 0     | 1     | 0     |
| 1     | 0     | 0     |
| 1     | 1     | 1     |

```
5  = 0101
3  = 0011
---------
&  = 0001 = 1
```

#### Python

```python
a, b = 5, 3
print(a & b)   # 1  — only the last bit is 1 in both
```

#### C++ (simple)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int a = 5, b = 3;
    cout << (a & b) << "\n";   // 1
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int bitwiseAnd(int a, int b) {
        return a & b;   // 1 only where both bits are 1
    }
};
```

---

## Bitwise OR (`|`)

Returns 1 if **at least one** of the two bits is 1.

| Bit A | Bit B | A \| B |
|-------|-------|--------|
| 0     | 0     | 0      |
| 0     | 1     | 1      |
| 1     | 0     | 1      |
| 1     | 1     | 1      |

```
5  = 0101
3  = 0011
---------
|  = 0111 = 7
```

#### Python

```python
a, b = 5, 3
print(a | b)   # 7  — every position with a 1 in either number is 1
```

#### C++ (simple)

```cpp
int main() {
    int a = 5, b = 3;
    cout << (a | b) << "\n";   // 7
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int bitwiseOr(int a, int b) {
        return a | b;   // turns ON any bit that is 1 in either operand
    }
};
```

---

## Bitwise XOR (`^`)

Returns 1 only when the two bits are **different**.

| Bit A | Bit B | A ^ B |
|-------|-------|-------|
| 0     | 0     | 0     |
| 0     | 1     | 1     |
| 1     | 0     | 1     |
| 1     | 1     | 0     |

```
5  = 0101
3  = 0011
---------
^  = 0110 = 6
```

Key properties:
- `a ^ a = 0` — a number XORed with itself is 0
- `a ^ 0 = a` — XOR with 0 leaves a number unchanged

#### Python

```python
a, b = 5, 3
print(a ^ b)    # 6
print(7 ^ 7)    # 0  (a ^ a = 0)
print(7 ^ 0)    # 7  (a ^ 0 = a)
```

#### C++ (simple)

```cpp
int main() {
    cout << (5 ^ 3) << "\n";   // 6
    cout << (7 ^ 7) << "\n";   // 0
    cout << (7 ^ 0) << "\n";   // 7
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int bitwiseXor(int a, int b) {
        return a ^ b;   // 1 only where bits differ
    }
};
```

---

## Bitwise NOT (`~`)

Flips **every** bit. Because integers use **two's complement**, the result is always `~n = -(n + 1)`.

```
5 (32-bit) = 00000000 00000000 00000000 00000101
~5         = 11111111 11111111 11111111 11111010  = -6
```

#### Python

```python
print(~5)    # -6   (-(5+1))
print(~0)    # -1   (-(0+1))
# Rule: ~n = -(n + 1) in two's complement
```

#### C++ (simple)

```cpp
int main() {
    cout << ~5 << "\n";    // -6
    cout << ~0 << "\n";    // -1
}
```

NOT appears most often in **clear-bit** masks: `num & ~(1 << k)`.

---

## Left Shift (`<<`)

Moves all bits **left** by k positions; empty positions on the right are filled with 0.
Each left shift by 1 **multiplies by 2**: `n << k = n * 2^k`

```
5  = 00000101
5 << 1 = 00001010 = 10
5 << 2 = 00010100 = 20
5 << 3 = 00101000 = 40
```

`1 << n` is a fast way to compute **2^n** and to build bitmasks.

#### Python

```python
a = 5
print(a << 1)    # 10   (5 * 2)
print(a << 2)    # 20   (5 * 4)
print(a << 3)    # 40   (5 * 8)

# 1 << n = 2^n
print(1 << 4)    # 16   (2^4)
```

#### C++ (simple)

```cpp
int main() {
    int a = 5;
    cout << (a << 1) << "\n";   // 10
    cout << (a << 2) << "\n";   // 20
    cout << (a << 3) << "\n";   // 40
    cout << (1 << 4) << "\n";   // 16 (2^4)
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int powerOf2(int n) {
        return 1 << n;    // fast 2^n using left shift
    }
};
```

---

## Right Shift (`>>`)

Moves all bits **right** by k positions. Each right shift by 1 **integer-divides by 2**: `n >> k = n // 2^k`

```
20 = 00010100
20 >> 1 = 00001010 = 10
20 >> 2 = 00000101 = 5
20 >> 3 = 00000010 = 2   (20 / 8 = 2.5 → floor = 2)
```

#### Python

```python
a = 20
print(a >> 1)    # 10   (20 // 2)
print(a >> 2)    # 5    (20 // 4)
print(a >> 3)    # 2    (20 // 8, floor division)
```

#### C++ (simple)

```cpp
int main() {
    int a = 20;
    cout << (a >> 1) << "\n";   // 10
    cout << (a >> 2) << "\n";   // 5
    cout << (a >> 3) << "\n";   // 2
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int fastDivide(int n, int k) {
        return n >> k;    // integer division by 2^k
    }
};
```

---

## Practical Patterns

### 1. Check Even / Odd

The last bit is 1 for odd numbers, 0 for even.

#### Python

```python
n = 7
if (n & 1) == 1:
    print(f"{n} is Odd")    # 7 is Odd
else:
    print(f"{n} is Even")
```

#### C++ (simple)

```cpp
void checkParity(int n) {
    if ((n & 1) == 1)
        cout << n << " is Odd\n";
    else
        cout << n << " is Even\n";
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    bool isOdd(int n) {
        return (n & 1) == 1;   // last bit 1 → odd
    }
};
```

---

### 2. Check if a Specific Bit is Set

To check the **i-th bit** (0-indexed from right): shift 1 left by i positions and AND with the number.
If the result is non-zero, the bit is set.

```
n = 5 = 0101
i = 2
1 << 2 = 0100
5 & 4  = 0100  → non-zero → bit 2 is SET
```

#### Python

```python
n, i = 5, 2
if (n & (1 << i)) != 0:
    print(f"Bit {i} is SET")       # Bit 2 is SET
else:
    print(f"Bit {i} is NOT SET")
```

#### C++ (simple)

```cpp
bool isBitSet(int n, int i) {
    return (n & (1 << i)) != 0;   // mask = 1 shifted to position i
}

int main() {
    cout << isBitSet(5, 2) << "\n";   // 1 (bit 2 of 0101 is set)
    cout << isBitSet(5, 1) << "\n";   // 0 (bit 1 of 0101 is not set)
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    bool isBitSet(int n, int i) {
        return (n & (1 << i)) != 0;   // AND with mask isolates bit i
    }
};
```

---

### 3. Swap Two Numbers Without a Temp Variable (XOR Swap)

XOR swap uses the fact that `a ^ a = 0` to swap values in three steps without extra memory.

```
a = 5 (0101),  b = 3 (0011)
Step 1: a = a ^ b  →  0101 ^ 0011 = 0110  (a = 6)
Step 2: b = a ^ b  →  0110 ^ 0011 = 0101  (b = 5, original a)
Step 3: a = a ^ b  →  0110 ^ 0101 = 0011  (a = 3, original b)
```

#### Python

```python
a, b = 5, 3
a = a ^ b    # a = 6
b = a ^ b    # b = 5  (original a recovered)
a = a ^ b    # a = 3  (original b recovered)
print(f"a = {a}, b = {b}")   # a = 3, b = 5
```

#### C++ (simple)

```cpp
void xorSwap(int& a, int& b) {
    a = a ^ b;    // mix both values into a
    b = a ^ b;    // recover original a into b
    a = a ^ b;    // recover original b into a
}

int main() {
    int a = 5, b = 3;
    xorSwap(a, b);
    cout << "a = " << a << ", b = " << b << "\n";   // a = 3, b = 5
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Swap a and b in-place using XOR (no temp variable)
    void xorSwap(int& a, int& b) {
        if (a != b) {        // guard: XOR of same address would zero out the value
            a ^= b;
            b ^= a;
            a ^= b;
        }
    }
};
```

---

## Quick Reference Summary

| Operation                   | Expression            | Result / Purpose                       |
|-----------------------------|-----------------------|----------------------------------------|
| AND                         | `a & b`               | 1 only where both bits are 1           |
| OR                          | `a \| b`              | 1 where either bit is 1                |
| XOR                         | `a ^ b`               | 1 only where bits differ               |
| NOT                         | `~n`                  | `-(n+1)` — flips all bits              |
| Left shift                  | `n << k`              | `n * 2^k`                              |
| Right shift                 | `n >> k`              | `n // 2^k` (floor)                     |
| Check even/odd              | `n & 1`               | 0 → even, 1 → odd                      |
| Check bit i                 | `(n & (1 << i)) != 0` | True if bit i is set                   |
| Set bit i                   | `n \| (1 << i)`       | Force bit i to 1                       |
| Clear bit i                 | `n & ~(1 << i)`       | Force bit i to 0                       |
| Toggle bit i                | `n ^ (1 << i)`        | Flip bit i                             |
| Swap without temp           | XOR swap (3 steps)    | No extra variable needed               |
| Power of two                | `1 << n`              | 2^n                                    |

---

## Key Takeaways

- **AND** is for checking and masking; **OR** is for setting bits ON.
- **XOR** is the most versatile — cancels duplicates, swaps values, toggles bits.
- **NOT** gives `-(n+1)` due to two's complement; most useful inside mask expressions.
- **Left shift** = multiply by power of 2; **Right shift** = integer divide by power of 2.
- `(num >> k) & 1` extracts the k-th bit — the single most useful extraction pattern.
