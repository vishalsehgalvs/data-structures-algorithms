# Stack Data Structure — Basics and Common Applications

> **One-line summary:**
> A stack is a LIFO (Last In, First Out) linear data structure where push, pop, and peek all run in $O(1)$ time — it powers browser history, undo functionality, balanced parentheses checking, and the call stack that makes recursion work.

---

## Table of Contents

1. [What is a Stack?](#1-what-is-a-stack)
2. [Stack Terminology](#2-stack-terminology)
3. [How a Stack Works — Visual Walkthrough](#3-how-a-stack-works--visual-walkthrough)
4. [Stack Using an Array](#4-stack-using-an-array)
5. [Stack Using a Linked List](#5-stack-using-a-linked-list)
6. [Array vs Linked List Stack](#6-array-vs-linked-list-stack)
7. [Time and Space Complexity](#7-time-and-space-complexity)
8. [Applications of Stacks](#8-applications-of-stacks)
9. [Common Mistakes to Avoid](#9-common-mistakes-to-avoid)
10. [Key Takeaways](#10-key-takeaways)
11. [FAQs](#11-faqs)

---

## 1. What is a Stack?

Imagine a pile of plates in your kitchen. You always place a new plate on **top**, and when you need one, you take it from the **top** too. You never grab a plate from the middle or bottom. This simple idea is exactly how a stack works in programming.

A **stack** is a linear data structure that follows the **LIFO** principle: **Last In, First Out**. The last element you add is the first one you remove.

```
  Push 10, 20, 30 →          Pop →
  ┌────┐                      ┌────┐
  │ 30 │  ← top               │ 20 │  ← top (30 removed)
  ├────┤                      ├────┤
  │ 20 │                      │ 10 │
  ├────┤                      └────┘
  │ 10 │
  └────┘
```

Stacks appear everywhere in software: browser back buttons, undo in text editors, and how your computer manages function calls.

---

## 2. Stack Terminology

| Term           | Meaning                                                 |
| -------------- | ------------------------------------------------------- |
| **Push**       | Add an element to the top of the stack                  |
| **Pop**        | Remove and return the top element                       |
| **Peek / Top** | View the top element without removing it                |
| **isEmpty**    | Check if the stack has no elements                      |
| **Overflow**   | Attempting to push onto a full stack (array-based only) |
| **Underflow**  | Attempting to pop from an empty stack                   |

Think of `push` and `pop` as the two main actions. Everything else supports them.

---

## 3. How a Stack Works — Visual Walkthrough

Starting with an empty stack: push 10, push 20, push 30, pop, peek.

| Operation | Stack state (top on right) | Result     |
| --------- | -------------------------- | ---------- |
| Push 10   | `[10]`                     | 10 added   |
| Push 20   | `[10, 20]`                 | 20 added   |
| Push 30   | `[10, 20, 30]`             | 30 added   |
| Pop       | `[10, 20]`                 | 30 removed |
| Peek      | `[10, 20]`                 | Returns 20 |

Notice how the last element pushed (30) was the first one popped — LIFO in action.

---

## 4. Stack Using an Array

The simplest way to build a stack is with an array or list. Python lists have built-in `append` (push) and `pop` methods that make this trivial.

### Python

```python
# Python — Stack using a list (array-based)
class Stack:
    def __init__(self):
        self.items = []         # internal list to store elements

    def push(self, item):
        self.items.append(item) # add item to the top

    def pop(self):
        if self.is_empty():
            return "Underflow: Stack is empty"
        return self.items.pop() # remove and return top item

    def peek(self):
        if self.is_empty():
            return "Stack is empty"
        return self.items[-1]   # return top item without removing

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)


s = Stack()
s.push(10)
s.push(20)
s.push(30)

print(s.peek())   # Output: 30
print(s.pop())    # Output: 30
print(s.peek())   # Output: 20
print(s.size())   # Output: 2
```

### C++

```cpp
// C++ — Stack using a vector (array-based)
#include <iostream>
#include <vector>
#include <stdexcept>

class Stack {
private:
    std::vector<int> items;

public:
    void push(int item) {
        items.push_back(item);         // add item to the top
    }

    int pop() {
        if (isEmpty())
            throw std::underflow_error("Underflow: Stack is empty");
        int top = items.back();
        items.pop_back();              // remove top item
        return top;
    }

    int peek() const {
        if (isEmpty())
            throw std::underflow_error("Stack is empty");
        return items.back();           // return top without removing
    }

    bool isEmpty() const {
        return items.empty();
    }

    int size() const {
        return static_cast<int>(items.size());
    }
};

int main() {
    Stack s;
    s.push(10);
    s.push(20);
    s.push(30);

    std::cout << s.peek() << "\n";   // Output: 30
    std::cout << s.pop()  << "\n";   // Output: 30
    std::cout << s.peek() << "\n";   // Output: 20
    std::cout << s.size() << "\n";   // Output: 2
}
```

> **Note:** C++ also has `std::stack` in `<stack>` for direct use: `std::stack<int> s; s.push(10); s.top(); s.pop();`

---

## 5. Stack Using a Linked List

A linked list-based stack has no fixed size limit. Each push creates a new node dynamically — ideal when you do not know the maximum number of elements upfront. The top of the stack is always the **head** of the linked list.

### Python

```python
# Python — Stack using a Linked List
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None


class LinkedStack:
    def __init__(self):
        self.top = None   # empty stack

    def push(self, item):
        new_node = Node(item)
        new_node.next = self.top   # new node points to current top
        self.top = new_node        # new node becomes the new top

    def pop(self):
        if self.top is None:
            return "Underflow: Stack is empty"
        popped = self.top.data
        self.top = self.top.next   # move top to next node
        return popped

    def peek(self):
        if self.top is None:
            return "Stack is empty"
        return self.top.data

    def is_empty(self):
        return self.top is None


ls = LinkedStack()
ls.push(5)
ls.push(15)
ls.push(25)

print(ls.peek())  # Output: 25
print(ls.pop())   # Output: 25
print(ls.peek())  # Output: 15
```

### C++

```cpp
// C++ — Stack using a Linked List
#include <iostream>
#include <stdexcept>

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedStack {
private:
    Node* top_node;

public:
    LinkedStack() : top_node(nullptr) {}

    ~LinkedStack() {
        while (top_node) {      // free all nodes on destruction
            Node* tmp = top_node->next;
            delete top_node;
            top_node = tmp;
        }
    }

    void push(int item) {
        Node* new_node = new Node(item);
        new_node->next = top_node;   // new node points to current top
        top_node = new_node;         // new node becomes the new top
    }

    int pop() {
        if (isEmpty())
            throw std::underflow_error("Underflow: Stack is empty");
        int val = top_node->data;
        Node* old_top = top_node;
        top_node = top_node->next;
        delete old_top;
        return val;
    }

    int peek() const {
        if (isEmpty())
            throw std::underflow_error("Stack is empty");
        return top_node->data;
    }

    bool isEmpty() const {
        return top_node == nullptr;
    }
};

int main() {
    LinkedStack ls;
    ls.push(5);
    ls.push(15);
    ls.push(25);

    std::cout << ls.peek() << "\n";  // Output: 25
    std::cout << ls.pop()  << "\n";  // Output: 25
    std::cout << ls.peek() << "\n";  // Output: 15
}
```

---

## 6. Array vs Linked List Stack

| Feature           | Array-Based Stack               | Linked List Stack                   |
| ----------------- | ------------------------------- | ----------------------------------- |
| Memory allocation | Fixed or dynamic (Python grows) | Dynamic — grows as needed           |
| Push / Pop speed  | $O(1)$                          | $O(1)$                              |
| Memory overhead   | Low                             | Higher — each node stores a pointer |
| Overflow risk     | Only with fixed-size arrays     | None (until system memory runs out) |
| Implementation    | Simpler                         | Slightly more complex               |
| Cache performance | Better (contiguous memory)      | Worse (scattered memory)            |

For most competitive programming and beginner problems, the array-based approach (Python list / C++ `vector`) is the go-to choice.

---

## 7. Time and Space Complexity

| Operation | Time Complexity | Notes                                     |
| --------- | --------------- | ----------------------------------------- |
| Push      | $O(1)$          | Always operates on the top                |
| Pop       | $O(1)$          | Always operates on the top                |
| Peek      | $O(1)$          | Just reads the top                        |
| Search    | $O(n)$          | Must scan all elements in the worst case  |
| Space     | $O(n)$          | Proportional to number of elements stored |

Push, pop, and peek are constant time because you always work with the top — no traversal needed.

---

## 8. Applications of Stacks

### Balanced Parentheses Checker

Push every opening bracket. When you see a closing bracket, pop and check if they match. If the stack is empty at the end, the expression is balanced.

#### Python

```python
# Python — Balanced brackets checker
def is_balanced(expression):
    stack = []
    matching = {')': '(', '}': '{', ']': '['}

    for char in expression:
        if char in '({[':
            stack.append(char)         # push opening bracket
        elif char in ')}]':
            if not stack:
                return False           # no matching opening bracket
            if stack[-1] != matching[char]:
                return False           # wrong bracket type
            stack.pop()                # matched — remove from stack

    return len(stack) == 0   # True if all brackets matched


print(is_balanced("({[]})"))   # Output: True
print(is_balanced("({[}])"))   # Output: False
print(is_balanced("((("))      # Output: False
```

#### C++

```cpp
// C++ — Balanced brackets checker
#include <iostream>
#include <stack>
#include <string>
#include <unordered_map>

bool isBalanced(const std::string& expr) {
    std::stack<char> st;
    std::unordered_map<char,char> matching = {{')',  '('}, {'}', '{'}, {']', '['}};

    for (char c : expr) {
        if (c == '(' || c == '{' || c == '[') {
            st.push(c);                // push opening bracket
        } else if (matching.count(c)) {
            if (st.empty() || st.top() != matching[c])
                return false;          // unmatched bracket
            st.pop();
        }
    }
    return st.empty();   // true if all brackets matched
}

int main() {
    std::cout << isBalanced("({[]})") << "\n";   // Output: 1 (true)
    std::cout << isBalanced("({[}]})") << "\n";  // Output: 0 (false)
    std::cout << isBalanced("(((")    << "\n";   // Output: 0 (false)
}
```

---

### Undo Functionality

Every action you perform gets pushed onto a history stack. Pressing Ctrl+Z pops the last action and restores the previous state.

#### Python

```python
# Python — Undo functionality with a stack
class TextEditor:
    def __init__(self):
        self.text = ""
        self.history = []   # stack of previous states

    def type(self, new_text):
        self.history.append(self.text)   # save current state
        self.text += new_text
        print(f"Text: {self.text}")

    def undo(self):
        if not self.history:
            print("Nothing to undo")
            return
        self.text = self.history.pop()   # restore previous state
        print(f"After undo: {self.text}")


editor = TextEditor()
editor.type("Hello")
editor.type(" World")
editor.undo()   # After undo: Hello
editor.undo()   # After undo: (empty)
```

#### C++

```cpp
// C++ — Undo functionality with a stack
#include <iostream>
#include <stack>
#include <string>

class TextEditor {
    std::string text;
    std::stack<std::string> history;
public:
    void type(const std::string& new_text) {
        history.push(text);      // save current state
        text += new_text;
        std::cout << "Text: " << text << "\n";
    }

    void undo() {
        if (history.empty()) { std::cout << "Nothing to undo\n"; return; }
        text = history.top();
        history.pop();           // restore previous state
        std::cout << "After undo: " << text << "\n";
    }
};

int main() {
    TextEditor editor;
    editor.type("Hello");
    editor.type(" World");
    editor.undo();   // After undo: Hello
    editor.undo();   // After undo: (empty)
}
```

---

### Reversing a Sequence

Because of LIFO, stacks are natural reversers. Push all elements in, then pop them out to get them in reverse order.

#### Python

```python
# Python — Reverse a string using a stack
def reverse_string(s):
    stack = []
    for char in s:
        stack.append(char)      # push each character

    return "".join(stack.pop() for _ in range(len(stack)))


print(reverse_string("hello"))    # Output: olleh
print(reverse_string("Python"))   # Output: nohtyP
```

#### C++

```cpp
// C++ — Reverse a string using a stack
#include <iostream>
#include <stack>
#include <string>

std::string reverseString(const std::string& s) {
    std::stack<char> st;
    for (char c : s) st.push(c);   // push each character

    std::string result;
    while (!st.empty()) {
        result += st.top();         // pop gives reverse order
        st.pop();
    }
    return result;
}

int main() {
    std::cout << reverseString("hello")  << "\n";  // Output: olleh
    std::cout << reverseString("Python") << "\n";  // Output: nohtyP
}
```

---

### Function Call Stack

When your program calls a function, the computer pushes a **stack frame** onto the call stack. When the function returns, the frame is popped. This is exactly why deep recursion without a proper base case causes a **stack overflow** — the call stack runs out of space.

```
main() calls foo()
  foo() calls bar()
    bar() calls baz()

Call stack (top = most recent):
  ┌─────────┐
  │  baz()  │  ← currently executing
  ├─────────┤
  │  bar()  │
  ├─────────┤
  │  foo()  │
  ├─────────┤
  │  main() │
  └─────────┘
```

---

## 9. Common Mistakes to Avoid

**1. Popping from an empty stack**

Always check `is_empty()` before calling `pop()`. Skipping this causes an underflow error or crash.

```python
# Wrong — crashes if stack is empty
value = s.pop()

# Correct — check first
if not s.is_empty():
    value = s.pop()
```

**2. Confusing LIFO with FIFO**

A stack is **not** a queue. Last in, first out — always. If you need first-in-first-out behaviour, a queue is the right choice (covered in the next post).

**3. Ignoring stack size in fixed-size implementations**

In C++ with a fixed-size array, always handle overflow gracefully. Using `std::vector` or `std::stack` avoids this issue entirely.

**4. Using a stack when a different structure fits better**

If you need to access elements by index or process them in arrival order, an array or queue is more appropriate.

---

## 10. Key Takeaways

- A stack follows **LIFO** — the last element added is always the first removed.
- The three core operations — `push`, `pop`, and `peek` — all run in $O(1)$ time.
- You can implement a stack with an **array** (simpler, cache-friendly) or a **linked list** (no size limit, dynamic).
- In C++ the standard library provides `std::stack<T>` out of the box.
- Real-world uses: balanced bracket checking, undo/redo, function call management, expression evaluation, reversing sequences.
- Always check for **underflow** before popping; in array-based stacks also check for **overflow** with fixed sizes.
- The call stack that powers recursion is itself a stack — understanding this makes recursive code much easier to reason about.

---

## 11. FAQs

**What is the difference between a stack and a queue?**

A stack follows LIFO — the most recently added element is removed first (like a pile of plates). A queue follows FIFO — the oldest element is removed first (like a line at a ticket counter). We cover queues in the next post.

**When should I use a stack?**

Use a stack when the problem involves reversing something, matching nested pairs (brackets, tags), tracking the most recent state (undo), or managing nested function calls. Keywords: _last_, _recent_, _previous_, _nested_, _undo_.

**What does stack overflow mean in programming?**

Stack overflow happens when the call stack runs out of memory — usually from recursion that is too deep or missing a base case. The computer reserves a fixed amount of memory for the call stack, and exceeding it crashes the program with a "stack overflow" error.

**Is Python's built-in `list` a safe stack?**

Yes for most purposes. Python lists grow dynamically, so there is no fixed overflow risk. `append()` is push and `pop()` (no argument) is pop — both $O(1)$ amortised. For thread-safe stacks, use `collections.deque` instead.

**Does C++ `std::stack` use an array or linked list internally?**

By default `std::stack` is backed by `std::deque`. You can customise this: `std::stack<int, std::vector<int>>` uses a vector, and `std::stack<int, std::list<int>>` uses a linked list. For most purposes the default is fine.
