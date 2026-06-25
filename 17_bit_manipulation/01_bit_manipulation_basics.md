# Bit Manipulation Basics: Binary and Bitwise Operators

## What is Bit Manipulation?

Every number in a computer is stored as a series of **bits** (0s and 1s). Bit manipulation means working directly with those bits to perform operations faster and more efficiently — often replacing slow loops or expensive arithmetic with a single expression.

Think of each bit as a light switch: on (1) or off (0). Flipping specific switches in a pattern creates a number.

---

## Binary Number System

Binary is **base-2**: digits are only 0 or 1. Each position represents a power of 2.

```
Binary  1 0 1 1
Power   8 4 2 1
Value   8+0+2+1 = 11 (decimal)
```

Common conversions:

| Decimal | Binary |
|---------|--------|
| 1       | 0001   |
| 5       | 0101   |
| 10      | 1010   |
| 15      | 1111   |

A **32-bit integer** uses 32 bits. The rightmost bit is the **Least Significant Bit (LSB)**; the leftmost is the **Most Significant Bit (MSB)**.

```python
print(bin(10))   # 0b1010  (0b prefix means binary)
print(bin(15))   # 0b1111
```

---

## Bitwise Operators Overview

| Operator    | Symbol | Description                          |
|-------------|--------|--------------------------------------|
| AND         | `&`    | 1 only if **both** bits are 1        |
| OR          | `\|`   | 1 if **at least one** bit is 1       |
| XOR         | `^`    | 1 only if the bits are **different** |
| NOT         | `~`    | Flips **all** bits (complement)      |
| Left Shift  | `<<`   | Shifts bits to the left              |
| Right Shift | `>>`   | Shifts bits to the right             |

---

## AND Operator (`&`)

Result bit is 1 only when **both** corresponding bits are 1.

```
5  = 0101
3  = 0011
---------
&  = 0001 = 1
```

**Most common use: check odd/even** — the last bit is 1 for odd, 0 for even.

#### Python

```python
print(5 & 3)    # 1

# Check odd/even — faster than using % 2
num = 6
if num & 1 == 0:
    print("Even")   # Even

num = 7
if num & 1 == 1:
    print("Odd")    # Odd
```

#### C++ (simple)

```cpp
#include <bits/stdc++.h>
using namespace std;

bool isEven(int n) {
    return (n & 1) == 0;   // last bit 0 → even
}

bool isOdd(int n) {
    return (n & 1) == 1;   // last bit 1 → odd
}

int main() {
    cout << (5 & 3) << "\n";          // 1
    cout << isEven(6) << "\n";        // 1 (true)
    cout << isOdd(7)  << "\n";        // 1 (true)
}
```

#### C++ (LeetCode class style)

```cpp
// Utility patterns used inside LeetCode solutions
class Solution {
public:
    // Check if number is even using AND
    bool isEven(int n) {
        return (n & 1) == 0;   // last bit is 0 → even
    }

    // Check if number is odd using AND
    bool isOdd(int n) {
        return (n & 1) == 1;   // last bit is 1 → odd
    }
};
```

---

## OR Operator (`|`)

Result bit is 1 if **at least one** corresponding bit is 1.

```
5  = 0101
3  = 0011
---------
|  = 0111 = 7
```

**Most common use: set (turn on) a specific bit.**

#### Python

```python
print(5 | 3)    # 7

# Set bit at position k
num  = 5        # 0101
k    = 1
mask = 1 << k   # shift 1 to position 1 → 0010
result = num | mask
print(result)   # 7 (0111) — bit 1 is now set
```

#### C++ (simple)

```cpp
int setBit(int num, int k) {
    return num | (1 << k);   // force bit k to 1
}

int main() {
    cout << (5 | 3)       << "\n";  // 7
    cout << setBit(5, 1)  << "\n";  // 7 (bit 1 set in 0101 → 0111)
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Set the bit at position k to 1
    int setBit(int num, int k) {
        return num | (1 << k);   // OR with mask that has only bit k set
    }
};
```

