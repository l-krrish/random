# Comprehensive Recursion Examples in C++

**ECE 150 - Fundamentals of Programming**

---

## 📚 Table of Contents

1. [Introduction](#introduction)
2. [Recursive Mathematical Functions](#recursive-mathematical-functions)
   - [Factorial](#factorial)
   - [Binomial Coefficients](#binomial-coefficients)
   - [Fibonacci Numbers](#fibonacci-numbers)
   - [Power Functions](#power-functions)
3. [Sorting Algorithms](#sorting-algorithms)
   - [Selection Sort](#selection-sort)
   - [Insertion Sort](#insertion-sort)
   - [Merge Sort](#merge-sort)
4. [Binary Number Printing](#binary-number-printing)
5. [Binary Search](#binary-search)
6. [Subset Generation](#subset-generation)
7. [Jumper Configuration Problem](#jumper-configuration-problem)
8. [Complete Source Code](#complete-source-code)
9. [How to Compile and Run](#how-to-compile-and-run)

---

## Introduction

This document contains complete implementations of various recursive algorithms with detailed explanations and examples. Recursion is a powerful programming technique where a function calls itself to solve smaller instances of the same problem.

### Key Concepts of Recursion

Every recursive function must have:
1. **Base Case**: The condition that stops recursion
2. **Recursive Case**: The part where the function calls itself with a simpler problem

---

## Recursive Mathematical Functions

### Factorial

**Mathematical Definition:**
```
n! = 1                if n ≤ 1
n! = n × (n-1)!       if n ≥ 2
```

**Example:** `5! = 5 × 4 × 3 × 2 × 1 = 120`

**How Recursion Works:**
```
factorial(5)
  → 5 × factorial(4)
  → 5 × 4 × factorial(3)
  → 5 × 4 × 3 × factorial(2)
  → 5 × 4 × 3 × 2 × factorial(1)
  → 5 × 4 × 3 × 2 × 1
  → 120
```

**Implementation:**
```cpp
unsigned int factorial(unsigned int n) {
    // BASE CASE: Stop recursion when n ≤ 1
    if (n <= 1) {
        return 1;
    }
    
    // RECURSIVE CASE: n! = n × (n-1)!
    return n * factorial(n - 1);
}
```

**Time Complexity:** O(n)  
**Space Complexity:** O(n) due to call stack

---

### Binomial Coefficients

**Also known as:** "n choose k" or C(n,k)

**Represents:** Number of ways to choose k items from n items

**Mathematical Definition (Pascal's Triangle):**
```
C(n,k) = 0                        if k > n
C(n,k) = 1                        if k = 0 or k = n
C(n,k) = C(n-1,k) + C(n-1,k-1)    otherwise
```

**Example:** `C(5,2) = 10` (there are 10 ways to choose 2 items from 5)

**Pascal's Triangle:**
```
        1
      1   1
    1   2   1
  1   3   3   1
1   4   6   4   1
```
Each number is the sum of the two above it.

**Implementation:**
```cpp
unsigned int binomial(unsigned int n, unsigned int k) {
    // BASE CASE 1: Can't choose more items than we have
    if (k > n) {
        return 0;
    }
    
    // BASE CASE 2: Choosing 0 or all items = 1 way
    if ((k == 0) || (k == n)) {
        return 1;
    }
    
    // RECURSIVE CASE: Use Pascal's identity
    return binomial(n - 1, k) + binomial(n - 1, k - 1);
}
```

**Time Complexity:** O(2^n) - exponential! Very slow for large n  
**Space Complexity:** O(n)

---

### Fibonacci Numbers

**Mathematical Definition:**
```
F(0) = 0
F(1) = 1
F(n) = F(n-1) + F(n-2)    for n ≥ 2
```

**Sequence:** 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144...

#### Naive Recursive Implementation (⚠️ INEFFICIENT)

```cpp
unsigned int fibonacci_recursive(unsigned int n) {
    // BASE CASES
    if (n == 0) return 0;
    if (n == 1) return 1;
    
    // RECURSIVE CASE
    return fibonacci_recursive(n - 1) + fibonacci_recursive(n - 2);
}
```

**Problem with Naive Recursion:**

This implementation recalculates the same values many times!

```
                    fib(5)
                   /      \
              fib(4)      fib(3)
             /     \      /     \
        fib(3)   fib(2) fib(2) fib(1)
        /    \   /   \   /   \
    fib(2) fib(1) ...  ...  ...
```

Notice `fib(3)` is calculated twice, `fib(2)` three times, etc.

**Performance:** For F(47), this takes ~109 seconds!

**Time Complexity:** O(2^n) - exponential!

#### Iterative Implementation (✅ EFFICIENT)

```cpp
unsigned int fibonacci_iterative(unsigned int n) {
    unsigned int values[2]{0, 1};
    
    // Build up from F(2) to F(n)
    for (unsigned int k{2}; k <= n; ++k) {
        values[k % 2] = values[0] + values[1];
    }
    
    return values[n % 2];
}
```

**Performance:** For F(47), this takes ~0.015 seconds!

That's **over 7000 times faster!**

**Time Complexity:** O(n) - linear  
**Space Complexity:** O(1) - constant

**Key Lesson:** Not all recursive solutions are efficient!

---

### Power Functions

Calculate x^n for integer exponent n.

#### Basic Recursive Implementation

**Mathematical Definition:**
```
x^0 = 1
x^(-n) = 1 / x^n
x^n = x × x^(n-1)    for n > 0
```

**Example:** `2^5 = 2 × 2 × 2 × 2 × 2 = 32`

```cpp
double power_basic(double x, int n) {
    // BASE CASE
    if (n == 0) return 1.0;
    
    // Handle negative exponents
    if (n < 0) return 1.0 / power_basic(x, -n);
    
    // RECURSIVE CASE: x^n = x × x^(n-1)
    return x * power_basic(x, n - 1);
}
```

**Time Complexity:** O(n) - makes n-1 recursive calls

#### Optimized Recursive Implementation (Divide and Conquer)

**Mathematical Definition:**
```
x^0 = 1
x^(-n) = 1 / x^n
x^n = (x^(n/2))^2       if n is even
x^n = x × (x^(n/2))^2   if n is odd
```

**Example:** 
```
2^8 = (2^4)^2 = ((2^2)^2)^2 = (4^2)^2 = 16^2 = 256
```

Only 3 multiplications instead of 7!

```cpp
double power_optimized(double x, int n) {
    // BASE CASE
    if (n == 0) return 1.0;
    
    // Handle negative exponents
    if (n < 0) return 1.0 / power_optimized(x, -n);
    
    // CRITICAL: Store result to avoid duplicate calculations!
    double half_power = power_optimized(x, n / 2);
    
    // If n is even: x^n = (x^(n/2))^2
    if ((n % 2) == 0) {
        return half_power * half_power;
    }
    // If n is odd: x^n = x × (x^(n/2))^2
    else {
        return x * half_power * half_power;
    }
}
```

**Time Complexity:** O(log n) - much faster!  
**Space Complexity:** O(log n)

**Key Insight:** `2^8` needs only 3 multiplications instead of 7!

---

## Sorting Algorithms

### Selection Sort

**Strategy:**
1. Find the largest element in the array
2. Swap it with the last element
3. Recursively sort the first n-1 elements

**Example:**
```
[3, 1, 4, 2]
Step 1: Find max (4), swap with last: [3, 1, 2, 4]
Step 2: Sort [3, 1, 2]:
        Find max (3), swap: [2, 1, 3] | 4
Step 3: Sort [2, 1]:
        Find max (2), swap: [1, 2] | 3, 4
Step 4: Sort [1]: Already sorted!
Result: [1, 2, 3, 4]
```

**Implementation:**
```cpp
void selection_sort(double array[], size_t capacity) {
    // BASE CASE: Array of 0 or 1 elements is sorted
    if (capacity <= 1) {
        return;
    }
    
    // Find maximum element
    size_t max_index = find_max(array, capacity);
    
    // Swap max with last element
    if (array[max_index] > array[capacity - 1]) {
        swap(array[max_index], array[capacity - 1]);
    }
    
    // RECURSIVE CASE: Sort first n-1 elements
    selection_sort(array, capacity - 1);
}
```

**Time Complexity:** O(n²)  
**Space Complexity:** O(n) due to recursion stack

---

### Insertion Sort

**Strategy:**
1. Recursively sort the first n-1 elements
2. Insert the last element into the sorted portion

**Example:**
```
[3, 1, 4, 2]
Step 1: Sort [3, 1, 4]:
        Sort [3, 1]:
          Sort [3]: Already sorted
          Insert 1: [1, 3]
        Insert 4: [1, 3, 4]
Step 2: Insert 2: [1, 2, 3, 4]
```

**Implementation:**
```cpp
void insertion_sort(double array[], size_t capacity) {
    // BASE CASE
    if (capacity <= 1) {
        return;
    }
    
    // RECURSIVE CASE: Sort first n-1 elements
    insertion_sort(array, capacity - 1);
    
    // Insert last element into sorted portion
    insert(array, capacity);
}
```

**Time Complexity:** O(n²)  
**Space Complexity:** O(n)

---

### Merge Sort

**Strategy (Divide and Conquer):**
1. Divide array into two halves
2. Recursively sort each half
3. Merge the sorted halves

**Example:**
```
[3, 1, 4, 2]

Divide:  [3, 1] | [4, 2]

Sort left:  [3, 1]
  Divide:   [3] | [1]
  Merge:    [1, 3]

Sort right: [4, 2]
  Divide:   [4] | [2]
  Merge:    [2, 4]

Merge: [1, 3] and [2, 4] → [1, 2, 3, 4]
```

**Implementation:**
```cpp
void merge_sort(double array[], size_t capacity) {
    // BASE CASE
    if (capacity <= 1) {
        return;
    }
    
    // Divide into two halves
    size_t capacity_1 = capacity / 2;
    size_t capacity_2 = capacity - capacity_1;
    
    // RECURSIVE CASE: Sort both halves
    merge_sort(array, capacity_1);
    merge_sort(array + capacity_1, capacity_2);
    
    // Merge sorted halves
    merge(array, capacity_1, capacity_2);
}
```

**Time Complexity:** O(n log n) - much better than O(n²)!  
**Space Complexity:** O(n)

This is one of the most efficient general-purpose sorting algorithms.

---

## Binary Number Printing

**Goal:** Print the binary representation of an integer

**Strategy:**
- To print 13 in binary (which is 1101):
  1. Last bit is `13 % 2 = 1`
  2. Before printing that, print `13/2 = 6` in binary
  3. Continue recursively until we reach 0 or 1

**Example Trace for 13:**
```
print_binary(13)
  print_binary(6)
    print_binary(3)
      print_binary(1)  → prints "1"
      prints 3%2 = 1
    prints 6%2 = 0
  prints 13%2 = 1
Result: "1101"
```

**Implementation:**
```cpp
void print_binary(int const n) {
    // Handle negative numbers
    if (n < 0) {
        cout << "-";
        print_binary(-n);
        return;
    }
    
    // BASE CASE: Single bit
    if ((n == 0) || (n == 1)) {
        cout << n;
        return;
    }
    
    // RECURSIVE CASE
    print_binary(n / 2);    // Print all bits except last
    cout << (n % 2);        // Print last bit
}
```

**Examples:**
- `print_binary(5)` → "101"
- `print_binary(13)` → "1101"
- `print_binary(-5)` → "-101"

---

## Binary Search

**Goal:** Search for a value in a SORTED array (much faster than linear search!)

**Strategy:**
1. Check middle element
2. If it matches, return its index
3. If value < middle, search left half
4. If value > middle, search right half

**Example:** Search for 7 in `[1, 4, 7, 12, 21, 30, 34, 48, 50, 56]`
```
                                      ↑
Step 1: Middle is 21 (index 4)
        7 < 21, search left: [1, 4, 7, 12]
                                  ↑
Step 2: Middle is 4 (index 1)
        7 > 4, search right: [7, 12]
                              ↑
Step 3: Middle is 7 (index 0)
        7 == 7, found at index 2!
```

**Implementation:**
```cpp
size_t binary_search(double array[], size_t capacity, double value) {
    // BASE CASE: Small array
    if (capacity <= 2) {
        for (size_t k = 0; k < capacity; k++) {
            if (array[k] == value) return k;
        }
        return capacity;  // Not found
    }
    
    size_t middle_index = capacity / 2;
    
    // Check middle element
    if (array[middle_index] == value) {
        return middle_index;
    }
    // Search left half
    else if (value < array[middle_index]) {
        size_t result = binary_search(array, middle_index, value);
        if (result == middle_index) return capacity;
        return result;
    }
    // Search right half
    else {
        return binary_search(array + middle_index + 1,
                           capacity - middle_index - 1,
                           value) + middle_index + 1;
    }
}
```

**Time Complexity:** O(log n) - very fast!  
For 1 million elements, needs at most 20 comparisons.

---

## Subset Generation

**Goal:** Print all subsets of {1, 2, 3, ..., n}

**Strategy (Decision Tree):**
- For each element, make 2 choices: include it or exclude it
- This creates a binary tree of decisions

**Example for {1, 2, 3}:**
```
                        {}
                    /        \
              include 1      exclude 1
                {1}             {}
              /    \          /    \
            {1,2}  {1}      {2}    {}
           /   \   /  \    /  \   /  \
       {1,2,3}{1,2}{1,3}{1}{2,3}{2}{3}{}
```

**Output:**
```
(empty set)
1
2
3
1 2
1 3
2 3
1 2 3
```

This generates 2^n subsets (8 for n=3).

**Implementation:**
```cpp
void print_subsets_helper(bool membership_array[], size_t capacity,
                          size_t current_index) {
    // BASE CASE: Decided for all elements
    if (current_index == capacity) {
        // Print this subset
        for (size_t k = 0; k < capacity; k++) {
            if (membership_array[k]) {
                cout << (k + 1) << " ";
            }
        }
        cout << endl;
        return;
    }
    
    // RECURSIVE CASE 1: Include current element
    membership_array[current_index] = true;
    print_subsets_helper(membership_array, capacity, current_index + 1);
    
    // RECURSIVE CASE 2: Exclude current element
    membership_array[current_index] = false;
    print_subsets_helper(membership_array, capacity, current_index + 1);
}

void print_subsets(unsigned int n) {
    bool* membership_array = new bool[n]();
    print_subsets_helper(membership_array, n, 0);
    delete[] membership_array;
}
```

**Time Complexity:** O(2^n)  
**Space Complexity:** O(n)

---

## Jumper Configuration Problem

**Problem:** Print all valid configurations of jumper pins

A jumper configuration string contains:
- `'0'` = open (not connected)
- `'1'` = closed (connected)
- `'?'` = user configurable (can be 0 or 1)

**Example:** `"1?0?1"` has 4 possible configurations:
```
10001
10011
11001
11011
```

**Implementation:**
```cpp
void print_configurations(char pins[]) {
    // Find first '?' character
    size_t len = strlen(pins);
    size_t question_pos = len;
    
    for (size_t i = 0; i < len; i++) {
        if (pins[i] == '?') {
            question_pos = i;
            break;
        }
    }
    
    // BASE CASE: No more '?' characters
    if (question_pos == len) {
        cout << pins << endl;
        return;
    }
    
    // RECURSIVE CASE 1: Replace '?' with '0'
    pins[question_pos] = '0';
    print_configurations(pins);
    
    // RECURSIVE CASE 2: Replace '?' with '1'
    pins[question_pos] = '1';
    print_configurations(pins);
    
    // Restore '?' for backtracking
    pins[question_pos] = '?';
}
```

---

## Complete Source Code

```cpp
/*
 * COMPREHENSIVE RECURSION EXAMPLES
 * ECE 150 - Fundamentals of Programming
 * 
 * This file contains implementations of various recursive algorithms
 * with detailed explanations and examples.
 */

#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

// ============================================================================
// SECTION 1: RECURSIVE MATHEMATICAL FUNCTIONS
// ============================================================================

unsigned int factorial(unsigned int n) {
    if (n <= 1) {
        return 1;
    }
    return n * factorial(n - 1);
}

unsigned int binomial(unsigned int n, unsigned int k) {
    if (k > n) {
        return 0;
    }
    if ((k == 0) || (k == n)) {
        return 1;
    }
    return binomial(n - 1, k) + binomial(n - 1, k - 1);
}

unsigned int fibonacci_recursive(unsigned int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;
    return fibonacci_recursive(n - 1) + fibonacci_recursive(n - 2);
}

unsigned int fibonacci_iterative(unsigned int n) {
    unsigned int values[2]{0, 1};
    for (unsigned int k{2}; k <= n; ++k) {
        values[k % 2] = values[0] + values[1];
    }
    return values[n % 2];
}

double power_basic(double x, int n) {
    if (n == 0) return 1.0;
    if (n < 0) return 1.0 / power_basic(x, -n);
    return x * power_basic(x, n - 1);
}

double power_optimized(double x, int n) {
    if (n == 0) return 1.0;
    if (n < 0) return 1.0 / power_optimized(x, -n);
    
    double half_power = power_optimized(x, n / 2);
    
    if ((n % 2) == 0) {
        return half_power * half_power;
    } else {
        return x * half_power * half_power;
    }
}

// ============================================================================
// SECTION 2: HELPER FUNCTIONS FOR SORTING
// ============================================================================

size_t find_max(double array[], size_t capacity) {
    if (capacity == 0) return 0;
    size_t max_index = 0;
    for (size_t i = 1; i < capacity; i++) {
        if (array[i] > array[max_index]) {
            max_index = i;
        }
    }
    return max_index;
}

void insert(double array[], size_t capacity) {
    if (capacity <= 1) return;
    double last = array[capacity - 1];
    size_t i = capacity - 2;
    while (i < capacity && array[i] > last) {
        array[i + 1] = array[i];
        i--;
    }
    array[i + 1] = last;
}

bool is_sorted(double array[], size_t capacity) {
    for (size_t i = 1; i < capacity; i++) {
        if (array[i] < array[i - 1]) return false;
    }
    return true;
}

void print_array(double array[], size_t capacity) {
    cout << "[";
    for (size_t i = 0; i < capacity; i++) {
        cout << array[i];
        if (i < capacity - 1) cout << ", ";
    }
    cout << "]" << endl;
}

// ============================================================================
// SECTION 3: RECURSIVE SORTING ALGORITHMS
// ============================================================================

void selection_sort(double array[], size_t capacity) {
    if (capacity <= 1) {
        return;
    }
    size_t max_index = find_max(array, capacity);
    if (array[max_index] > array[capacity - 1]) {
        swap(array[max_index], array[capacity - 1]);
    }
    selection_sort(array, capacity - 1);
}

void insertion_sort(double array[], size_t capacity) {
    if (capacity <= 1) {
        return;
    }
    insertion_sort(array, capacity - 1);
    insert(array, capacity);
}

void merge(double array[], size_t cap_1, size_t cap_2) {
    double* tmp_array = new double[cap_1 + cap_2];
    size_t k1 = 0, k2 = cap_1, j = 0;
    
    while ((k1 < cap_1) && (k2 < cap_1 + cap_2)) {
        if (array[k1] <= array[k2]) {
            tmp_array[j] = array[k1];
            k1++;
        } else {
            tmp_array[j] = array[k2];
            k2++;
        }
        j++;
    }
    
    while (k1 < cap_1) {
        tmp_array[j] = array[k1];
        k1++; j++;
    }
    
    while (k2 < cap_1 + cap_2) {
        tmp_array[j] = array[k2];
        k2++; j++;
    }
    
    for (size_t k = 0; k < (cap_1 + cap_2); k++) {
        array[k] = tmp_array[k];
    }
    
    delete[] tmp_array;
}

void merge_sort(double array[], size_t capacity) {
    if (capacity <= 1) {
        return;
    }
    size_t capacity_1 = capacity / 2;
    size_t capacity_2 = capacity - capacity_1;
    merge_sort(array, capacity_1);
    merge_sort(array + capacity_1, capacity_2);
    merge(array, capacity_1, capacity_2);
}

// ============================================================================
// SECTION 4: BINARY NUMBER PRINTING
// ============================================================================

void print_binary(int const n) {
    if (n < 0) {
        cout << "-";
        print_binary(-n);
        return;
    }
    if ((n == 0) || (n == 1)) {
        cout << n;
        return;
    }
    print_binary(n / 2);
    cout << (n % 2);
}

// ============================================================================
// SECTION 5: BINARY SEARCH
// ============================================================================

size_t binary_search(double array[], size_t capacity, double value) {
    if (capacity <= 2) {
        for (size_t k = 0; k < capacity; k++) {
            if (array[k] == value) return k;
        }
        return capacity;
    }
    
    size_t middle_index = capacity / 2;
    
    if (array[middle_index] == value) {
        return middle_index;
    } else if (value < array[middle_index]) {
        size_t result = binary_search(array, middle_index, value);
        if (result == middle_index) return capacity;
        return result;
    } else {
        return binary_search(array + middle_index + 1,
                           capacity - middle_index - 1,
                           value) + middle_index + 1;
    }
}

// ============================================================================
// SECTION 6: SUBSET GENERATION
// ============================================================================

void print_subsets_helper(bool membership_array[], size_t capacity,
                          size_t current_index) {
    if (current_index == capacity) {
        for (size_t k = 0; k < capacity; k++) {
            if (membership_array[k]) {
                cout << (k + 1) << " ";
            }
        }
        cout << endl;
        return;
    }
    
    membership_array[current_index] = true;
    print_subsets_helper(membership_array, capacity, current_index + 1);
    
    membership_array[current_index] = false;
    print_subsets_helper(membership_array, capacity, current_index + 1);
}

void print_subsets(unsigned int n) {
    bool* membership_array = new bool[n]();
    print_subsets_helper(membership_array, n, 0);
    delete[] membership_array;
}

void print_configurations(char pins[]) {
    size_t len = strlen(pins);
    size_t question_pos = len;
    
    for (size_t i = 0; i < len; i++) {
        if (pins[i] == '?') {
            question_pos = i;
            break;
        }
    }
    
    if (question_pos == len) {
        cout << pins << endl;
        return;
    }
    
    pins[question_pos] = '0';
    print_configurations(pins);
    
    pins[question_pos] = '1';
    print_configurations(pins);
    
    pins[question_pos] = '?';
}

// ============================================================================
// SECTION 7: TESTING FUNCTIONS
// ============================================================================

void test_factorial() {
    cout << "\n========== TESTING FACTORIAL ==========\n";
    cout << "5! = " << factorial(5) << " (expected: 120)" << endl;
}

void test_fibonacci() {
    cout << "\n========== TESTING FIBONACCI ==========\n";
    cout << "First 10 Fibonacci numbers:" << endl;
    for (int i = 0; i <= 9; i++) {
        cout << "F(" << i << ") = " << fibonacci_iterative(i) << endl;
    }
}

void test_sorting() {
    cout << "\n========== TESTING SORTING ==========\n";
    double arr[] = {3.5, 1.2, 4.8, 2.1, 5.3};
    size_t size = 5;
    
    cout << "Original: ";
    print_array(arr, size);
    
    merge_sort(arr, size);
    cout << "Sorted:   ";
    print_array(arr, size);
}

void test_binary_print() {
    cout << "\n========== TESTING BINARY PRINTING ==========\n";
    for (int i = 0; i <= 10; i++) {
        cout << i << " = ";
        print_binary(i);
        cout << endl;
    }
}

void test_subsets() {
    cout << "\n========== TESTING SUBSETS ==========\n";
    cout << "All subsets of {1, 2, 3}:" << endl;
    print_subsets(3);
}

// ============================================================================
// MAIN FUNCTION
// ============================================================================

int main() {
    cout << "====================================================\n";
    cout << "   COMPREHENSIVE RECURSION DEMONSTRATIONS\n";
    cout << "====================================================\n";
    
    while (true) {
        cout << "\n\nSelect a demonstration:\n";
        cout << "1. Factorial\n";
        cout << "2. Fibonacci\n";
        cout << "3. Sorting\n";
        cout << "4. Binary Printing\n";
        cout << "5. Subsets\n";
        cout << "0. Run ALL tests\n";
        cout << "Q. Quit\n";
        cout << "\nEnter choice: ";
        
        char choice;
        cin >> choice;
        
        switch (choice) {
            case '1': test_factorial(); break;
            case '2': test_fibonacci(); break;
            case '3': test_sorting(); break;
            case '4': test_binary_print(); break;
            case '5': test_subsets(); break;
            case '0':
                test_factorial();
                test_fibonacci();
                test_sorting();
                test_binary_print();
                test_subsets();
                break;
            case 'Q':
            case 'q':
                cout << "\nThank you!\n";
                return 0;
            default:
                cout << "\nInvalid choice.\n";
        }
    }
    
    return 0;
}
```

---

## How to Compile and Run

### Compilation
```bash
g++ recursion_comprehensive.cpp -o recursion
```

### Execution
```bash
./recursion
```

### Features
- ✅ Interactive menu to run individual demonstrations
- ✅ Option to run all tests at once
- ✅ Detailed comments explaining each algorithm
- ✅ Examples showing time complexity differences

### Key Learning Points
1. **Understanding base cases and recursive cases** - Every recursive function needs both
2. **Recognizing efficiency** - Naive Fibonacci is 7000x slower than iterative!
3. **Divide and conquer** - Power function optimization reduces O(n) to O(log n)
4. **When to use recursion** - Some problems (merge sort, tree traversal) are naturally recursive
5. **Space complexity** - Recursion uses stack space (can cause stack overflow)

---

## Summary of Time Complexities

| Algorithm | Time Complexity | Notes |
|-----------|----------------|-------|
| Factorial | O(n) | Linear recursive calls |
| Binomial | O(2^n) | Exponential - very slow |
| Fibonacci (Recursive) | O(2^n) | Extremely inefficient |
| Fibonacci (Iterative) | O(n) | Much better! |
| Power (Basic) | O(n) | Linear recursive calls |
| Power (Optimized) | O(log n) | Divide and conquer |
| Selection Sort | O(n²) | Quadratic |
| Insertion Sort | O(n²) | Quadratic |
| Merge Sort | O(n log n) | Optimal for comparison-based sorting |
| Binary Search | O(log n) | Very efficient |
| Subset Generation | O(2^n) | Must generate all 2^n subsets |

---

## Additional Resources

- **University of Waterloo ECE 150** Course Materials
- **CLRS Introduction to Algorithms** - For deeper algorithm analysis
- **The Recursive Book of Recursion** by Al Sweigart - Fun introduction

---

**Created for ECE 150 - Fundamentals of Programming**  
**University of Waterloo**

---
