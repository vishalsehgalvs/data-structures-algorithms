# Huffman Coding Explained: Build Optimal Prefix Codes

## What is Huffman Coding?

Huffman Coding is a **lossless data compression** technique that assigns **shorter binary codes to frequent characters** and longer codes to rare ones.

Standard ASCII uses 8 bits for every character regardless of frequency. Huffman breaks that rule — if 'a' appears 70% of the time, giving it 1–2 bits instead of 8 can cut file size dramatically.

Used in: **ZIP, GZIP, JPEG, MP3** and many other real-world formats.

---

## Key Concepts

### Prefix-Free Code

No code for one character is the **prefix (beginning)** of another character's code. This guarantees unambiguous decoding. Huffman codes are always prefix-free by construction.

### Binary Tree + Leaf Nodes

Characters sit at **leaf nodes**. The path from root to a leaf = the binary code for that character. **Left edge = '0', Right edge = '1'**.

### Greedy Rule

**Always merge the two nodes with the smallest frequencies first.** This greedy choice is provably optimal — merging the least frequent nodes deepest in the tree minimizes total weighted path length.

---

## Step-by-Step Tree Construction

**Input string:** `"aabbbccdddd"`

**Frequency table:**

| Character | Frequency |
| --------- | --------- |
| a         | 2         |
| b         | 3         |
| c         | 2         |
| d         | 4         |

```
Step 1 — Insert all as leaf nodes into min-heap:
  Heap: [a:2] [c:2] [b:3] [d:4]

Step 2 — Extract a:2 and c:2, merge → new node *:4
  Heap: [b:3] [*:4] [d:4]
  Tree:
       [*:4]
      /     \
   [a:2]  [c:2]

Step 3 — Extract b:3 and *:4, merge → new node *:7
  Heap: [d:4] [*:7]
  Tree:
       [*:7]
      /     \
   [b:3]  [*:4]
          /   \
       [a:2] [c:2]

Step 4 — Extract d:4 and *:7, merge → root *:11
  Heap: [*:11]
  Final Huffman Tree:
              [*:11]
             /      \
          [d:4]    [*:7]
                  /     \
               [b:3]  [*:4]
                      /   \
                   [a:2] [c:2]
```

**Assigned codes** (left=0, right=1):

| Character | Frequency | Code | Bits |
| --------- | --------- | ---- | ---- |
| d         | 4         | 0    | 1    |
| b         | 3         | 10   | 2    |
| a         | 2         | 110  | 3    |
| c         | 2         | 111  | 3    |

Frequent characters get shorter codes — optimal compression.

---

## Encoding and Decoding

```
Encoding "aabbbccdddd":
  a=110, a=110, b=10, b=10, b=10, c=111, c=111, d=0, d=0, d=0, d=0
  Encoded: 110 110 10 10 10 111 111 0 0 0 0
  Total:  22 bits  vs  88 bits (fixed 8-bit ASCII) → ~75% reduction

Decoding (traverse tree bit by bit, reset to root at each leaf):
  Read 1 → go right
  Read 1 → go right
  Read 0 → go left → leaf! Output 'a', reset to root
  ... continues until full string "aabbbccdddd" is recovered
```

---

## Code Implementation

#### Python

```python
import heapq

class HuffmanNode:
    def __init__(self, char, freq):
        self.char = char
        self.freq = freq
        self.left = None
        self.right = None

    def __lt__(self, other):            # needed for heapq comparisons
        return self.freq < other.freq


def build_huffman_tree(text):
    # Step 1: Count frequencies
    freq = {}
    for ch in text:
        freq[ch] = freq.get(ch, 0) + 1

    # Step 2: Create leaf nodes and push into min-heap
    heap = [HuffmanNode(ch, f) for ch, f in freq.items()]
    heapq.heapify(heap)

    # Step 3: Merge two smallest nodes until one root remains
    while len(heap) > 1:
        left = heapq.heappop(heap)      # smallest frequency
        right = heapq.heappop(heap)     # second smallest

        merged = HuffmanNode(None, left.freq + right.freq)
        merged.left = left
        merged.right = right

        heapq.heappush(heap, merged)    # push merged node back

    return heap[0]                      # root of the Huffman tree


def generate_codes(node, prefix="", codes={}):
    if node is None:
        return
    if node.char is not None:           # leaf node
        codes[node.char] = prefix
        return
    generate_codes(node.left,  prefix + "0", codes)
    generate_codes(node.right, prefix + "1", codes)
    return codes


def encode(text, codes):
    return "".join(codes[ch] for ch in text)


def decode(encoded, root):
    result = []
    node = root
    for bit in encoded:
        node = node.left if bit == "0" else node.right
        if node.char is not None:       # reached a leaf
            result.append(node.char)
            node = root                 # reset to root
    return "".join(result)


# Example
text = "aabbbccdddd"
root = build_huffman_tree(text)
codes = generate_codes(root)

print("Codes:", codes)
encoded = encode(text, codes)
print("Encoded:", encoded)
print("Decoded:", decode(encoded, root))
# Decoded: aabbbccdddd
```

#### C++ (simple)

