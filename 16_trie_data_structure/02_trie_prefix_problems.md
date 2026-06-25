# Trie Data Structure: Implement and Solve Prefix Problems

## Quick Recap: Trie Node and Core Operations

A Trie node holds 26 child pointers (one per letter a–z) and a flag marking the end of a complete word. Insert/search/startsWith all run in **O(L)** where L is the word/prefix length.

### Core Trie Class

#### Python

```python
class TrieNode:
    def __init__(self):
        self.children = [None] * 26   # one slot per letter a-z
        self.is_end   = False          # True if a word ends at this node

class Trie:
    def __init__(self):
        self.root = TrieNode()         # root is an empty node

    def insert(self, word):
        node = self.root
        for char in word:
            idx = ord(char) - ord('a')        # 'a'->0, 'b'->1, ...
            if node.children[idx] is None:
                node.children[idx] = TrieNode()   # create node if missing
            node = node.children[idx]
        node.is_end = True                    # mark end of word

    def search(self, word):
        node = self.root
        for char in word:
            idx = ord(char) - ord('a')
            if node.children[idx] is None:
                return False                  # path broken → word absent
            node = node.children[idx]
        return node.is_end                    # must be a complete word

    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            idx = ord(char) - ord('a')
            if node.children[idx] is None:
                return False                  # prefix path doesn't exist
            node = node.children[idx]
        return True                           # prefix found (word or partial)

# --- Test ---
trie = Trie()
trie.insert("apple"); trie.insert("app"); trie.insert("bat")

print(trie.search("apple"))      # True
print(trie.search("app"))        # True
print(trie.search("ap"))         # False — not a complete word
print(trie.starts_with("ap"))    # True
print(trie.starts_with("ba"))    # True
print(trie.starts_with("cat"))   # False
```

#### C++ (simple)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TrieNode {
    TrieNode* children[26];
    bool isEnd;
    TrieNode() {
        fill(children, children + 26, nullptr);
        isEnd = false;
    }
};

void insert(TrieNode* root, const string& word) {
    TrieNode* node = root;
    for (char c : word) {
        int idx = c - 'a';
        if (!node->children[idx])
            node->children[idx] = new TrieNode();   // create node if missing
        node = node->children[idx];
    }
    node->isEnd = true;                             // mark end of word
}

bool search(TrieNode* root, const string& word) {
    TrieNode* node = root;
    for (char c : word) {
        int idx = c - 'a';
        if (!node->children[idx]) return false;     // path broken
        node = node->children[idx];
    }
    return node->isEnd;                             // must be a complete word
}

bool startsWith(TrieNode* root, const string& prefix) {
    TrieNode* node = root;
    for (char c : prefix) {
        int idx = c - 'a';
        if (!node->children[idx]) return false;     // prefix absent
        node = node->children[idx];
    }
    return true;                                    // prefix exists
}

int main() {
    TrieNode* root = new TrieNode();
    insert(root, "apple"); insert(root, "app"); insert(root, "bat");

    cout << search(root, "apple")      << "\n"; // 1
    cout << search(root, "app")        << "\n"; // 1
    cout << search(root, "ap")         << "\n"; // 0
    cout << startsWith(root, "ap")     << "\n"; // 1
    cout << startsWith(root, "ba")     << "\n"; // 1
    cout << startsWith(root, "cat")    << "\n"; // 0
}
```

#### C++ (LeetCode class style)

```cpp
// LeetCode #208 — Implement Trie (Prefix Tree)
class TrieNode {
public:
    TrieNode* children[26];
    bool isEnd;
    TrieNode() {
        fill(children, children + 26, nullptr);
        isEnd = false;
    }
};

class Trie {
    TrieNode* root;
public:
    Trie() { root = new TrieNode(); }

    void insert(string word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEnd = true;
    }

    bool search(string word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return node->isEnd;
    }

    bool startsWith(string prefix) {
        TrieNode* node = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return true;
    }
};
```

---

## Problem 1: Count Words With a Given Prefix

Given a list of words and a prefix, count how many words begin with that prefix.

**Key idea:** Store a `count` inside each node. Every time a word passes through a node during insert, increment its count. To answer a prefix query, traverse to the end of the prefix and return its node's count.

#### Python

```python
class TrieNodeCount:
    def __init__(self):
        self.children = [None] * 26
        self.is_end   = False
        self.count    = 0   # how many words pass through this node

