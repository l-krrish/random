# Complete Linked List Implementation Guide

## Overview

This guide provides a complete implementation of a singly linked list using the **dummy node pattern** with a tracked tail pointer for efficient operations.

## Key Features

- **Dummy Head Node**: Simplifies edge case handling
- **Tail Pointer**: Enables O(1) insertions at the end
- **Size Tracking**: Provides O(1) size queries
- **Comprehensive API**: All common linked list operations

---

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

---

## Complete Implementation

```cpp
#include <iostream>

using std::cout;
using std::endl;

class ListNode {
public:
    int val_;
    ListNode* next = nullptr;

    ListNode(int val) {
        val_ = val;
    }
};

// Implementation for Singly Linked List with Dummy Node
class LinkedList {
private:
    ListNode* head;
    ListNode* tail;
    int list_size_;  // Track size for O(1) size() operation

public:
    LinkedList() {
        // Init the list with a 'dummy' node
        head = new ListNode(-1);
        tail = head;
        list_size_ = 0;
    }

    // ========== BASIC OPERATIONS FROM TABLE ==========
    
    // push_front - O(1)
    // Adds element to the beginning of the list
    void push_front(int val) {
        ListNode* newNode = new ListNode(val);
        newNode->next = head->next;
        head->next = newNode;
        
        // If list was empty, update tail
        if (tail == head) {
            tail = newNode;
        }
        
        list_size_++;
    }

    // pop_front - O(1)
    // Removes element from the beginning of the list
    void pop_front() {
        if (empty()) return;
        
        ListNode* toDelete = head->next;
        head->next = head->next->next;
        
        // If we removed the only element, update tail
        if (toDelete == tail) {
            tail = head;
        }
        
        delete toDelete;
        list_size_--;
    }

    // push_back (with tail) - O(1)
    // Adds element to the end of the list
    void push_back(int val) {
        tail->next = new ListNode(val);
        tail = tail->next;
        list_size_++;
    }

    // pop_back - O(n)
    // Removes element from the end of the list
    // Must traverse to find second-to-last node
    void pop_back() {
        if (empty()) return;
        
        ListNode* curr = head;
        
        // Find node before tail
        while (curr->next != tail) {
            curr = curr->next;
        }
        
        delete tail;
        tail = curr;
        tail->next = nullptr;
        list_size_--;
    }

    // front() - O(1)
    // Returns the value at the beginning of the list
    int front() {
        if (empty()) {
            throw std::runtime_error("List is empty");
        }
        return head->next->val_;
    }

    // back() (with tail) - O(1)
    // Returns the value at the end of the list
    int back() {
        if (empty()) {
            throw std::runtime_error("List is empty");
        }
        return tail->val_;
    }

    // size() (tracked) - O(1)
    // Returns the number of elements in the list
    int size() {
        return list_size_;
    }

    // find() - O(n)
    // Returns index of value, -1 if not found
    int find(int val) {
        ListNode* curr = head->next;
        int index = 0;
        
        while (curr) {
            if (curr->val_ == val) {
                return index;
            }
            curr = curr->next;
            index++;
        }
        return -1;
    }

    // erase() - O(n)
    // Remove element at given index
    void erase(int index) {
        if (index < 0 || index >= list_size_) return;
        
        int i = 0;
        ListNode* curr = head;
        
        // Find node before the one to remove
        while (i < index) {
            i++;
            curr = curr->next;
        }
        
        // Remove the node ahead of curr
        if (curr && curr->next) {
            ListNode* toDelete = curr->next;
            curr->next = curr->next->next;
            
            // If removed tail, update tail pointer
            if (toDelete == tail) {
                tail = curr;
            }
            
            delete toDelete;
            list_size_--;
        }
    }

    // clear() - O(n)
    // Removes all elements from the list
    void clear() {
        ListNode* curr = head->next;
        while (curr) {
            ListNode* next = curr->next;
            delete curr;
            curr = next;
        }
        head->next = nullptr;
        tail = head;
        list_size_ = 0;
    }

    // ========== ADDITIONAL HELPER FUNCTIONS ==========

    // Check if list is empty - O(1)
    bool empty() {
        return head->next == nullptr;
    }

    // Insert at end (alias for push_back) - O(1)
    void insertEnd(int val) {
        push_back(val);
    }

    // Insert at beginning (alias for push_front) - O(1)
    void insertFront(int val) {
        push_front(val);
    }

    // Insert at index - O(n)
    // Inserts element at specified position
    void insert(int index, int val) {
        if (index < 0 || index > list_size_) return;
        
        if (index == 0) {
            push_front(val);
            return;
        }
        if (index == list_size_) {
            push_back(val);
            return;
        }
        
        int i = 0;
        ListNode* curr = head;
        
        // Find node before insertion point
        while (i < index) {
            i++;
            curr = curr->next;
        }
        
        ListNode* newNode = new ListNode(val);
        newNode->next = curr->next;
        curr->next = newNode;
        list_size_++;
    }

    // Get value at index - O(n)
    int get(int index) {
        if (index < 0 || index >= list_size_) {
            throw std::out_of_range("Index out of bounds");
        }
        
        int i = 0;
        ListNode* curr = head->next;
        
        while (i < index) {
            i++;
            curr = curr->next;
        }
        
        return curr->val_;
    }

    // ========== SEARCH OPERATIONS ==========

    // Check if value exists - O(n)
    bool contains(int val) {
        return find(val) != -1;
    }

    // ========== MODIFICATION OPERATIONS ==========

    // Reverse the list - O(n)
    // Reverses the order of all elements
    void reverse() {
        ListNode* prev = nullptr;
        ListNode* curr = head->next;
        tail = head->next;  // Old head becomes new tail
        
        while (curr) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        
        head->next = prev;
        
        // If list was empty, tail should point to head
        if (tail == nullptr) {
            tail = head;
        }
    }

    // Remove duplicates (assumes unsorted list) - O(n²)
    // Removes all duplicate values from the list
    void removeDuplicates() {
        ListNode* curr = head->next;
        
        while (curr) {
            ListNode* runner = curr;
            
            // Check all nodes after curr
            while (runner->next) {
                if (runner->next->val_ == curr->val_) {
                    // Found duplicate, remove it
                    ListNode* toDelete = runner->next;
                    runner->next = runner->next->next;
                    
                    // Update tail if we deleted it
                    if (toDelete == tail) {
                        tail = runner;
                    }
                    
                    delete toDelete;
                    list_size_--;
                } else {
                    runner = runner->next;
                }
            }
            curr = curr->next;
        }
    }

    // Rotate list to the right by k positions - O(n)
    // Example: [1,2,3,4,5] rotated by 2 becomes [4,5,1,2,3]
    void rotate(int k) {
        if (empty() || k == 0) return;
        
        // Normalize k
        k = k % list_size_;
        if (k == 0) return;
        
        // Find the node before the new head
        int stepsToNewTail = list_size_ - k;
        ListNode* newTail = head;
        for (int i = 0; i < stepsToNewTail; i++) {
            newTail = newTail->next;
        }
        
        // Rearrange pointers
        ListNode* newHead = newTail->next;
        newTail->next = nullptr;
        tail->next = head->next;
        head->next = newHead;
        tail = newTail;
    }

    // Remove all occurrences of a value - O(n)
    void removeValue(int val) {
        ListNode* curr = head;
        
        while (curr->next) {
            if (curr->next->val_ == val) {
                ListNode* toDelete = curr->next;
                curr->next = curr->next->next;
                
                // Update tail if necessary
                if (toDelete == tail) {
                    tail = curr;
                }
                
                delete toDelete;
                list_size_--;
            } else {
                curr = curr->next;
            }
        }
    }

    // ========== ADVANCED OPERATIONS ==========

    // Detect if list has a cycle - O(n)
    // Uses Floyd's cycle detection algorithm (tortoise and hare)
    bool hasCycle() {
        ListNode* slow = head->next;
        ListNode* fast = head->next;
        
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }

    // Find middle element - O(n)
    // Uses slow and fast pointer technique
    int getMiddle() {
        if (empty()) {
            throw std::runtime_error("List is empty");
        }
        
        ListNode* slow = head->next;
        ListNode* fast = head->next;
        
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        return slow->val_;
    }

    // Get nth node from end - O(n)
    // Uses two-pointer technique with n-step gap
    int getNthFromEnd(int n) {
        if (n <= 0 || n > list_size_) {
            throw std::out_of_range("Invalid position");
        }
        
        ListNode* first = head->next;
        ListNode* second = head->next;
        
        // Move first pointer n steps ahead
        for (int i = 0; i < n; i++) {
            first = first->next;
        }
        
        // Move both pointers until first reaches end
        while (first) {
            first = first->next;
            second = second->next;
        }
        
        return second->val_;
    }

    // ========== UTILITY OPERATIONS ==========

    // Print the list - O(n)
    void print() {
        ListNode* curr = head->next;  // Skip dummy
        cout << "[ ";
        while (curr) {
            cout << curr->val_;
            if (curr->next) cout << " -> ";
            curr = curr->next;
        }
        cout << " ]" << endl;
    }

    // Print with size info - O(n)
    void printDetailed() {
        cout << "Size: " << list_size_ << ", List: ";
        print();
    }

    // ========== COMPARISON OPERATIONS ==========

    // Check if two lists are equal - O(n)
    bool equals(LinkedList& other) {
        if (list_size_ != other.list_size_) {
            return false;
        }
        
        ListNode* curr1 = head->next;
        ListNode* curr2 = other.head->next;
        
        while (curr1 && curr2) {
            if (curr1->val_ != curr2->val_) {
                return false;
            }
            curr1 = curr1->next;
            curr2 = curr2->next;
        }
        
        return true;
    }

    // ========== DESTRUCTOR ==========

    // Destructor - clean up memory
    ~LinkedList() {
        clear();
        delete head;  // Delete dummy node
    }
};
```