---

## XOR Operator (`^`)

Result is 1 only when the two bits are **different**.

```
5  = 0101
3  = 0011
---------
^  = 0110 = 6
```

**Key properties:**
- `a ^ a = 0` — a number XORed with itself cancels out
- `a ^ 0 = a` — a number XORed with 0 stays the same

These two properties make XOR perfect for **finding the unique number** in a list where every other element appears twice.

#### Python

```python
print(5 ^ 3)    # 6

# Property: a ^ a = 0
print(7 ^ 7)    # 0

# Property: a ^ 0 = a
print(7 ^ 0)    # 7

# Find the unique number — all others appear twice
# XOR all → duplicates cancel (a ^ a = 0), unique survives (x ^ 0 = x)
nums   = [2, 3, 5, 3, 2]
result = 0
for n in nums:
    result ^= n
print(result)   # 5
```

#### C++ (simple)

```cpp
int findUnique(vector<int>& nums) {
    int result = 0;
    for (int n : nums)
        result ^= n;     // duplicates cancel, unique survives
    return result;
}

int main() {
    cout << (5 ^ 3) << "\n";   // 6
    cout << (7 ^ 7) << "\n";   // 0
    cout << (7 ^ 0) << "\n";   // 7

    vector<int> nums = {2, 3, 5, 3, 2};
    cout << findUnique(nums) << "\n";  // 5
}
```

#### C++ (LeetCode class style)

```cpp
// LeetCode #136 — Single Number
// Every element appears twice except one; find the unique element.
// XOR all numbers: duplicates cancel (a^a=0), unique remains (x^0=x).
// Time O(N) | Space O(1)
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int result = 0;
        for (int n : nums)
            result ^= n;   // XOR accumulates; pairs cancel
        return result;
    }
};
```

---

## NOT Operator (`~`)

