# ECE 150: Linked Lists - Complete Study Guide
## University of Waterloo - Fundamentals of Programming

---

# Table of Contents

1. [Why Linked Lists?](#why-linked-lists)
2. [Basic Concepts](#basic-concepts)
3. [Node Implementation](#node-implementation)
4. [Linked List Implementation](#linked-list-implementation)
5. [Operations Deep Dive](#operations-deep-dive)
6. [Enhancements](#enhancements)
7. [Advanced Operations](#advanced-operations)
8. [Complete Code](#complete-code)

---

# 1. Why Linked Lists?

## Problems with Arrays

### Fixed Size
Once an array is declared, its size cannot change:
```cpp
int array[10];  // Forever 10 elements
int *dynamic = new int[20];  // Forever 20 elements
```

To resize, you must:
1. Allocate new array
2. Copy all elements
3. Delete old array
4. Update pointer

**Cost: O(n)** - expensive!

### Expensive Insertions at Front

Inserting at position 0 requires shifting ALL elements:
```
Before: [18, 20, 19, 21, 21, 22, 21, 23, 22, 24]
Insert 17 at front
After:  [17, 18, 20, 19, 21, 21, 22, 21, 23, 22]
```

**Must shift every element right by one position!**

**Cost: O(n)**

### Memory Waste in Operating Systems

OS manages many queues:
- Print queue
- I/O queue  
- Memory allocation queue
- Process scheduling queues

**Problem:** Most queues are empty or have 1-2 items, but must allocate for worst case (maybe 256 slots each!)

If 50 queues × 256 slots = 12,800 slots
Typical usage: 100 slots
**Waste: 99.2%**

In embedded systems, this waste affects:
- Power consumption → battery life
- Heat generation → cooling needs
- Weight → portability
- Cost → competitiveness

---

# 2. Basic Concepts

## What is a Linked List?

A **linked list** is a data structure where each element (node) contains:
1. **Data** (the value being stored)
2. **Pointer/reference** to the next node

The list only needs to remember the **head** (first node).

### Visual Representation
```
head → [6.3 | ●] → [9.1 | ●] → [4.2 | ∅]
        data  next   data  next   data  null
```

## Array-Based Introduction (Simplified)

Before using pointers, the slides introduce linked lists using array indices:
```cpp
class Node {
public:
    double value_;
    std::size_t next_index_;  // Index of next node in array
};
```

Example:
```
Array index:     0    1    2    3    4    5    6    7
value_:         0.0  0.0  9.1  0.0  0.0  4.2  0.0  6.3
next_index_:    11   11    5   11   11   10   11    2

list_head = 7
```

The list is: `7 → 2 → 5 → end`
Values: `6.3 → 9.1 → 4.2`

**Convention:**
- `next_index_ = 11` means "unused"
- `list_head = 10` means "empty list"  
- `next_index_ = 10` means "last node"

---

# 3. Node Implementation

## Node Class - Pointer Version
```cpp
class Node {
public:
    Node(double const new_value, Node *const p_new_next_node);
    
    double value() const;
    Node *p_next_node() const;
    void value(double const new_value);
    void p_next_node(Node *const p_new_next_node);
    
private:
    double value_;
    Node *p_next_node_;  // Pointer to next node
};
```

### Constructor
```cpp
Node::Node(double const new_value, Node *const p_new_next_node)
    : value_{new_value},
      p_next_node_{p_new_next_node} {
    // Member initializer list does all work
}
```

### Accessors and Mutators
```cpp
double Node::value() const {
    return value_;
}

Node *Node::p_next_node() const {
    return p_next_node_;
}

void Node::value(double const new_value) {
    value_ = new_value;
}

void Node::p_next_node(Node *const p_new_next_node) {
    p_next_node_ = p_new_next_node;
}
```

---

# 4. Linked List Implementation

## Class Declaration
```cpp
class Linked_list {
public:
    Linked_list();
    ~Linked_list();
    
    double front() const;
    double back() const;
    bool empty() const;
    std::size_t size() const;
    
    void push_front(double new_value);
    void push_back(double new_value);
    void pop_front();
    void pop_back();
    void clear();
    
private:
    Node *p_list_head_;
    Node *p_list_tail_;
    std::size_t list_size_;
};
```

## Constructor - Empty List
```cpp
Linked_list::Linked_list()
    : p_list_head_{nullptr},
      p_list_tail_{nullptr},
      list_size_{0} {
}
```

**Memory state:**
```
Stack:                  Heap:
┌─────────────────┐
│ p_list_head_: ∅ │    (nothing allocated)
│ p_list_tail_: ∅ │
│ list_size_: 0   │
└─────────────────┘
```

---

# 5. Operations Deep Dive

## Push Front - The Elegant Implementation

### The Code
```cpp
void Linked_list::push_front(double new_value) {
    p_list_head_ = new Node{new_value, p_list_head_};
    
    if (p_list_tail_ == nullptr) {
        p_list_tail_ = p_list_head_;
    }
    
    ++list_size_;
}
```

### Why This Works

**For empty list** (`p_list_head_ == nullptr`):
1. `new Node{new_value, nullptr}` - creates node pointing to null
2. `p_list_head_ = address` - head points to new node
3. `p_list_tail_ = p_list_head_` - tail also points to this (only) node

**For non-empty list:**
1. `new Node{new_value, old_head_address}` - new node points to old head
2. `p_list_head_ = new_address` - head points to new node
3. Tail unchanged

### Memory Visualization - Adding 4.2 to Empty List
```
Step 1: Initial
p_list_head_ → ∅
p_list_tail_ → ∅

Step 2: new Node{4.2, nullptr} allocates at 0x303030
p_list_head_ → ∅
               0x303030: [4.2 | ∅]

Step 3: p_list_head_ = 0x303030
p_list_head_ ──→ 0x303030: [4.2 | ∅]
p_list_tail_ → ∅

Step 4: p_list_tail_ = p_list_head_
p_list_head_ ──→ 0x303030: [4.2 | ∅] ←── p_list_tail_
```

### Adding 9.1 to Non-Empty List
```
Step 1: Current state
p_list_head_ ──→ 0x303030: [4.2 | ∅] ←── p_list_tail_

Step 2: new Node{9.1, p_list_head_} allocates at 0xbdbdb8
p_list_head_ ──→ 0x303030: [4.2 | ∅]
                 0xbdbdb8: [9.1 | 0x303030]

Step 3: p_list_head_ = 0xbdbdb8
p_list_head_ ──→ 0xbdbdb8: [9.1 | ●] ──→ 0x303030: [4.2 | ∅] ←── p_list_tail_
```

## Pop Front - The Tricky One

### The WRONG Approaches

**Wrong #1: Memory Leak**
```cpp
// WRONG!
void pop_front() {
    p_list_head_ = p_list_head_->p_next_node();
    // Old head never deleted - MEMORY LEAK!
}
```

**Wrong #2: Use-After-Delete**
```cpp
// WRONG!
void pop_front() {
    delete p_list_head_;
    p_list_head_ = p_list_head_->p_next_node();  // UNDEFINED BEHAVIOR!
}
```

### The CORRECT Approach
```cpp
void Linked_list::pop_front() {
    if (!empty()) {
        Node *p_old_head{p_list_head_};              // Save old head
        p_list_head_ = p_list_head_->p_next_node();  // Update to second
        
        if (p_list_head_ == nullptr) {               // Was last node?
            p_list_tail_ = nullptr;                  // Update tail too
        }
        
        --list_size_;
        
        delete p_old_head;      // Now safe to delete
        p_old_head = nullptr;   // Good practice
    }
}
```

### Step-by-Step Visualization
```
Initial: [9.1 | ●] → [4.2 | ∅]
          ↑           ↑
        head        tail

Step 1: Save old head
p_old_head ──→ [9.1 | ●] → [4.2 | ∅]
               ↑            ↑
             head         tail

Step 2: Update head
p_old_head ──→ [9.1 | ●] → [4.2 | ∅]
                            ↑      ↑
                          head   tail

Step 3: Delete old head
                (freed)    [4.2 | ∅]
                            ↑      ↑
                          head   tail
```

## Destructor - Clean Up All Nodes

### The Problem

When a `Linked_list` goes out of scope, what happens to the nodes?
```cpp
int main() {
    Linked_list data{};
    data.push_front(4.2);
    data.push_front(9.1);
    return 0;  // data goes out of scope - MEMORY LEAK without destructor!
}
```

### The Solution
```cpp
Linked_list::~Linked_list() {
    while (!empty()) {
        pop_front();
    }
}
```

**Or even better:**
```cpp
Linked_list::~Linked_list() {
    clear();  // Reuse existing function!
}

void Linked_list::clear() {
    while (!empty()) {
        pop_front();
    }
}
```

## Traversal - Looping Through Nodes

### The Pattern
```cpp
for (Node *p_node{p_list_head_}; p_node != nullptr;
     p_node = p_node->p_next_node()) {
    
    // Access current node
    std::cout << p_node->value() << std::endl;
}
```

### Execution Trace for [6.3 | ●] → [9.1 | ●] → [4.2 | ∅]
```
Iteration 1:
  p_node = address of 6.3 node
  Print: 6.3
  p_node = p_node->p_next_node() → address of 9.1 node

Iteration 2:
  p_node = address of 9.1 node
  Print: 9.1
  p_node = p_node->p_next_node() → address of 4.2 node

Iteration 3:
  p_node = address of 4.2 node
  Print: 4.2
  p_node = p_node->p_next_node() → nullptr

Iteration 4:
  p_node = nullptr
  Loop exits
```

---

# 6. Enhancements

## Adding Size Member Variable

### Why?

**Without size tracking:**
```cpp
std::size_t size() const {
    std::size_t count{0};
    for (Node *p{p_list_head_}; p != nullptr; p = p->p_next_node()) {
        ++count;
    }
    return count;  // O(n) - must traverse entire list!
}
```

**With size tracking:**
```cpp
std::size_t size() const {
    return list_size_;  // O(1) - instant!
}
```

### Implementation

Add member variable:
```cpp
class Linked_list {
private:
    Node *p_list_head_;
    Node *p_list_tail_;
    std::size_t list_size_;  // Track size
};
```

Initialize:
```cpp
Linked_list::Linked_list()
    : p_list_head_{nullptr},
      p_list_tail_{nullptr},
      list_size_{0} {
}
```

Update in operations:
```cpp
void push_front(double new_value) {
    p_list_head_ = new Node{new_value, p_list_head_};
    if (p_list_tail_ == nullptr) {
        p_list_tail_ = p_list_head_;
    }
    ++list_size_;  // Increment
}

void pop_front() {
    if (!empty()) {
        Node *p_old_head{p_list_head_};
        p_list_head_ = p_list_head_->p_next_node();
        if (p_list_head_ == nullptr) {
            p_list_tail_ = nullptr;
        }
        --list_size_;  // Decrement
        delete p_old_head;
    }
}
```

### Best Practice: Critical Code Sections

Group all member variable updates together:
```cpp
void push_front(double new_value) {
    // Begin critical code:
    p_list_head_ = new Node{new_value, p_list_head_};
    if (p_list_tail_ == nullptr) {
        p_list_tail_ = p_list_head_;
    }
    ++list_size_;
    // End critical code
}
```

**Benefits:**
- All related changes in one place
- Easy to verify consistency
- Clear what's being modified
- Easier to maintain

## Adding Tail Pointer

### Why?

**Without tail pointer:**
```cpp
void push_back(double new_value) {
    // Must traverse to find last node - O(n)!
    Node *p_current{p_list_head_};
    while (p_current->p_next_node() != nullptr) {
        p_current = p_current->p_next_node();
    }
    p_current->p_next_node(new Node{new_value, nullptr});
}
```

**With tail pointer:**
```cpp
void push_back(double new_value) {
    if (empty()) {
        push_front(new_value);
    } else {
        p_list_tail_->p_next_node(new Node{new_value, nullptr});
        p_list_tail_ = p_list_tail_->p_next_node();
        ++list_size_;
    }
    // O(1) - instant!
}
```

### Update push_front for Tail
```cpp
void push_front(double new_value) {
    p_list_head_ = new Node{new_value, p_list_head_};
    
    if (p_list_tail_ == nullptr) {  // Was empty?
        p_list_tail_ = p_list_head_;  // First node is also last
    }
    
    ++list_size_;
}
```

### Update pop_front for Tail
```cpp
void pop_front() {
    if (!empty()) {
        Node *p_old_head{p_list_head_};
        p_list_head_ = p_list_head_->p_next_node();
        
        if (p_list_head_ == nullptr) {  // Removed last node?
            p_list_tail_ = nullptr;      // Update tail
        }
        
        --list_size_;
        delete p_old_head;
    }
}
```

### The Limitation: pop_back Still Slow

To pop the back, we must find the second-to-last node:
```cpp
void pop_back() {
    if (size() == 1) {
        pop_front();
    } else if (!empty()) {
        Node *p_new_tail{p_list_head_};
        
        // Traverse to find second-to-last - O(n)!
        while (p_new_tail->p_next_node() != p_list_tail_) {
            p_new_tail = p_new_tail->p_next_node();
        }
        
        delete p_list_tail_;
        p_list_tail_ = p_new_tail;
        p_list_tail_->p_next_node(nullptr);
        --list_size_;
    }
}
```

**Solution:** Use doubly-linked lists (nodes with pointers to both next AND previous).

---

# 7. Advanced Operations

## Concatenating Lists

### push_front(Linked_list &list)

Move all nodes from another list to the front of this list.
```
Before:
this: [7|●] → [6|●] → [5|∅]
list: [3|●] → [2|●] → [1|∅]

After this->push_front(list):
this: [3|●] → [2|●] → [1|●] → [7|●] → [6|●] → [5|∅]
list: (empty)
```

### Decision Table

| Size of this | Size of list | Action |
|--------------|--------------|--------|
| 0 | 0 | Do nothing |
| 0 | any | Swap member variables |
| any | 0 | Do nothing |
| ≥1 | ≥1 | General case |

### Implementation
```cpp
void Linked_list::push_front(Linked_list &list) {
    // Check for self-concatenation
    if (this == &list) {
        throw std::invalid_argument{
            "Cannot push a list onto itself"
        };
    }
    
    if (!list.empty()) {
        if (empty()) {
            // Just swap everything
            swap(list);
        } else {
            // Link list's tail to this's head
            list.p_list_tail_->p_next_node(p_list_head_);
            
            // Update this's head
            p_list_head_ = list.p_list_head_;
            
            // Update size
            list_size_ += list.size();
            
            // Reset list to empty
            list.p_list_head_ = nullptr;
            list.p_list_tail_ = nullptr;
            list.list_size_ = 0;
        }
    }
}
```

### Swap Helper Function
```cpp
void Linked_list::swap(Linked_list &list) {
    std::swap(p_list_head_, list.p_list_head_);
    std::swap(p_list_tail_, list.p_list_tail_);
    std::swap(list_size_, list.list_size_);
}
```

### push_back(Linked_list &list) - Clever Trick
```cpp
void Linked_list::push_back(Linked_list &list) {
    swap(list);      // Swap the two lists
    push_front(list); // Push old 'this' (now in 'list') to front
}
```

**Example:**
```
Initial:
this: [A|●] → [B|●] → [C|∅]
list: [X|●] → [Y|∅]

After swap:
this: [X|●] → [Y|∅]
list: [A|●] → [B|●] → [C|∅]

After push_front(list):
this: [A|●] → [B|●] → [C|●] → [X|●] → [Y|∅]
list: (empty)

Result: X,Y was pushed to back of A,B,C!
```

## Friendship - Printing

### The Problem

To print a list, we need to access `p_list_head_`, which is private!
```cpp
std::ostream &operator<<(std::ostream &out, Linked_list const &list) {
    for (Node *p{list.p_list_head_}; ...) {  // ERROR: private!
        // ...
    }
}
```

### The Solution: Friend Function

Declare as friend in class:
```cpp
class Linked_list {
private:
    Node *p_list_head_;
    Node *p_list_tail_;
    std::size_t list_size_;
    
    friend std::ostream &operator<<(std::ostream &out, 
                                   Linked_list const &list);
};
```

Now the function can access private members:
```cpp
std::ostream &operator<<(std::ostream &out, Linked_list const &list) {
    out << "head -> ";
    
    for (Node *p{list.p_list_head_}; p != nullptr; p = p->p_next_node()) {
        out << p->value() << " -> ";
    }
    
    out << "nullptr";
    return out;
}
```

---

# 8. Complete Code

## Time Complexity Summary

| Operation | Complexity |
|-----------|------------|
| push_front | O(1) |
| pop_front | O(1) |
| push_back (with tail) | O(1) |
| pop_back | O(n) |
| front() | O(1) |
| back() (with tail) | O(1) |
| size() (tracked) | O(1) |
| find() | O(n) |
| erase() | O(n) |
| clear() | O(n) |

## Space Complexity

- **Per node:** 1 pointer (4-8 bytes) + data
- **Per list:** 2 pointers + size (16-20 bytes)

## Arrays vs Linked Lists

### Use Arrays When:
- Need random access (arr[i])
- Size is fixed or predictable
- Memory is contiguous (cache-friendly)
- Few insertions/deletions

### Use Linked Lists When:
- Frequent insertions/deletions at front/back
- Size varies dramatically
- Don't need random access
- Want O(1) concatenation

---

# Key Takeaways

## Memory Management
1. Always save pointers before updating
2. Delete in correct order
3. Set deleted pointers to nullptr
4. Destructor must free all nodes

## Code Quality
1. Group member variable updates
2. Minimize inconsistent states
3. Reuse existing functions
4. Handle edge cases (empty, single node)

## Design Patterns
1. Elegant solutions work for all cases
2. Check for self-operations
3. Use helper functions (swap, clear)
4. Friend functions for controlled access

---

# Practice Problems

## Easy
1. Implement `reverse()` to reverse the list
2. Implement `operator==` to compare two lists
3. Add `contains(value)` to check if value exists

## Medium
1. Implement `insert_after(index, value)`
2. Add `remove_duplicates()`
3. Implement `merge(sorted_list)` for sorted lists

## Hard
1. Detect if list has a cycle
2. Find the middle element in one pass
3. Implement a doubly-linked list

---

*End of Study Guide*
