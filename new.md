Here is a **fully compiled Markdown (.md) file** containing **EVERY linked-list program**, including:

* All **pseudocode**
* All **C++ implementations**
* Unified formatting
* Table of contents
* Clean headings

You can copy/paste directly into a file named:

👉 **linked_list_library.md**

---

# 📄 **`linked_list_library.md`**

```md
# Linked List Program Library (Pseudocode + C++ — Raw Function Style)

Author: Krrish Lala  
Purpose: Complete reference for all linked-list operations in exam-style standalone C functions.

---

# Table of Contents
1. Node Structure  
2. Insert at Head  
3. Insert at Tail  
4. Insert at Index  
5. Search  
6. Get Value at Index  
7. Length  
8. Delete Head  
9. Delete Tail  
10. Delete at Index  
11. Delete by Value  
12. Reverse List  
13. Find Middle  
14. Append Two Lists  
15. Split List  
16. Merge Two Sorted Lists  
17. Merge Sort  
18. Detect Cycle  
19. Find Cycle Start  
20. Nth From End  
21. Palindrome Check  

---

# 1. Node Structure

## Pseudocode
```

Node:
int value
Node* next

````

## C++
```cpp
struct Node {
    int value;
    Node* next;
    Node(int v) : value(v), next(nullptr) {}
};
````

---

# 2. Insert at Head

## Pseudocode

```
insertHead(head, val):
    newNode = Node(val)
    newNode.next = head
    return newNode
```

## C++

```cpp
Node* insertHead(Node* head, int val) {
    Node* newNode = new Node(val);
    newNode->next = head;
    return newNode;
}
```

---

# 3. Insert at Tail

## Pseudocode

```
insertTail(head, val):
    newNode = Node(val)

    if head == null:
        return newNode

    curr = head
    while curr.next != null:
        curr = curr.next

    curr.next = newNode
    return head
```

## C++

```cpp
Node* insertTail(Node* head, int val) {
    Node* newNode = new Node(val);
    if (head == nullptr) return newNode;

    Node* curr = head;
    while (curr->next != nullptr)
        curr = curr->next;

    curr->next = newNode;
    return head;
}
```

---

# 4. Insert at Index

## Pseudocode

```
insertAt(head, index, val):
    if index == 0:
        return insertHead(head, val)

    curr = head
    pos = 0

    while curr != null and pos < index - 1:
        curr = curr.next
        pos++

    if curr == null:
        ERROR

    newNode = Node(val)
    newNode.next = curr.next
    curr.next = newNode

    return head
```

## C++

```cpp
Node* insertAt(Node* head, int index, int val) {
    if (index == 0) return insertHead(head, val);

    Node* curr = head;
    int pos = 0;

    while (curr != nullptr && pos < index - 1) {
        curr = curr->next;
        pos++;
    }

    if (curr == nullptr) return head; // out of bounds

    Node* newNode = new Node(val);
    newNode->next = curr->next;
    curr->next = newNode;

    return head;
}
```

---

# 5. Search

## Pseudocode

```
search(head, target):
    curr = head
    while curr != null:
        if curr.value == target:
            return true
        curr = curr.next
    return false
```

## C++

```cpp
bool search(Node* head, int target) {
    Node* curr = head;
    while (curr != nullptr) {
        if (curr->value == target) return true;
        curr = curr->next;
    }
    return false;
}
```

---

# 6. Get Value at Index

## Pseudocode

```
getAt(head, index):
    curr = head
    pos = 0

    while curr != null:
        if pos == index:
            return curr.value
        curr = curr.next
        pos++

    ERROR
```

## C++

```cpp
int getAt(Node* head, int index) {
    Node* curr = head;
    int pos = 0;

    while (curr != nullptr) {
        if (pos == index) return curr->value;
        curr = curr->next;
        pos++;
    }

    throw "Index out of bounds";
}
```

---

# 7. Length

## Pseudocode

```
length(head):
    count = 0
    curr = head
    while curr != null:
        count++
        curr = curr.next
    return count
