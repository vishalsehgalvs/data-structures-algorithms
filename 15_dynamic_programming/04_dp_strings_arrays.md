# Dynamic Programming on Strings and Arrays: Beginner Guide

## What is DP on Strings and Arrays?

Most string DP problems involve two strings (or one string compared with itself). You create a **2D table** where rows represent characters of one string and columns represent characters of the other. Each cell stores the answer to a subproblem.

**Core pattern:**

- Characters **match** → build on the diagonal cell (`dp[i-1][j-1] + 1`)
- Characters **don't match** → take the best from the cell above or to the left

This pattern appears in LCS, Edit Distance, LPS, and many more.

---

## Longest Common Subsequence (LCS)

The LCS of two strings is the longest sequence of characters that appears in both strings **in the same order** (not necessarily contiguous).

Example: LCS of `"ABCDE"` and `"ACE"` = `"ACE"` (length 3).

#### Python

```python
def lcs(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]        # characters match — extend
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])  # take best of above or left

    return dp[m][n]

print(lcs("ABCDE", "ACE"))      # 3
print(lcs("AGGTAB", "GXTXAYB")) # 4
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

int lcs(const string& s1, const string& s2) {
    int m = s1.size(), n = s2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1[i-1] == s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];       // match — extend diagonal
            else
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]); // no match — best neighbor
        }
    }
    return dp[m][n];
}

int main() {
    cout << lcs("ABCDE", "ACE")       << "\n";   // 3
    cout << lcs("AGGTAB", "GXTXAYB") << "\n";   // 4
    return 0;
}
// Time: O(m * n) | Space: O(m * n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #1143 — Longest Common Subsequence
    int longestCommonSubsequence(string text1, string text2) {
        int m = text1.size(), n = text2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1[i-1] == text2[j-1])
                    dp[i][j] = 1 + dp[i-1][j-1];           // match
                else
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]); // no match
            }
        }
        return dp[m][n];
    }
};
// Time: O(m * n) | Space: O(m * n) — reducible to O(n) with two-row optimization
```

---

## Edit Distance (Levenshtein Distance)

Minimum number of operations (**insert, delete, replace**) to convert string `s1` into `s2`.

**Recurrence:**

- `s1[i-1] == s2[j-1]` → `dp[i][j] = dp[i-1][j-1]` (no cost)
- Otherwise → `dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])`  
  (delete from s1 / insert into s1 / replace)

**Base cases:** converting to/from an empty string costs as many operations as there are characters.

#### Python

```python
def edit_distance(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(m + 1): dp[i][0] = i    # delete all chars of s1
    for j in range(n + 1): dp[0][j] = j    # insert all chars of s2

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1]            # no operation needed
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],         # delete from s1
                    dp[i][j-1],         # insert into s1
                    dp[i-1][j-1]        # replace
                )

    return dp[m][n]

print(edit_distance("horse", "ros"))            # 3
print(edit_distance("intention", "execution"))  # 5
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

int editDistance(const string& s1, const string& s2) {
    int m = s1.size(), n = s2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

    for (int i = 0; i <= m; i++) dp[i][0] = i;  // delete all from s1
    for (int j = 0; j <= n; j++) dp[0][j] = j;  // insert all from s2

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1[i-1] == s2[j-1])
                dp[i][j] = dp[i-1][j-1];           // no cost
            else
                dp[i][j] = 1 + min({dp[i-1][j],    // delete
                                    dp[i][j-1],     // insert
                                    dp[i-1][j-1]}); // replace
        }
    }
    return dp[m][n];
}

int main() {
    cout << editDistance("horse", "ros")            << "\n";  // 3
    cout << editDistance("intention", "execution")  << "\n";  // 5
    return 0;
}
// Time: O(m * n) | Space: O(m * n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #72 — Edit Distance
    int minDistance(string word1, string word2) {
        int m = word1.size(), n = word2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1));

        for (int i = 0; i <= m; i++) dp[i][0] = i;
        for (int j = 0; j <= n; j++) dp[0][j] = j;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1[i-1] == word2[j-1])
                    dp[i][j] = dp[i-1][j-1];
                else
                    dp[i][j] = 1 + min({dp[i-1][j], dp[i][j-1], dp[i-1][j-1]});
            }
        }
        return dp[m][n];
    }
};
// Time: O(m * n) | Space: O(m * n)
```

---

## Longest Palindromic Subsequence (LPS)

The LPS of a string is the longest subsequence that reads the same forwards and backwards.

**Key insight:** LPS of string `s` = LCS of `s` and `reverse(s)`.

Example: `"bbbab"` reversed = `"babbb"`. LCS = `"bbbb"` (length 4).

#### Python

```python
def longest_palindromic_subsequence(s):
    return lcs(s, s[::-1])      # LCS of s and its reverse

print(longest_palindromic_subsequence("bbbab"))     # 4
print(longest_palindromic_subsequence("cbbd"))      # 2
```

#### C++ (simple)

```cpp
#include <string>
#include <algorithm>
using namespace std;

// Reuse lcs() function from above
int longestPalindromicSubseq(const string& s) {
    string rev = s;
    reverse(rev.begin(), rev.end());
    return lcs(s, rev);     // LCS of s and reverse(s)
}
// "bbbab" → lcs("bbbab","babbb") = 4 ("bbbb")
// Time: O(n^2) | Space: O(n^2)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #516 — Longest Palindromic Subsequence
    int longestPalindromeSubseq(string s) {
        string rev = s;
        reverse(rev.begin(), rev.end());

        int n = s.size();
        vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (s[i-1] == rev[j-1])
                    dp[i][j] = 1 + dp[i-1][j-1];
                else
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
        return dp[n][n];
    }
};
// Time: O(n^2) | Space: O(n^2)
```

---

