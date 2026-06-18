# Palindrome Problems — String Checks and Variations

> **One-line summary:**
> A palindrome reads the same forwards and backwards — check it with reverse-and-compare (simple) or two pointers (optimal), then build on that to solve substring and variation problems.

---

## Table of Contents

1. [What is a Palindrome?](#1-what-is-a-palindrome)
2. [Basic Palindrome Check](#2-basic-palindrome-check)
3. [Method 1 — Reverse and Compare](#3-method-1--reverse-and-compare)
4. [Method 2 — Two Pointer Technique](#4-method-2--two-pointer-technique)
5. [Comparison of Methods](#5-comparison-of-methods)
6. [Handling Edge Cases](#6-handling-edge-cases)
7. [Palindrome Variations](#7-palindrome-variations)
   - [Variation 1 — Longest Palindromic Substring](#variation-1--longest-palindromic-substring)
   - [Variation 2 — Count Palindromic Substrings](#variation-2--count-palindromic-substrings)
   - [Variation 3 — Valid Palindrome with One Deletion](#variation-3--valid-palindrome-with-one-deletion)
   - [Variation 4 — Check if a Number is a Palindrome](#variation-4--check-if-a-number-is-a-palindrome)
8. [Dry Run Example](#8-dry-run-example)
9. [Common Mistakes to Avoid](#9-common-mistakes-to-avoid)
10. [Key Takeaways](#10-key-takeaways)
11. [FAQs](#11-faqs)

---

## 1. What is a Palindrome?

Have you ever noticed that **"racecar"** reads the same forwards and backwards? That is a palindrome.

> **Definition:** A palindrome is a string that looks identical whether you read it from left to right or right to left.

Common examples: `"madam"`, `"level"`, `"noon"`, `"racecar"`

```
"racecar"
 r a c e c a r
 ↑           ↑   r == r  ✓
   ↑       ↑     a == a  ✓
     ↑   ↑       c == c  ✓
       ↑         e  (center — always matches itself)

→ PALINDROME ✓

"hello"
 h e l l o
 ↑       ↑   h ≠ o  ✗

→ NOT a palindrome ✗
```

---

## 2. Basic Palindrome Check

There are two main approaches to check if a string is a palindrome:

1. **Reverse and compare** — simple, but uses extra space
2. **Two pointers** — optimal, O(1) space

---

## 3. Method 1 — Reverse and Compare

Create a reversed copy of the string and check if it equals the original.

```python
# Python — Reverse and Compare
def is_palindrome(s):
    reversed_s = s[::-1]   # reverse the string
    return s == reversed_s


# Example usage
print(is_palindrome("racecar"))   # Output: True
print(is_palindrome("hello"))     # Output: False
print(is_palindrome("madam"))     # Output: True
```

```cpp
// C++ — Reverse and Compare
#include <iostream>
#include <string>
#include <algorithm>

bool is_palindrome(const std::string& s) {
    std::string reversed = s;
    std::reverse(reversed.begin(), reversed.end());
    return s == reversed;
}

int main() {
    std::cout << is_palindrome("racecar") << std::endl;   // Output: 1 (true)
    std::cout << is_palindrome("hello") << std::endl;     // Output: 0 (false)
}
```

> **Time complexity:** O(n) — reversing and comparing each visit every character once.  
> **Space complexity:** O(n) — a new reversed string is created in memory.

---

## 4. Method 2 — Two Pointer Technique

Place one pointer at the start and one at the end. Move them inward — if any pair of characters does not match, it is not a palindrome.

```
"level"

left=0  right=4  →  l == l  ✓  → left++, right--
left=1  right=3  →  e == e  ✓  → left++, right--
left=2  right=2  →  left >= right → stop

All pairs matched → PALINDROME ✓
```

```python
# Python — Two Pointer
def is_palindrome_two_pointer(s):
    left = 0
    right = len(s) - 1

    while left < right:
        if s[left] != s[right]:
            return False    # mismatch found — not a palindrome
        left += 1
        right -= 1

    return True


# Example usage
print(is_palindrome_two_pointer("level"))    # Output: True
print(is_palindrome_two_pointer("python"))   # Output: False
```

```cpp
// C++ — Two Pointer
#include <iostream>
#include <string>

bool is_palindrome_two_pointer(const std::string& s) {
    int left = 0;
    int right = s.length() - 1;

    while (left < right) {
        if (s[left] != s[right]) {
            return false;   // mismatch — not a palindrome
        }
        left++;
        right--;
    }
    return true;
}

int main() {
    std::cout << is_palindrome_two_pointer("level") << std::endl;    // Output: 1 (true)
    std::cout << is_palindrome_two_pointer("python") << std::endl;   // Output: 0 (false)
}
```

> **Time complexity:** O(n) — at most n/2 comparisons.  
> **Space complexity:** O(1) — only two integer pointers, no extra string created.

---

## 5. Comparison of Methods

| Approach | Time Complexity | Space Complexity | Best For |
|---|---|---|---|
| Reverse and Compare | O(n) | O(n) | Quick scripts, readability |
| Two Pointer | O(n) | O(1) | Interviews, memory efficiency |

Both have the same time complexity. The two-pointer method wins on space because it avoids creating a new string in memory. **In interviews, always use two pointers** and explain why.

---

## 6. Handling Edge Cases

### Empty string and single character

An empty string and a single character are both palindromes by definition — there is nothing to contradict itself. The two-pointer loop never runs (`left >= right` from the start), so it correctly returns `True`.

```python
print(is_palindrome_two_pointer(""))    # Output: True
print(is_palindrome_two_pointer("a"))   # Output: True
```

### Case-insensitive check

`"Racecar"` should still be a palindrome if the problem ignores case. Convert to lowercase before checking.

```python
# Python — Case-insensitive palindrome
def is_palindrome_ignore_case(s):
    s = s.lower()   # normalise to lowercase
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True


print(is_palindrome_ignore_case("Racecar"))   # Output: True
print(is_palindrome_ignore_case("Level"))     # Output: True
```

### Alphanumeric-only check

Some problems ask you to ignore spaces and special characters. `"A man, a plan, a canal: Panama"` is a classic example.

```python
# Python — Alphanumeric-only palindrome
def is_palindrome_alphanumeric(s):
    # Keep only letters and digits, convert to lowercase
    cleaned = [c.lower() for c in s if c.isalnum()]
    left, right = 0, len(cleaned) - 1
    while left < right:
        if cleaned[left] != cleaned[right]:
            return False
        left += 1
        right -= 1
    return True


print(is_palindrome_alphanumeric("A man, a plan, a canal: Panama"))   # Output: True
print(is_palindrome_alphanumeric("race a car"))                        # Output: False
```

> **Interview tip:** Always clarify the constraints — does the problem ignore case? spaces? special characters? These details completely change your approach.

---

## 7. Palindrome Variations

### Variation 1 — Longest Palindromic Substring

Find the longest contiguous substring of a string that is a palindrome.

```
Input:  "babad"
Output: "bab"  (or "aba" — both are valid)

Input:  "cbbd"
Output: "bb"
```

**The trick — Expand Around Center:**  
Every palindrome has a center. Treat each character (and each gap between characters) as a potential center, then expand outward as long as characters match.

Two cases at every index `i`:
- **Odd length** — center is a single character at `i`
- **Even length** — center is between `i` and `i+1`

```
"racecar"
         r  a  c  e  c  a  r
Odd:     0  1  2  3  4  5  6
         ↑              center at 3 (e) expands to full string

"abba"
         a  b  b  a
Even:    0  1  2  3
                center between 1 and 2 (b,b) expands to full string
```

```python
# Python — Longest Palindromic Substring
def expand_around_center(s, left, right):
    # Expand outward while characters match
    while left >= 0 and right < len(s) and s[left] == s[right]:
        left -= 1
        right += 1
    # left and right have moved one step past the palindrome
    return s[left + 1:right]


def longest_palindromic_substring(s):
    if not s:
        return ""

    longest = ""

    for i in range(len(s)):
        odd_palindrome  = expand_around_center(s, i, i)       # odd length
        even_palindrome = expand_around_center(s, i, i + 1)   # even length

        if len(odd_palindrome) > len(longest):
            longest = odd_palindrome
        if len(even_palindrome) > len(longest):
            longest = even_palindrome

    return longest


print(longest_palindromic_substring("babad"))     # Output: bab
print(longest_palindromic_substring("cbbd"))      # Output: bb
print(longest_palindromic_substring("racecar"))   # Output: racecar
```

> **Time complexity:** O(n²) — n centers, each expands up to O(n).  
> **Space complexity:** O(1) — no extra data structures.

---

### Variation 2 — Count Palindromic Substrings

Count how many palindromic substrings exist in the string. Every single character counts as one.

```
"abc"  → "a", "b", "c"              → 3
"aaa"  → "a"×3, "aa"×2, "aaa"×1    → 6
"abba" → "a"×2, "b"×2, "bb", "abba" → 6
```

```python
# Python — Count all palindromic substrings
def count_palindromic_substrings(s):
    count = 0

    def expand_and_count(left, right):
        nonlocal count
        while left >= 0 and right < len(s) and s[left] == s[right]:
            count += 1   # every valid expansion is one palindrome
            left -= 1
            right += 1

    for i in range(len(s)):
        expand_and_count(i, i)       # odd length centered at i
        expand_and_count(i, i + 1)   # even length centered between i and i+1

    return count


print(count_palindromic_substrings("abc"))    # Output: 3
print(count_palindromic_substrings("aaa"))    # Output: 6
print(count_palindromic_substrings("abba"))   # Output: 6
```

> **Time complexity:** O(n²).  
> **Space complexity:** O(1).

---

### Variation 3 — Valid Palindrome with One Deletion

Check if a string can become a palindrome by removing **at most one** character.

```
"abca"  → remove 'c' → "aba"  → palindrome ✓ → True
"abcde" → no single removal makes it a palindrome → False
```

**The idea:** Use two pointers. When a mismatch is found, try two options:
- Skip the **left** character and check if the rest is a palindrome
- Skip the **right** character and check if the rest is a palindrome

If either works, return `True`.

```python
# Python — Valid Palindrome with at most one deletion
def valid_palindrome_one_delete(s):

    def is_palindrome_range(s, left, right):
        # Check if s[left..right] is a palindrome
        while left < right:
            if s[left] != s[right]:
                return False
            left += 1
            right -= 1
        return True

    left, right = 0, len(s) - 1

    while left < right:
        if s[left] != s[right]:
            # Mismatch — try skipping left char OR right char
            return (is_palindrome_range(s, left + 1, right) or
                    is_palindrome_range(s, left, right - 1))
        left += 1
        right -= 1

    return True   # already a palindrome, no deletion needed


print(valid_palindrome_one_delete("abca"))     # Output: True   (remove 'c')
print(valid_palindrome_one_delete("racecar"))  # Output: True   (already palindrome)
print(valid_palindrome_one_delete("abcde"))    # Output: False
```

> **Time complexity:** O(n) — two pointer scan plus at most one extra O(n) check.  
> **Space complexity:** O(1).

---

### Variation 4 — Check if a Number is a Palindrome

Numbers can be palindromes too — `121` is, `123` is not. Convert the number to a string and apply the two-pointer check.

```
121  → "121"  → palindrome ✓
-121 → negative → always False (minus sign breaks symmetry)
1221 → "1221" → palindrome ✓
```

```python
# Python — Number palindrome check
def is_number_palindrome(n):
    if n < 0:
        return False   # negative numbers are never palindromes

    s = str(n)   # convert number to string
    left, right = 0, len(s) - 1

    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1

    return True


print(is_number_palindrome(121))    # Output: True
print(is_number_palindrome(-121))   # Output: False
print(is_number_palindrome(1221))   # Output: True
print(is_number_palindrome(123))    # Output: False
```

---

## 8. Dry Run Example

Two-pointer palindrome check on `"abcba"`:

| Step | left | right | s[left] | s[right] | Match? | Action |
|---|---|---|---|---|---|---|
| 1 | 0 | 4 | `a` | `a` | Yes | left++, right-- |
| 2 | 1 | 3 | `b` | `b` | Yes | left++, right-- |
| 3 | 2 | 2 | `c` | `c` | left == right | stop |

All pairs matched → `"abcba"` is a **palindrome**.

This kind of step-by-step trace is very useful during interviews to show your thought process clearly.

---

## 9. Common Mistakes to Avoid

1. **Not handling empty strings or single characters** — the two-pointer loop handles these naturally, but verify your code does not crash on them.

2. **Ignoring case when the problem requires it** — `"Racecar"` ≠ `"racecar"` unless you call `.lower()` first.

3. **Not stripping spaces or special characters** — `"A man, a plan, a canal: Panama"` requires cleaning before the palindrome check.

4. **Forgetting the two cases in expand-around-center** — you must check both odd-length (center at `i`) and even-length (center between `i` and `i+1`). Missing either gives wrong answers.

5. **Using O(n) space unnecessarily** — slicing creates a new string. If the interviewer asks for O(1) space, use the two-pointer approach instead.

---

## 10. Key Takeaways

- A string is a **palindrome** if it reads the same forwards and backwards.
- **Two-pointer** is the preferred approach — O(n) time, O(1) space. Reverse-and-compare is simpler but uses O(n) space.
- Edge cases: empty string and single character are always palindromes; handle case and alphanumeric-only requirements by normalising the string first.
- **Expand around center** is the key technique for substring palindrome problems (longest palindromic substring, count palindromic substrings).
- For **one deletion** problems: when a mismatch is found, try skipping the left or right character and check the remaining range.
- Always clarify constraints before coding: case sensitivity, allowed characters, deletions — these completely change the approach.

---

## 11. FAQs

**Q: Is an empty string considered a palindrome?**  
Yes. By definition, an empty string is a palindrome — there are no characters to contradict the property. The two-pointer loop never runs when `left >= right`, so it correctly returns `True`.

**Q: What is the difference between a palindromic substring and a palindromic subsequence?**  
A **substring** is a contiguous part of the string, like `"aba"` in `"xabayz"`. A **subsequence** does not need to be contiguous — you can skip characters. For example, `"aaa"` is a palindromic subsequence of `"axaybza"`. This file focuses on substrings.

**Q: Which palindrome approach should I use in interviews?**  
Use the **two-pointer technique** for basic palindrome checks — O(1) space. For longest palindromic substring, use **expand around center**. State both options and explain your reasoning.

**Q: Why must we check two cases (odd and even) in expand-around-center?**  
Palindromes can have either a single character at the center (`"aba"` → center is `b`) or two identical characters at the center (`"abba"` → center is `bb`). Checking only one case misses the other type entirely.

**Q: Can a negative number be a palindrome?**  
No. The minus sign at the front means the number can never read the same from both ends. Always return `False` immediately for negative inputs.