Flips **all** bits. In Python (and most languages using two's complement): `~n = -(n + 1)`.

```
5  = ...0000 0101
~5 = ...1111 1010  = -6  (two's complement)
```

#### Python

```python
print(~5)    # -6   (two's complement: -(5+1))
print(~0)    # -1   (-(0+1))
# Rule: ~n = -(n + 1)
```

#### C++ (simple)

```cpp
int main() {
    cout << ~5 << "\n";   // -6
    cout << ~0 << "\n";   // -1
}
```

NOT is rarely used alone; it appears in masking patterns (e.g., clearing a bit).

---

## Left Shift (`<<`)

Moves all bits **left** by k positions. Each left shift by 1 **multiplies by 2**.
`n << k  =  n * (2^k)`

```
1 = 0001
1 << 1 = 0010 = 2
1 << 2 = 0100 = 4
1 << 3 = 1000 = 8
```

**Most common use:** create a bitmask targeting bit position k with `1 << k`.

#### Python

```python
print(1 << 1)   # 2
print(1 << 2)   # 4
print(1 << 3)   # 8

# General rule: n << k = n * (2^k)
print(3 << 2)   # 12  (3 * 4)
```

#### C++ (simple)

```cpp
int main() {
    cout << (1 << 1) << "\n";   // 2
    cout << (1 << 2) << "\n";   // 4
    cout << (1 << 3) << "\n";   // 8
    cout << (3 << 2) << "\n";   // 12
}
```

---

## Right Shift (`>>`)

Moves all bits **right** by k positions. Each right shift by 1 **integer-divides by 2**.
`n >> k  =  n // (2^k)`

```
8 = 1000
8 >> 1 = 0100 = 4
8 >> 2 = 0010 = 2
8 >> 3 = 0001 = 1
```

**Most common use:** extract the bit at position k with `(n >> k) & 1`.

#### Python

```python
print(8 >> 1)    # 4
print(8 >> 2)    # 2
print(8 >> 3)    # 1

# General rule: n >> k = n // (2^k)
print(20 >> 2)   # 5  (20 // 4)
```

#### C++ (simple)

```cpp
int main() {
    cout << (8  >> 1) << "\n";   // 4
    cout << (8  >> 2) << "\n";   // 2
    cout << (8  >> 3) << "\n";   // 1
    cout << (20 >> 2) << "\n";   // 5
}
```

---

## Core Bit Tricks

These three patterns — **check / clear / toggle** — form the toolkit for most bit manipulation problems.

### Check if bit k is set

`(num >> k) & 1` — shift the target bit to the LSB position and mask with 1.

#### Python

```python
num = 13    # 1101 in binary
k   = 2

if (num >> k) & 1:
    print(f"Bit {k} is set")      # Bit 2 is set
else:
    print(f"Bit {k} is not set")
```

#### C++ (simple)

```cpp
bool isBitSet(int num, int k) {
    return ((num >> k) & 1) == 1;   // shift bit k to LSB, check if 1
}

int main() {
    cout << isBitSet(13, 2) << "\n";  // 1 (bit 2 of 1101 is set)
    cout << isBitSet(13, 1) << "\n";  // 0 (bit 1 of 1101 is not set)
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    bool isBitSet(int num, int k) {
        return ((num >> k) & 1) == 1;   // shift and mask
    }
};
```

---

### Clear (turn off) bit k

`num & ~(1 << k)` — NOT of the mask forces bit k to 0 while leaving others unchanged.

#### Python

```python
num = 13    # 1101
k   = 2
mask   = ~(1 << k)   # NOT of 0100 = ...11111011
result = num & mask
print(result)         # 9 (1001 — bit 2 cleared)
```

#### C++ (simple)

```cpp
int clearBit(int num, int k) {
    return num & ~(1 << k);   // AND with inverted mask clears bit k
}

int main() {
    cout << clearBit(13, 2) << "\n";  // 9  (1101 → 1001)
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int clearBit(int num, int k) {
        return num & ~(1 << k);   // force bit k to 0
    }
};
```

---

### Toggle (flip) bit k

`num ^ (1 << k)` — XOR with the mask flips only bit k (0→1 or 1→0).

#### Python

```python
num    = 13    # 1101
k      = 0    # toggle the last bit
result = num ^ (1 << k)
print(result)  # 12 (1100 — last bit flipped from 1 to 0)
```

#### C++ (simple)

```cpp
int toggleBit(int num, int k) {
    return num ^ (1 << k);   // XOR flips only bit k
}

int main() {
    cout << toggleBit(13, 0) << "\n";  // 12 (1101 → 1100)
    cout << toggleBit(12, 0) << "\n";  // 13 (1100 → 1101)
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int toggleBit(int num, int k) {
        return num ^ (1 << k);   // XOR with mask flips bit k
    }
};
```

---

## Summary Table

| Operation           | Expression          | Effect                              |
|---------------------|---------------------|-------------------------------------|
| Check odd/even      | `n & 1`             | 1 → odd, 0 → even                   |
| Check bit k         | `(n >> k) & 1`      | 1 if bit k is set, else 0           |
| Set bit k           | `n \| (1 << k)`     | Force bit k to 1                    |
| Clear bit k         | `n & ~(1 << k)`     | Force bit k to 0                    |
| Toggle bit k        | `n ^ (1 << k)`      | Flip bit k                          |
| Multiply by 2^k     | `n << k`            | Left shift                          |
| Divide by 2^k       | `n >> k`            | Right shift (integer division)      |
| Cancel duplicates   | `a ^ a = 0`         | XOR of equal values is 0            |
| Identity            | `a ^ 0 = a`         | XOR with 0 unchanged                |
| NOT / complement    | `~n = -(n+1)`       | Two's complement                    |

---

## Why Bit Manipulation Matters

- `n & 1` is faster than `n % 2` for odd/even checks
- `n << k` / `n >> k` are faster than multiplication/division by powers of 2
- **Subsets, flags, permissions** — real systems (OS, databases) rely on bit masks
- **Interview problems** — XOR tricks, subset enumeration with masks, power-of-two checks all build on these basics