```

## C++

```cpp
int length(Node* head) {
    int count = 0;
    Node* curr = head;
    while (curr != nullptr) {
        count++;
        curr = curr->next;
    }
    return count;
}
```

---

# 8. Delete Head

## Pseudocode

```
deleteHead(head):
    if head == null:
        return null

    temp = head
    head = head.next
    delete temp

    return head
```

## C++

```cpp
Node* deleteHead(Node* head) {
    if (head == nullptr) return nullptr;

    Node* temp = head;
    head = head->next;
    delete temp;

    return head;
}
```

---

# 9. Delete Tail

## Pseudocode

```
deleteTail(head):
    if head == null:
        return null

    if head.next == null:
        delete head
        return null

    curr = head
    while curr.next.next != null:
        curr = curr.next

    delete curr.next
    curr.next = null
    return head
```

## C++

```cpp
Node* deleteTail(Node* head) {
    if (head == nullptr) return nullptr;

    if (head->next == nullptr) {
        delete head;
        return nullptr;
    }

    Node* curr = head;
    while (curr->next->next != nullptr)
        curr = curr->next;

    delete curr->next;
    curr->next = nullptr;
    return head;
}
```

---

# 10. Delete at Index

## Pseudocode

```
deleteAt(head, index):
    if index == 0:
        return deleteHead(head)

    curr = head
    pos = 0

    while curr != null and pos < index - 1:
        curr = curr.next
        pos++

    if curr == null or curr.next == null:
        ERROR

    temp = curr.next
    curr.next = temp.next
    delete temp

    return head
```

## C++

```cpp
Node* deleteAt(Node* head, int index) {
    if (index == 0) return deleteHead(head);

    Node* curr = head;
    int pos = 0;

    while (curr != nullptr && pos < index - 1) {
        curr = curr->next;
        pos++;
    }

    if (curr == nullptr || curr->next == nullptr) return head;

    Node* temp = curr->next;
    curr->next = temp->next;
    delete temp;

    return head;
}
```

---

# 11. Delete by Value

## Pseudocode

```
deleteValue(head, target):
    if head == null:
        return head

    if head.value == target:
        return deleteHead(head)

    curr = head
    while curr.next != null and curr.next.value != target:
        curr = curr.next

    if curr.next == null:
        return head

    temp = curr.next
    curr.next = temp.next
    delete temp

    return head
```

## C++

```cpp
Node* deleteValue(Node* head, int target) {
    if (head == nullptr) return head;

    if (head->value == target)
        return deleteHead(head);

    Node* curr = head;
    while (curr->next != nullptr && curr->next->value != target)
        curr = curr->next;

    if (curr->next == nullptr) return head;

    Node* temp = curr->next;
    curr->next = temp->next;
    delete temp;

    return head;
}
```

---

# 12. Reverse List (Iterative)

## Pseudocode

```
reverse(head):
    prev = null
    curr = head

    while curr != null:
        next = curr.next
        curr.next = prev
        prev = curr
        curr = next

    return prev
```

## C++

```cpp
Node* reverse(Node* head) {
    Node* prev = nullptr;
    Node* curr = head;

    while (curr != nullptr) {
        Node* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }

    return prev;
}
```

---

# 13. Find Middle

## Pseudocode

```
findMiddle(head):
    slow = head
    fast = head

    while fast != null and fast.next != null:
        slow = slow.next
        fast = fast.next.next

    return slow
```

## C++

```cpp
Node* findMiddle(Node* head) {
    Node* slow = head;
    Node* fast = head;

    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
    }

    return slow;
}
```

---

# 14. Append Two Lists

## Pseudocode

```
append(a, b):
    if a == null:
        return b

    curr = a
    while curr.next != null:
        curr = curr.next

    curr.next = b
    return a
```

## C++

```cpp
Node* append(Node* a, Node* b) {
    if (a == nullptr) return b;

    Node* curr = a;
    while (curr->next != nullptr)
        curr = curr->next;

    curr->next = b;
    return a;
}
```

---

# 15. Split List

## Pseudocode

```
split(head):
    if head == null:
        return (null, null)

    slow = head
    fast = head.next

    while fast != null and fast.next != null:
        slow = slow.next
        fast = fast.next.next

    second = slow.next
    slow.next = null

    return (head, second)