class TrieWithCount:
    def __init__(self):
        self.root = TrieNodeCount()

    def insert(self, word):
        node = self.root
        for char in word:
            idx = ord(char) - ord('a')
            if node.children[idx] is None:
                node.children[idx] = TrieNodeCount()
            node = node.children[idx]
            node.count += 1           # increment at every node along the path
        node.is_end = True

    def count_with_prefix(self, prefix):
        node = self.root
        for char in prefix:
            idx = ord(char) - ord('a')
            if node.children[idx] is None:
                return 0              # no words with this prefix
            node = node.children[idx]
        return node.count             # words that passed through this node

# --- Test ---
trie = TrieWithCount()
words = ["apple", "app", "apt", "bat", "ball", "ape"]
for w in words:
    trie.insert(w)

print(trie.count_with_prefix("ap"))   # apple, app, apt, ape → 4
print(trie.count_with_prefix("ba"))   # bat, ball → 2
print(trie.count_with_prefix("ca"))   # 0
```

#### C++ (simple)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TrieNodeCount {
    TrieNodeCount* children[26];
    bool isEnd;
    int count;   // words passing through this node
    TrieNodeCount() {
        fill(children, children + 26, nullptr);
        isEnd = false;
        count = 0;
    }
};

void insertCount(TrieNodeCount* root, const string& word) {
    TrieNodeCount* node = root;
    for (char c : word) {
        int idx = c - 'a';
        if (!node->children[idx])
            node->children[idx] = new TrieNodeCount();
        node = node->children[idx];
        node->count++;               // increment at every node
    }
    node->isEnd = true;
}

int countWithPrefix(TrieNodeCount* root, const string& prefix) {
    TrieNodeCount* node = root;
    for (char c : prefix) {
        int idx = c - 'a';
        if (!node->children[idx]) return 0;   // prefix absent
        node = node->children[idx];
    }
    return node->count;                        // words through this prefix node
}

int main() {
    TrieNodeCount* root = new TrieNodeCount();
    vector<string> words = {"apple","app","apt","bat","ball","ape"};
    for (auto& w : words) insertCount(root, w);

    cout << countWithPrefix(root, "ap") << "\n";  // 4
    cout << countWithPrefix(root, "ba") << "\n";  // 2
    cout << countWithPrefix(root, "ca") << "\n";  // 0
}
```

#### C++ (LeetCode class style)

```cpp
// LeetCode #2185 — Counting Words With a Given Prefix (Trie approach)
class TrieNodeCount {
public:
    TrieNodeCount* children[26];
    bool isEnd;
    int count;
    TrieNodeCount() {
        fill(children, children + 26, nullptr);
        isEnd = false;
        count = 0;
    }
};

class Solution {
    TrieNodeCount* root;

    void insert(const string& word) {
        TrieNodeCount* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNodeCount();
            node = node->children[idx];
            node->count++;               // track how many words pass through
        }
        node->isEnd = true;
    }

    int queryPrefix(const string& prefix) {
        TrieNodeCount* node = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!node->children[idx]) return 0;
            node = node->children[idx];
        }
        return node->count;
    }

public:
    int prefixCount(vector<string>& words, string pref) {
        root = new TrieNodeCount();
        for (auto& w : words) insert(w);    // build trie from all words
        return queryPrefix(pref);            // query prefix in O(L)
    }
};
```

---

## Problem 2: Longest Common Prefix

Given a list of words, find the longest prefix shared by **all** words.

**Key idea:** Insert all words into a Trie. Starting at the root, keep walking as long as the current node has **exactly one child** and does **not** mark the end of any word. The path walked is the common prefix.

**Example:** `["flower", "flow", "flight"]`

- Insert all three → common path starts at root
- After `f`, `l` → next nodes branch to `o` (flower/flow) and `i` (flight) — two children, stop
- Longest common prefix = `"fl"`

#### Python

```python
class TrieNode:
    def __init__(self):
        self.children = [None] * 26
        self.is_end   = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            idx = ord(char) - ord('a')
            if node.children[idx] is None:
                node.children[idx] = TrieNode()
            node = node.children[idx]
        node.is_end = True

    def longest_common_prefix(self):
        node   = self.root
        prefix = ""
        while True:
            # collect indices of non-null children
            non_null = [i for i in range(26) if node.children[i] is not None]
            # stop if there's branching or the node marks a word end
            if len(non_null) != 1 or node.is_end:
                break
            idx     = non_null[0]
            prefix += chr(idx + ord('a'))   # convert index back to char
            node    = node.children[idx]
        return prefix

# --- Test ---
trie = Trie()
for w in ["flower", "flow", "flight"]:
    trie.insert(w)
print(trie.longest_common_prefix())   # fl

trie2 = Trie()
for w in ["dog", "racecar", "car"]:
    trie2.insert(w)
print(trie2.longest_common_prefix())  # "" (no common prefix)
```

