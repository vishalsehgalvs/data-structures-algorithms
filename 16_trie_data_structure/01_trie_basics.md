# Trie Data Structure: Basics and Real-World Uses

## What is a Trie?

A **Trie** (pronounced "try") is a tree-like data structure designed to store and search strings efficiently. Unlike a BST where each node holds a complete value, a Trie breaks strings into **individual characters** — each character occupies one node, and words are formed by following a path from root to leaf.

Think of it like a folder system: each folder is a letter, and each path spells out a word.

---

## How a Trie Works

A Trie always starts with an empty **root node**. From the root, each branch represents one character. When inserting a word:

- Walk down the tree one character at a time.
- If a node for that character already exists → **reuse it**.
- If not → **create a new node**.

Common characters are shared between words. "cat" and "car" share 'c' and 'a', branching only at 't' vs 'r'.

### Visual

```
After inserting "cat", "car", "dog":

        root
        /   \
       c     d
       |     |
       a     o
      / \    |
     t   r   g
  (end)(end)(end)

(end) = isEndOfWord = true
```

Each path from root to an `(end)` node = one complete word.

---

## Trie Node Structure

Each node holds:

- An array (or map) of **child nodes** — one slot per character (26 for lowercase English).
- A **boolean flag** `isEndOfWord` — marks whether a complete word ends here.

#### Python

```python
class TrieNode:
    def __init__(self):
        self.children = {}          # char → TrieNode (dict avoids wasting 26 slots)
        self.is_end = False         # True if a word ends at this node
```

#### C++ (simple)

```cpp
struct TrieNode {
    TrieNode* children[26];         // one slot per lowercase letter
    bool isEnd;

    TrieNode() : isEnd(false) {
        for (int i = 0; i < 26; i++)
            children[i] = nullptr;  // no children initially
    }
};
```

#### C++ (LeetCode class style)

```cpp
struct TrieNode {
    TrieNode* children[26];
    bool isEnd;
    TrieNode() : isEnd(false) {
        fill(begin(children), end(children), nullptr);
    }
};
```

---

## Core Trie Operations

Three fundamental operations: **insert**, **search**, **startsWith**.

---

### Insert

Walk character by character. For each character `ch`:

- Index = `ch - 'a'` (maps 'a'→0, 'b'→1, ..., 'z'→25).
- If child at that index is null → create a new node.
- Move to that child.
- At the end → set `isEndOfWord = true`.

#### Python

```python
class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:     # create node if missing
                node.children[ch] = TrieNode()
            node = node.children[ch]        # move to next character
        node.is_end = True                  # mark end of word
```

#### C++ (simple)

```cpp
class Trie {
    TrieNode* root;
public:
    Trie() { root = new TrieNode(); }

    void insert(const string& word) {
        TrieNode* node = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();  // create if missing
            node = node->children[idx];                 // advance
        }
        node->isEnd = true;     // mark word ending
    }
};
// Time: O(L) where L = word length
```

#### C++ (LeetCode class style)

```cpp
class Trie {
    TrieNode* root;
public:
    Trie() { root = new TrieNode(); }

    // LeetCode #208 — insert
    void insert(string word) {
        TrieNode* node = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEnd = true;
    }
};
// After insert("cat"): nodes 'c'→'a'→'t' created, 't'.isEnd = true
```

---

### Search

Walk each character. If at any point a child is missing → word not found. If you reach the end and `isEnd` is true → word exists.

**O(L) time** — independent of how many words are stored.

#### Python

```python
    def search(self, word: str) -> bool:
        node = self.root
        for ch in word:
            if ch not in node.children:     # path breaks — word not found
                return False
            node = node.children[ch]
        return node.is_end                  # True only if word ends here
```

#### C++ (simple)

```cpp
    bool search(const string& word) {
        TrieNode* node = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!node->children[idx]) return false;  // missing node = not found
            node = node->children[idx];
        }
        return node->isEnd;     // full word must end here
    }
// Time: O(L)
```

#### C++ (LeetCode class style)

```cpp
    // LeetCode #208 — search
    bool search(string word) {
        TrieNode* node = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return node->isEnd;
    }
```

---

### startsWith (Prefix Check)

Almost identical to search — but do **not** check `isEnd` at the end. Just verify the path exists.

#### Python

```python
    def starts_with(self, prefix: str) -> bool:
        node = self.root
        for ch in prefix:
            if ch not in node.children:     # prefix path doesn't exist
                return False
            node = node.children[ch]
        return True                         # full prefix path found
```

#### C++ (simple)

```cpp
    bool startsWith(const string& prefix) {
        TrieNode* node = root;
        for (char ch : prefix) {
            int idx = ch - 'a';
            if (!node->children[idx]) return false;  // prefix not present
            node = node->children[idx];
        }
        return true;    // prefix path exists (don't check isEnd)
    }
// Time: O(L)
```

#### C++ (LeetCode class style)

```cpp
    // LeetCode #208 — startsWith
    bool startsWith(string prefix) {
        TrieNode* node = root;
        for (char ch : prefix) {
            int idx = ch - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return true;    // just verify path — don't require isEnd
    }
```

---

## Complete Trie Example