```

## C++

```cpp
void split(Node* head, Node*& first, Node*& second) {
    if (head == nullptr) {
        first = second = nullptr;
        return;
    }

    Node* slow = head;
    Node* fast = head->next;

    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
    }

    first = head;
    second = slow->next;
    slow->next = nullptr;
}
```

---

# 16. Merge Two Sorted Lists

## Pseudocode

```
merge(a, b):
    if a == null: return b
    if b == null: return a

    if a.value <= b.value:
        a.next = merge(a.next, b)
        return a
    else:
        b.next = merge(a, b.next)
        return b
```

## C++

```cpp
Node* merge(Node* a, Node* b) {
    if (a == nullptr) return b;
    if (b == nullptr) return a;

    if (a->value <= b->value) {
        a->next = merge(a->next, b);
        return a;
    } else {
        b->next = merge(a, b->next);
        return b;
    }
}
```

---

# 17. Merge Sort

## Pseudocode

```
mergeSort(head):
    if head == null or head.next == null:
        return head

    slow = head
    fast = head.next

    while fast != null and fast.next != null:
        slow = slow.next
        fast = fast.next.next

    second = slow.next
    slow.next = null

    left = mergeSort(head)
    right = mergeSort(second)

    return merge(left, right)
```

## C++

```cpp
Node* mergeSort(Node* head) {
    if (head == nullptr || head->next == nullptr)
        return head;

    Node* slow = head;
    Node* fast = head->next;

    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
    }

    Node* second = slow->next;
    slow->next = nullptr;

    Node* left = mergeSort(head);
    Node* right = mergeSort(second);

    return merge(left, right);
}
```

---

# 18. Detect Cycle

## Pseudocode

```
hasCycle(head):
    slow = head
    fast = head

    while fast != null and fast.next != null:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return true

    return false
```

## C++

```cpp
bool hasCycle(Node* head) {
    Node* slow = head;
    Node* fast = head;

    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }

    return false;
}
```

---

# 19. Find Cycle Start

## Pseudocode

```
findCycleStart(head):
    slow = head
    fast = head

    while fast != null and fast.next != null:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break

    if fast == null or fast.next == null:
        return null

    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next

    return slow
```

## C++

```cpp
Node* findCycleStart(Node* head) {
    Node* slow = head;
    Node* fast = head;

    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;

        if (slow == fast) {
            slow = head;
            while (slow != fast) {
                slow = slow->next;
                fast = fast->next;
            }
            return slow;
        }
    }

    return nullptr;
}
```

---

# 20. Nth From End

## Pseudocode

```
nthFromEnd(head, n):
    fast = head
    slow = head

    repeat n times:
        fast = fast.next

    while fast != null:
        fast = fast.next
        slow = slow.next

    return slow
```

## C++

```cpp
Node* nthFromEnd(Node* head, int n) {
    Node* fast = head;
    Node* slow = head;

    for (int i = 0; i < n; i++) {
        if (fast == nullptr) return nullptr;
        fast = fast->next;
    }

    while (fast != nullptr) {
        fast = fast->next;
        slow = slow->next;
    }

    return slow;
}
```

---

# 21. Palindrome Check

## Pseudocode

```
isPalindrome(head):
    if head is null or one node:
        return true

    mid = findMiddle(head)
    second = reverse(mid)

    p1 = head
    p2 = second

    while p2 != null:
        if p1.value != p2.value:
            return false
        p1 = p1.next
        p2 = p2.next

    return true
```

## C++

```cpp
bool isPalindrome(Node* head) {
    if (head == nullptr || head->next == nullptr)
        return true;

    Node* slow = head;
    Node* fast = head;

    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
    }

    Node* second = reverse(slow);

    Node* p1 = head;
    Node* p2 = second;

    while (p2 != nullptr) {
        if (p1->value != p2->value)
            return false;
        p1 = p1->next;
        p2 = p2->next;
    }

    return true;
}
```

---

# End of File

```

---

# ✅ Your Markdown file is ready.

If you want:

📌 **A downloadable .md file**  
📌 **A PDF version**  
📌 **Syntax-highlighted version for GitHub**  
📌 **A reduced “exam-cheat-sheet” 1-page version**

Just tell me!
```