#### C++ (simple)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TrieNode {
    TrieNode* children[26];
    bool isEnd;
    TrieNode() {
        fill(children, children + 26, nullptr);
        isEnd = false;
    }
};

void insert(TrieNode* root, const string& word) {
    TrieNode* node = root;
    for (char c : word) {
        int idx = c - 'a';
        if (!node->children[idx])
            node->children[idx] = new TrieNode();
        node = node->children[idx];
    }
    node->isEnd = true;
}

string longestCommonPrefix(TrieNode* root) {
    TrieNode* node = root;
    string prefix  = "";
    while (true) {
        // count non-null children
        vector<int> nonNull;
        for (int i = 0; i < 26; i++)
            if (node->children[i]) nonNull.push_back(i);

        // stop at branch point or end-of-word
        if (nonNull.size() != 1 || node->isEnd) break;

        int idx = nonNull[0];
        prefix += (char)(idx + 'a');    // index back to character
        node    = node->children[idx];
    }
    return prefix;
}

int main() {
    TrieNode* root = new TrieNode();
    for (auto& w : {"flower", "flow", "flight"}) insert(root, w);
    cout << longestCommonPrefix(root) << "\n";  // fl
}
```

#### C++ (LeetCode class style)

```cpp
// LeetCode #14 — Longest Common Prefix (Trie approach)
class TrieNode {
public:
    TrieNode* children[26];
    bool isEnd;
    TrieNode() {
        fill(children, children + 26, nullptr);
        isEnd = false;
    }
};

class Solution {
    TrieNode* root;

    void insert(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEnd = true;
    }

public:
    string longestCommonPrefix(vector<string>& strs) {
        root = new TrieNode();
        for (auto& w : strs) insert(w);     // build trie from all words

        TrieNode* node = root;
        string prefix  = "";
        while (true) {
            vector<int> nonNull;
            for (int i = 0; i < 26; i++)
                if (node->children[i]) nonNull.push_back(i);

            // stop when branching occurs or a word ends here
            if (nonNull.size() != 1 || node->isEnd) break;

            int idx = nonNull[0];
            prefix += (char)(idx + 'a');
            node    = node->children[idx];
        }
        return prefix;
    }
};
```

---

## Trie vs Other Data Structures (for Prefix Tasks)

| Feature               | Trie                  | Hash Map            | Sorted Array      |
| --------------------- | --------------------- | ------------------- | ----------------- |
| Exact word search     | O(L)                  | O(L)                | O(L · log N)      |
| Prefix search         | O(L)                  | O(N · L) worst case | O(L · log N)      |
| Count words by prefix | O(L) with count field | O(N · L)            | O(log N + K)      |
| Autocomplete          | Very efficient        | Not natural         | Possible but slow |
| Space usage           | High (26 pointers)    | Moderate            | Low               |
| Best for              | Prefix queries        | Exact key lookups   | Sorted traversal  |

> **Rule of thumb:** if the problem involves prefixes, think Trie first.

---

## Key Tips for Trie Problems

1. **Prefix → Trie** — Any problem mentioning prefix matching, autocomplete, or grouping words by start characters is a Trie candidate.
2. **Extra data in nodes** — You can store counts, word lists, or any metadata at each node to answer richer queries in O(L).
3. **Check for None/null** — Always guard against missing children before advancing a pointer.
4. **Complexity is word-length, not word-count** — O(L) regardless of how many words are stored.
5. **Words share structure** — Tries save space when many words share common prefixes ("apple", "app", "apt" share `ap`).
6. **Deletion** — Set `is_end = False` at the target node; optionally clean up childless nodes recursively (rarely tested).

---

## Complexity Summary

| Operation               | Time   | Space      |
| ----------------------- | ------ | ---------- |
| Insert word of length L | O(L)   | O(N · 26)  |
| Search word of length L | O(L)   | O(1) extra |
| Prefix query length L   | O(L)   | O(1) extra |
| Count prefix (w/ count) | O(L)   | O(N · 26)  |
| Longest common prefix   | O(N·L) | O(N · 26)  |

N = total nodes in the Trie; L = length of word/prefix.