#### Python

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def search(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children: return False
            node = node.children[ch]
        return node.is_end

    def starts_with(self, prefix):
        node = self.root
        for ch in prefix:
            if ch not in node.children: return False
            node = node.children[ch]
        return True


trie = Trie()
trie.insert("apple")
trie.insert("app")
trie.insert("bat")

print(trie.search("apple"))        # True
print(trie.search("app"))          # True
print(trie.search("ap"))           # False — "ap" was never inserted
print(trie.starts_with("ap"))      # True  — "app" and "apple" start with "ap"
print(trie.starts_with("bat"))     # True
print(trie.starts_with("cat"))     # False
```

#### C++ (simple)

```cpp
#include <iostream>
#include <string>
using namespace std;

struct TrieNode {
    TrieNode* children[26];
    bool isEnd;
    TrieNode() : isEnd(false) {
        for (int i = 0; i < 26; i++) children[i] = nullptr;
    }
};

class Trie {
    TrieNode* root;
public:
    Trie() { root = new TrieNode(); }

    void insert(const string& word) {
        TrieNode* node = root;
        for (char ch : word) {
            int i = ch - 'a';
            if (!node->children[i]) node->children[i] = new TrieNode();
            node = node->children[i];
        }
        node->isEnd = true;
    }

    bool search(const string& word) {
        TrieNode* node = root;
        for (char ch : word) {
            int i = ch - 'a';
            if (!node->children[i]) return false;
            node = node->children[i];
        }
        return node->isEnd;
    }

    bool startsWith(const string& prefix) {
        TrieNode* node = root;
        for (char ch : prefix) {
            int i = ch - 'a';
            if (!node->children[i]) return false;
            node = node->children[i];
        }
        return true;
    }
};

int main() {
    Trie trie;
    trie.insert("apple"); trie.insert("app"); trie.insert("bat");

    cout << trie.search("apple")     << "\n";   // 1 (true)
    cout << trie.search("app")       << "\n";   // 1 (true)
    cout << trie.search("ap")        << "\n";   // 0 (false)
    cout << trie.startsWith("ap")    << "\n";   // 1 (true)
    cout << trie.startsWith("bat")   << "\n";   // 1 (true)
    cout << trie.startsWith("cat")   << "\n";   // 0 (false)
    return 0;
}
```

#### C++ (LeetCode class style)

```cpp
// LeetCode #208 — Implement Trie (Prefix Tree)
class Trie {
    struct TrieNode {
        TrieNode* children[26];
        bool isEnd;
        TrieNode() : isEnd(false) {
            fill(begin(children), end(children), nullptr);
        }
    };

    TrieNode* root;

public:
    Trie() { root = new TrieNode(); }

    void insert(string word) {
        TrieNode* node = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEnd = true;
    }

    bool search(string word) {
        TrieNode* node = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return node->isEnd;
    }

    bool startsWith(string prefix) {
        TrieNode* node = root;
        for (char ch : prefix) {
            int idx = ch - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return true;
    }
};
// Time: O(L) per operation | Space: O(N * L * 26) worst case
```

---

## Trie vs HashMap vs BST

| Feature           | Trie                 | HashSet / HashMap      | BST               |
| ----------------- | -------------------- | ---------------------- | ----------------- |
| Exact word search | O(L)                 | O(L) average           | O(L log N)        |
| Prefix search     | **O(L) — excellent** | Not supported directly | Complex           |
| Autocomplete      | Yes, naturally       | No                     | Possible but slow |
| Sorted traversal  | Yes (alphabetical)   | No                     | Yes               |
| Space             | Higher (many nodes)  | Lower                  | Moderate          |

Use Trie when your problem involves **prefixes, autocomplete, or dictionary lookups**. Use HashSet for simple exact-match checks.

---

## Time and Space Complexity

| Operation  | Time |
| ---------- | ---- |
| insert     | O(L) |
| search     | O(L) |
| startsWith | O(L) |

L = length of the word or prefix. **Independent of how many words are stored** — a massive advantage over linear search.

**Space:** O(N × L × 26) worst case (N words, each of length L, 26-slot array per node). Use a `dict`/`unordered_map` instead of a fixed 26-array to reduce space when the alphabet is sparse.

---

## Real-World Applications

| Application                       | How Trie is Used                                      |
| --------------------------------- | ----------------------------------------------------- |
| Autocomplete / search suggestions | Prefix lookup returns all words matching typed prefix |
| Spell checkers                    | Quickly verify if a word exists; suggest alternatives |
| IP routing (Radix Tree)           | Match IP address prefixes for routing decisions       |
| Contact name search               | Instant prefix filtering as you type                  |
| Word games (Scrabble solvers)     | Find all valid words from a set of letters            |

---

## Common Trie Problems

- **LeetCode #208** — Implement Trie (insert, search, startsWith)
- **LeetCode #720** — Longest Word in Dictionary
- **LeetCode #14** — Longest Common Prefix
- **LeetCode #648** — Replace Words
- **LeetCode #211** — Design Add and Search Words Data Structure

---

## Key Takeaways

- Trie stores strings character by character; common prefixes are shared.
- Each node: `children[26]` + `isEndOfWord` flag.
- **Insert / Search / startsWith** all run in **O(L)** — independent of dictionary size.
- `search` checks `isEnd`; `startsWith` does not — that's the only difference.
- Use a `dict` for children to save space when you don't need all 26 slots.

---

## FAQ

**When should I use a Trie instead of a HashMap?**

Use a Trie when you need prefix-based operations like autocomplete or `startsWith`. A HashMap is faster for simple exact-match lookups but doesn't support prefix queries natively.

**Is a Trie case sensitive?**

By default yes. Convert all input to lowercase first (or use a dict for children) to handle mixed case.

**Can you delete a word from a Trie?**

Yes — unmark `isEnd` and recursively remove nodes that are no longer part of any other word. Most interview problems focus on insert/search/startsWith; deletion is an advanced extension.