---

## Example Usage

```cpp
int main() {
    LinkedList list;
    
    cout << "=== Testing push_front and push_back ===" << endl;
    list.push_back(3);
    list.push_back(4);
    list.push_back(5);
    list.push_front(2);
    list.push_front(1);
    list.print();  // [ 1 -> 2 -> 3 -> 4 -> 5 ]
    cout << "Size: " << list.size() << endl;
    
    cout << "\n=== Testing front() and back() ===" << endl;
    cout << "Front: " << list.front() << endl;  // 1
    cout << "Back: " << list.back() << endl;    // 5
    
    cout << "\n=== Testing find() ===" << endl;
    cout << "Index of 3: " << list.find(3) << endl;  // 2
    cout << "Index of 99: " << list.find(99) << endl; // -1
    
    cout << "\n=== Testing erase() ===" << endl;
    list.erase(2);  // Remove element at index 2 (value 3)
    list.print();   // [ 1 -> 2 -> 4 -> 5 ]
    
    cout << "\n=== Testing pop_front and pop_back ===" << endl;
    list.pop_front();
    list.print();  // [ 2 -> 4 -> 5 ]
    list.pop_back();
    list.print();  // [ 2 -> 4 ]
    
    cout << "\n=== Testing insert() ===" << endl;
    list.insert(0, 1);   // Insert at beginning
    list.insert(2, 3);   // Insert in middle
    list.insert(4, 5);   // Insert at end
    list.print();  // [ 1 -> 2 -> 3 -> 4 -> 5 ]
    
    cout << "\n=== Testing get() ===" << endl;
    cout << "Element at index 2: " << list.get(2) << endl;  // 3
    
    cout << "\n=== Testing reverse() ===" << endl;
    list.reverse();
    list.print();  // [ 5 -> 4 -> 3 -> 2 -> 1 ]
    
    cout << "\n=== Testing rotate() ===" << endl;
    list.rotate(2);
    list.print();  // [ 2 -> 1 -> 5 -> 4 -> 3 ]
    
    cout << "\n=== Testing getMiddle() ===" << endl;
    cout << "Middle element: " << list.getMiddle() << endl;
    
    cout << "\n=== Testing clear() ===" << endl;
    list.clear();
    cout << "After clear, size: " << list.size() << endl;  // 0
    cout << "Is empty? " << (list.empty() ? "Yes" : "No") << endl;
    
    return 0;
}
```