```cpp
#include <iostream>
#include <queue>
#include <unordered_map>
#include <string>
using namespace std;

struct HuffmanNode {
    char ch;
    int freq;
    HuffmanNode* left;
    HuffmanNode* right;
    HuffmanNode(char c, int f) : ch(c), freq(f), left(nullptr), right(nullptr) {}
};

// Min-heap comparator — smallest frequency at top
struct Compare {
    bool operator()(HuffmanNode* a, HuffmanNode* b) {
        return a->freq > b->freq;
    }
};

// Build the Huffman tree from a frequency map
HuffmanNode* buildTree(unordered_map<char,int>& freq) {
    priority_queue<HuffmanNode*, vector<HuffmanNode*>, Compare> pq;

    for (auto& [ch, f] : freq)
        pq.push(new HuffmanNode(ch, f));    // one leaf per character

    while (pq.size() > 1) {
        HuffmanNode* left  = pq.top(); pq.pop();    // smallest
        HuffmanNode* right = pq.top(); pq.pop();    // second smallest

        // Merge into internal node (ch='\0' marks internal nodes)
        HuffmanNode* merged = new HuffmanNode('\0', left->freq + right->freq);
        merged->left  = left;
        merged->right = right;
        pq.push(merged);
    }
    return pq.top();    // root of tree
}

// DFS to assign codes: left='0', right='1'
void generateCodes(HuffmanNode* node, string prefix,
                   unordered_map<char,string>& codes) {
    if (node == nullptr) return;
    if (node->ch != '\0') {             // leaf node
        codes[node->ch] = prefix;
        return;
    }
    generateCodes(node->left,  prefix + "0", codes);
    generateCodes(node->right, prefix + "1", codes);
}

string encode(const string& text, unordered_map<char,string>& codes) {
    string result;
    for (char ch : text) result += codes[ch];
    return result;
}

string decode(const string& encoded, HuffmanNode* root) {
    string result;
    HuffmanNode* node = root;
    for (char bit : encoded) {
        node = (bit == '0') ? node->left : node->right;
        if (node->ch != '\0') {         // reached a leaf
            result += node->ch;
            node = root;                // reset to root
        }
    }
    return result;
}

int main() {
    string text = "aabbbccdddd";

    // Count frequencies
    unordered_map<char,int> freq;
    for (char ch : text) freq[ch]++;

    HuffmanNode* root = buildTree(freq);

    unordered_map<char,string> codes;
    generateCodes(root, "", codes);

    cout << "Huffman Codes:\n";
    for (auto& [ch, code] : codes)
        cout << "  " << ch << " : " << code << "\n";

    string encoded = encode(text, codes);
    cout << "Encoded: " << encoded << "\n";
    cout << "Decoded: " << decode(encoded, root) << "\n";
    return 0;
}
// Time: O(n log n) | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #1167 — Minimum Cost to Connect Sticks
    // Identical greedy: always merge the two smallest — same as Huffman construction
    int connectSticks(vector<int>& sticks) {
        // Min-heap to always pop the two smallest sticks
        priority_queue<int, vector<int>, greater<int>> pq(sticks.begin(), sticks.end());

        int totalCost = 0;

        while (pq.size() > 1) {
            int first  = pq.top(); pq.pop();    // smallest stick
            int second = pq.top(); pq.pop();    // second smallest

            int merged = first + second;        // cost of merging = merged length
            totalCost += merged;
            pq.push(merged);                    // push merged stick back
        }
        return totalCost;
    }
};
// This is Huffman's greedy core: merge 2 least-frequent nodes each step
// Time: O(n log n) | Space: O(n)
```

---

## Why the Greedy Choice is Optimal

The proof uses an **exchange argument**: if the two least frequent characters are not siblings at the deepest level of the optimal tree, we can swap them to make the tree at least as good. Therefore, merging the two smallest nodes first is always a safe greedy choice — Huffman Coding is provably optimal among all prefix-free binary codes.

---

## Huffman vs Fixed-Length Coding

| Feature            | Fixed-Length (ASCII) | Huffman Coding                |
| ------------------ | -------------------- | ----------------------------- |
| Bits per character | Same for all (8)     | Varies by frequency           |
| Compression        | None                 | Lossless compression          |
| Decoding           | Trivial              | Needs the Huffman tree        |
| Optimality         | Not optimal          | Optimal for prefix-free codes |

---

## Time and Space Complexity

| Step                                   | Complexity     |
| -------------------------------------- | -------------- |
| Count frequencies                      | O(n)           |
| Build tree (n-1 merges, each O(log n)) | O(n log n)     |
| Generate codes (tree traversal)        | O(n)           |
| **Total time**                         | **O(n log n)** |
| **Space**                              | **O(n)**       |

---

## Key Takeaways

- Always merge the **two least frequent nodes** — that's the greedy step.
- Use a **min-heap** so each extraction/insertion is O(log n).
- The resulting tree gives **prefix-free codes** — no ambiguity during decoding.
- Frequent characters → shorter codes → fewer total bits.
- Provably optimal for prefix-free binary encoding.

---

## FAQ

**Is Huffman Coding lossless?**

Yes — the original data is perfectly reconstructed using the same Huffman tree. No information is lost.

**Why use a min-heap?**

A min-heap lets us quickly find and extract the two nodes with the smallest frequencies in O(log n), keeping the total algorithm at O(n log n) instead of O(n²).

**Can two different Huffman trees give the same compression ratio?**

Yes. Multiple valid trees can exist with equal total weighted path length. What matters is that the total is minimized, not the exact tree shape.