## Minimum Insertions to Make a Palindrome

Formula: `min_insertions = len(s) - LPS(s)`

Characters **not** in the LPS need to be inserted. For `"abcd"` (LPS=1): need 3 insertions → `"abcdcba"`.

#### Python

```python
def min_insertions_palindrome(s):
    return len(s) - longest_palindromic_subsequence(s)

print(min_insertions_palindrome("ab"))      # 1
print(min_insertions_palindrome("abcd"))    # 3
print(min_insertions_palindrome("aab"))     # 1
```

#### C++ (simple)

```cpp
int minInsertionsPalindrome(const string& s) {
    return s.size() - longestPalindromicSubseq(s);  // n - LPS
}
// Time: O(n^2) | Space: O(n^2)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #1312 — Minimum Insertion Steps to Make a String Palindrome
    int minInsertions(string s) {
        return s.size() - longestPalindromeSubseq(s);   // n - LPS
    }

private:
    int longestPalindromeSubseq(string& s) {
        string rev = s;
        reverse(rev.begin(), rev.end());
        int n = s.size();
        vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                dp[i][j] = (s[i-1] == rev[j-1]) ?
                    1 + dp[i-1][j-1] : max(dp[i-1][j], dp[i][j-1]);
        return dp[n][n];
    }
};
// Time: O(n^2) | Space: O(n^2)
```

---

## Longest Increasing Subsequence (LIS)

The LIS of an array is the longest subsequence where each element is **strictly greater** than the previous.

Example: `[10, 9, 2, 5, 3, 7, 101, 18]` → LIS = `[2, 3, 7, 18]` or `[2, 5, 7, 101]`, length **4**.

`dp[i]` = length of LIS ending at index `i`.

### Dry Run on [2, 5, 3, 7]

| Index | Value | dp[i] | Reason                                     |
| ----- | ----- | ----- | ------------------------------------------ |
| 0     | 2     | 1     | No previous elements                       |
| 1     | 5     | 2     | 5 > 2, extend dp[0]+1                      |
| 2     | 3     | 2     | 3 > 2, extend dp[0]+1                      |
| 3     | 7     | 3     | 7 > 5 and 7 > 3, max(dp[1]+1, dp[2]+1) = 3 |

Answer = max(1, 2, 2, 3) = **3** → LIS is `[2, 3, 7]` or `[2, 5, 7]`

#### Python

```python
def lis(arr):
    n = len(arr)
    dp = [1] * n                    # every element is an LIS of length 1 alone

    for i in range(1, n):
        for j in range(i):
            if arr[j] < arr[i]:     # arr[i] can extend the subsequence ending at j
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)

print(lis([10, 9, 2, 5, 3, 7, 101, 18]))   # 4
print(lis([0, 1, 0, 3, 2, 3]))              # 4
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n^2) LIS using DP
int lis(vector<int>& arr) {
    int n = arr.size();
    vector<int> dp(n, 1);       // each element is LIS of length 1 alone

    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (arr[j] < arr[i])
                dp[i] = max(dp[i], dp[j] + 1);  // extend subsequence
        }
    }
    return *max_element(dp.begin(), dp.end());
}

int main() {
    vector<int> a = {10, 9, 2, 5, 3, 7, 101, 18};
    cout << lis(a) << "\n";     // 4
    return 0;
}
// Time: O(n^2) | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #300 — Longest Increasing Subsequence
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 1);       // dp[i] = LIS ending at index i

        for (int i = 1; i < n; i++)
            for (int j = 0; j < i; j++)
                if (nums[j] < nums[i])
                    dp[i] = max(dp[i], dp[j] + 1);

        return *max_element(dp.begin(), dp.end());
    }
};
// Time: O(n^2) | Space: O(n)
// Note: O(n log n) solution exists using binary search + patience sorting
```

---

## Reference Table — Key DP Problems

| Problem                   | Input       | Key Idea                                            | Time   |
| ------------------------- | ----------- | --------------------------------------------------- | ------ |
| LCS                       | Two strings | Match chars → diagonal; no match → max(above, left) | O(m×n) |
| Edit Distance             | Two strings | Insert / delete / replace operations                | O(m×n) |
| LPS                       | One string  | LCS(s, reverse(s))                                  | O(n²)  |
| Min Insertions Palindrome | One string  | n − LPS(s)                                          | O(n²)  |
| LIS                       | One array   | Extend subsequences from all previous elements      | O(n²)  |

---

## Tips for String/Array DP Problems

1. **Define `dp[i]` or `dp[i][j]` in plain English first** before writing any code.
2. **Start with base cases:** empty strings, single characters, or a single element.
3. **Draw the table by hand** on a small example — the recurrence becomes obvious.
4. Ask: is this a disguised LCS, LIS, or knapsack problem?
5. When you see "minimum operations" or "maximum length" → think DP first.

---

## Key Takeaways

- **LCS** is the building block — LPS and Min Insertions both reduce to it.
- **Edit distance** uses three neighbor cells: delete (above), insert (left), replace (diagonal).
- **LPS** = `LCS(s, reverse(s))` — a beautiful reduction.
- **LIS** tracks the best subsequence ending at each index; scan all previous elements.
- Space can be reduced to O(n) for most 2D DP problems by keeping only two rows.

---

## FAQ

**Is LIS related to LCS?**

Yes — LIS can be found using LCS(arr, sorted(arr)), but the direct DP approach is simpler. An O(n log n) LIS also exists using binary search + patience sorting.

**Where is edit distance used in real life?**

Spell checkers, autocorrect, DNA sequence alignment, plagiarism detection. One of the most practical DP algorithms.

**Can we reduce space in string DP problems?**

Yes. For LCS and edit distance, you only need the current and previous rows at any time — reduces space from O(m×n) to O(n).