---

## Function Categories

### Basic Operations (O(1) operations)
- `push_front(val)` - Add to beginning
- `pop_front()` - Remove from beginning
- `push_back(val)` - Add to end
- `front()` - Get first element
- `back()` - Get last element
- `size()` - Get number of elements
- `empty()` - Check if list is empty

### O(n) Operations
- `pop_back()` - Remove from end
- `find(val)` - Find index of value
- `erase(index)` - Remove at index
- `clear()` - Remove all elements
- `insert(index, val)` - Insert at index
- `get(index)` - Get value at index

### Search Operations
- `contains(val)` - Check if value exists
- `find(val)` - Get index of value

### Modification Operations
- `reverse()` - Reverse the list
- `removeDuplicates()` - Remove duplicate values
- `rotate(k)` - Rotate list right by k positions
- `removeValue(val)` - Remove all occurrences

### Advanced Operations
- `hasCycle()` - Detect cycles (Floyd's algorithm)
- `getMiddle()` - Find middle element
- `getNthFromEnd(n)` - Get nth element from end

### Utility Operations
- `print()` - Display the list
- `printDetailed()` - Display with size
- `equals(other)` - Compare two lists

---

## Key Design Decisions

### 1. Dummy Node Pattern
- **Head always points to dummy** (value -1)
- **Actual data starts at** `head->next`
- **Eliminates special cases** for empty list operations

### 2. Tail Pointer
- Enables **O(1) insertions** at the end
- Must be **updated** when:
  - Inserting when list is empty
  - Removing the tail node
  - Inserting at the end

### 3. Size Tracking
- Maintains `list_size_` for **O(1) size queries**
- Must be **updated** in all modification operations:
  - Increment: `push_front`, `push_back`, `insert`
  - Decrement: `pop_front`, `pop_back`, `erase`, `removeValue`
  - Reset: `clear`

---

## Memory Management

### Constructor
```cpp
LinkedList() {
    head = new ListNode(-1);  // Allocate dummy node
    tail = head;
    list_size_ = 0;
}
```

### Destructor
```cpp
~LinkedList() {
    clear();      // Delete all nodes
    delete head;  // Delete dummy node
}
```

### Best Practices
1. **Always delete nodes** when removing
2. **Update tail** when removing last element
3. **Set pointers to nullptr** after deletion
4. **Check for empty** before accessing elements

---

## Common Pitfalls to Avoid

### 1. Forgetting to Update Tail
```cpp
// ❌ WRONG
void push_front(int val) {
    head->next = new ListNode(val);
    // Forgot to check if list was empty!
}

// ✅ CORRECT
void push_front(int val) {
    ListNode* newNode = new ListNode(val);
    newNode->next = head->next;
    head->next = newNode;
    if (tail == head) {  // List was empty
        tail = newNode;
    }
}
```

### 2. Forgetting to Update Size
```cpp
// ❌ WRONG
void insert(int index, int val) {
    // ... insertion code ...
    // Forgot to increment list_size_!
}

// ✅ CORRECT
void insert(int index, int val) {
    // ... insertion code ...
    list_size_++;  // Always update size
}
```

### 3. Not Handling Empty List
```cpp
// ❌ WRONG
int front() {
    return head->next->val_;  // Crash if empty!
}

// ✅ CORRECT
int front() {
    if (empty()) {
        throw std::runtime_error("List is empty");
    }
    return head->next->val_;
}
```

---

## Interview Tips

### Common Linked List Questions

1. **Reverse a linked list** - Use three pointers technique
2. **Detect cycle** - Floyd's algorithm (slow/fast pointers)
3. **Find middle** - Slow/fast pointer technique
4. **Merge two sorted lists** - Compare and link
5. **Remove nth from end** - Two pointers with gap
6. **Check palindrome** - Reverse second half, compare

### Time/Space Complexity
- Most operations: **O(n) time, O(1) space**
- Recursive solutions: **O(n) space** (call stack)

### Edge Cases to Consider
- Empty list
- Single element
- Two elements
- Operations at head/tail
- Middle operations

---

## Advantages of Dummy Node Approach

### Pros
✅ Fewer edge cases  
✅ More uniform code  
✅ Easier to maintain  
✅ Less error-prone  
✅ No special handling for empty list  

### Cons
❌ One extra node (minimal memory cost)  
❌ Slightly less intuitive initially  
❌ One extra pointer dereference  

---

## Comparison: Array vs Linked List

| Feature | Array | Linked List |
|---------|-------|-------------|
| Access by index | O(1) | O(n) |
| Insert at front | O(n) | O(1) |
| Insert at end | O(1) amortized | O(1) with tail |
| Insert at middle | O(n) | O(n) |
| Delete at front | O(n) | O(1) |
| Delete at end | O(1) | O(n) without tail pointer |
| Memory | Contiguous, cache-friendly | Scattered, extra pointer space |
| Size | Fixed or requires reallocation | Dynamic |

---

## Practice Problems

### Easy
1. Implement `count(val)` - count occurrences of a value
2. Implement `min()` and `max()` - find minimum and maximum
3. Implement `sum()` - sum all elements

### Medium
1. Merge two sorted linked lists
2. Remove every kth node
3. Partition list around value x
4. Add two numbers represented as linked lists

### Hard
1. Clone a linked list with random pointers
2. Flatten a multilevel doubly linked list
3. LRU Cache implementation using linked list
4. Find intersection point of two linked lists

---

## References

- [University of Waterloo ECE 150 Course Notes](#)
- [C++ Standard Library - std::forward_list](https://en.cppreference.com/w/cpp/container/forward_list)
- [Data Structures and Algorithms](https://www.geeksforgeeks.org/data-structures/linked-list/)

---

*Last updated: December 2024*
