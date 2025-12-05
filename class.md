# Comprehensive Guide to C++ Classes and Object-Oriented Programming
## ECE 150 - Detailed Explanations

---

## Table of Contents
1. [Class Data Structures](#1-class-data-structures)
2. [Namespaces and std::cout](#2-namespaces-and-stdcout)
3. [Functions on Objects](#3-functions-on-objects)
4. [Operator Overloading](#4-operator-overloading)
5. [Issues with Member Variables](#5-issues-with-member-variables)
6. [Using Member Functions](#6-using-member-functions)
7. [Throwing and Catching Exceptions](#7-throwing-and-catching-exceptions)
8. [Classes, Encapsulation and Constructors](#8-classes-encapsulation-and-constructors)
9. [The Need for Encapsulation](#9-the-need-for-encapsulation)
10. [Copies, Assignment and the Destructor](#10-copies-assignment-and-the-destructor)
11. [Classes, Arrays and Dynamic Allocation](#11-classes-arrays-and-dynamic-allocation)

---

## 1. Class Data Structures

### **Motivation: The Three-Body Problem**

Imagine simulating the motion of stars in space. Each star has multiple properties:
- **Position**: x, y, z coordinates (in kilometers)
- **Velocity**: vx, vy, vz components (in km/h)
- **Mass**: m (in kilograms)

**The Problem Without Classes:**
```cpp
// For Rigel Kentaurus (one star)
double rigil_kentaurus_position[3];  // km
double rigil_kentaurus_velocity[3];  // km/h
double rigil_kentaurus_mass;         // kg

// For Toliman (another star)
double toliman_position[3];
double toliman_velocity[3];
double toliman_mass;

// For Proxima Centauri (third star)
double proxima_centauri_position[3];
double proxima_centauri_velocity[3];
double proxima_centauri_mass;
```

This is messy! We have 9 separate variables for just 3 stars, and they're not clearly grouped together.

### **Solution: Classes**

A **class** is a user-defined data type that bundles related data together:

```cpp
// Class declaration (forward declaration)
class Body;

// Class definition
class Body {
public:
    // Member variables
    double position_[3];  // km
    double velocity_[3];  // km/h
    double mass_;         // kg
};
```

**Key Points:**
- `class Body` creates a new type called `Body`
- `public:` means these member variables can be accessed from outside the class
- Member variables are suffixed with `_` (naming convention, not required)
- Class names are typically capitalized (e.g., `Body`, not `body`)

### **Creating Objects (Instances)**

An **object** is an instance of a class:

```cpp
int main() {
    // Create an Earth object
    Body earth{ {1.496e8, 0.0, 0.0},    // position
                {0.0, 107e3, 0.0},       // velocity
                5.972e24 };              // mass
    
    return 0;
}
```

**Memory Layout of `earth` object:**
```
earth:
  position_[0] = 1.496e8
  position_[1] = 0.0
  position_[2] = 0.0
  velocity_[0] = 0.0
  velocity_[1] = 107e3
  velocity_[2] = 0.0
  mass_        = 5.972e24
```

### **Accessing Member Variables**

Use the **dot operator** (`.`) to access member variables:

```cpp
int main() {
    Body earth{ {1.496e8, 0.0, 0.0}, {0.0, 107e3, 0.0}, 5.972e24 };
    
    // Access member variables
    std::cout << earth.mass_ << std::endl;           // 5.972e+24
    std::cout << earth.position_[0] << std::endl;    // 1.496e+08
    std::cout << earth.velocity_[1] << std::endl;    // 107000
    
    // Modify member variables
    earth.mass_ = 6.0e24;
    earth.position_[0] = 1.5e8;
    
    return 0;
}
```

### **Passing Objects to Functions**

**Pass by Value** (copies the entire object - SLOW for large objects):
```cpp
void print_mass(Body b) {  // b is a COPY
    std::cout << b.mass_ << std::endl;
}
```

**Pass by Reference** (no copy - FAST):
```cpp
void print_mass(Body &b) {  // b is a reference to the original
    std::cout << b.mass_ << std::endl;
}
```

**Pass by Const Reference** (no copy, prevents modification - BEST for read-only):
```cpp
void print_mass(Body const &b) {  // Cannot modify b
    std::cout << b.mass_ << std::endl;
    // b.mass_ = 10.0;  // ERROR: cannot modify const reference
}
```

**Why Pass by Reference for Objects?**
- Objects can be LARGE (many member variables)
- Copying is expensive (time and memory)
- Pass by reference avoids copying
- Use `const` when you don't need to modify

---

## 2. Namespaces and std::cout

### **What is a Namespace?**

A **namespace** is a named scope that groups related identifiers (variables, functions, classes) to avoid naming conflicts.

**The Problem Without Namespaces:**
```cpp
// Math library defines sin()
double sin(double x);

// Your custom sine function
double sin(double x);  // ERROR: redefinition!
```

### **Creating and Using Namespaces**

```cpp
// Standard library namespace
namespace std {
    double sin(double x);
    class string { /* ... */ };
    // ... many other things
}

// Your custom namespace
namespace ca_uwaterloo_dwharder {
    double sin(double x) {
        // Your custom implementation
        if (x < 0) {
            return -sin(-x);
        } else if (x <= M_PI_2) {
            // Approximation formula
            return (((1.0 - 4.0*M_1_PI)*M_1_PI*x + (3.0*M_1_PI - 1.0))
                    *4.0*M_1_PI*x + 1.0)*x;
        } else if (x <= M_PI) {
            return sin(M_PI_2 - x);
        } else if (x <= 2.0*M_PI) {
            return -sin(x - M_PI);
        } else {
            return sin(x - 2.0*M_PI);
        }
    }
}

int main() {
    // Use standard sin
    double a = std::sin(1.0);
    
    // Use your custom sin
    double b = ca_uwaterloo_dwharder::sin(1.0);
    
    return 0;
}
```

### **The std Namespace**

The C++ Standard Library puts everything in the `std` namespace:

```cpp
std::size_t        // Type
std::string        // Class
std::sin           // Function
std::cout          // Object
std::endl          // Manipulator
```

### **std::cout and std::ostream**

`std::cout` is an **object** of class `std::ostream`:

```cpp
namespace std {
    class ostream {
    public:
        // Member functions
        ostream& operator<<(int value);
        ostream& operator<<(double value);
        ostream& operator<<(const char* str);
        // ... many more overloads
    };
    
    // Global object
    ostream cout;  // Connected to standard output
}
```

**Using std::cout:**
```cpp
#include <iostream>

int main() {
    std::cout << "Hello";      // Calls operator<<(const char*)
    std::cout << 42;           // Calls operator<<(int)
    std::cout << 3.14;         // Calls operator<<(double)
    std::cout << std::endl;    // Outputs newline and flushes buffer
    
    return 0;
}
```

**Chaining with operator<<:**
```cpp
std::cout << "Value: " << 42 << std::endl;
// Equivalent to:
((std::cout << "Value: ") << 42) << std::endl;
```

Each `<<` returns a reference to `std::cout`, allowing chaining.

### **std::endl Implementation**

`std::endl` is a **manipulator** - a special function:

```cpp
namespace std {
    ostream& endl(ostream& os) {
        os << '\n';     // Insert newline
        os.flush();     // Flush the output buffer
        return os;
    }
}
```

**Why use std::endl?**
- Inserts newline character
- **Flushes** the buffer (forces output immediately)
- Useful when you need immediate output (e.g., before a crash)

**When to use '\n' instead:**
- When performance matters (no flush overhead)
- For most regular output

```cpp
std::cout << "Fast\n";          // Just newline
std::cout << "Slow" << std::endl;  // Newline + flush
```

---

## 3. Functions on Objects

### **Two Types of Functions on Objects**

1. **Query (Accessor) Functions**: Read member variables, don't modify
2. **Modify (Mutator) Functions**: Change member variables

### **Example: Vector_3d Class**

```cpp
class Vector_3d {
public:
    double x_;
    double y_;
    double z_;
};
```

### **Query Functions**

**Inner Product (Dot Product):**
```cpp
double inner_product(Vector_3d const &u, Vector_3d const &v) {
    return u.x_*v.x_ + u.y_*v.y_ + u.z_*v.z_;
}

// Usage:
Vector_3d a{1.0, 2.0, 3.0};
Vector_3d b{4.0, 5.0, 6.0};
double dot = inner_product(a, b);  // 1*4 + 2*5 + 3*6 = 32
```

**Norm (Magnitude):**
```cpp
double norm(Vector_3d const &u) {
    return std::sqrt(u.x_*u.x_ + u.y_*u.y_ + u.z_*u.z_);
}

// Usage:
Vector_3d v{3.0, 4.0, 0.0};
double length = norm(v);  // sqrt(9 + 16) = 5.0
```

**Why const reference?**
- `const`: Promises not to modify the object
- `&`: Avoids copying (efficient)
- Compiler enforces you don't accidentally modify

### **Modify Functions (Return New Objects)**

**Vector Addition:**
```cpp
Vector_3d add(Vector_3d const &u, Vector_3d const &v) {
    Vector_3d result;
    result.x_ = u.x_ + v.x_;
    result.y_ = u.y_ + v.y_;
    result.z_ = u.z_ + v.z_;
    return result;
}

// Usage:
Vector_3d a{1.0, 2.0, 3.0};
Vector_3d b{4.0, 5.0, 6.0};
Vector_3d c = add(a, b);  // c = {5.0, 7.0, 9.0}
```

**Scalar Multiplication:**
```cpp
Vector_3d mul(double a, Vector_3d const &u) {
    Vector_3d result;
    result.x_ = a * u.x_;
    result.y_ = a * u.y_;
    result.z_ = a * u.z_;
    return result;
}

// Also allow u * a (commutative)
Vector_3d mul(Vector_3d const &u, double a) {
    return mul(a, u);  // Reuse the other function
}

// Usage:
Vector_3d v{1.0, 2.0, 3.0};
Vector_3d w1 = mul(5.0, v);  // {5.0, 10.0, 15.0}
Vector_3d w2 = mul(v, 5.0);  // {5.0, 10.0, 15.0}
```

**Cross Product:**
```cpp
Vector_3d cross(Vector_3d const &u, Vector_3d const &v) {
    Vector_3d result;
    result.x_ = u.y_*v.z_ - u.z_*v.y_;
    result.y_ = u.z_*v.x_ - u.x_*v.z_;
    result.z_ = u.x_*v.y_ - u.y_*v.x_;
    return result;
}
```

**Projection:**
```cpp
Vector_3d project(Vector_3d const &u, Vector_3d const &v) {
    // Project u onto v
    double scale = inner_product(u, v) / inner_product(v, v);
    return mul(scale, v);
}
```

### **Example: Rational Number Class**

```cpp
class Rational {
public:
    int numer_;  // numerator
    int denom_;  // denominator
};
```

**Query Functions:**
```cpp
int numerator(Rational const &r) {
    return r.numer_;
}

int denominator(Rational const &r) {
    return r.denom_;
}

std::string to_string(Rational const &r) {
    return std::to_string(r.numer_) + "/" + std::to_string(r.denom_);
}
```

**Arithmetic Functions:**
```cpp
Rational add(Rational const &p, Rational const &q) {
    Rational result;
    result.numer_ = p.numer_*q.denom_ + q.numer_*p.denom_;
    result.denom_ = p.denom_ * q.denom_;
    return result;
}

Rational multiply(Rational const &p, Rational const &q) {
    Rational result;
    result.numer_ = p.numer_ * q.numer_;
    result.denom_ = p.denom_ * q.denom_;
    return result;
}

Rational negate(Rational const &p) {
    Rational result;
    result.numer_ = -p.numer_;
    result.denom_ = p.denom_;
    return result;
}
```

**Usage:**
```cpp
Rational p{3, 4};   // 3/4
Rational q{1, 2};   // 1/2
Rational sum = add(p, q);  // 3/4 + 1/2 = 10/8
```

---

## 4. Operator Overloading

### **The Problem: Awkward Function Calls**

Compare using integers vs. rational numbers:

```cpp
int main() {
    int m = 15;
    int n = 17;
    Rational p{15, 1};
    Rational q{17, 1};
    
    // Natural for integers
    std::cout << "m + n = " << (m + n) << std::endl;
    
    // Awkward for Rational
    std::cout << "p + q = " << to_string(add(p, q)) << std::endl;
    
    // Natural for integers
    std::cout << "-m = " << (-m) << std::endl;
    
    // Awkward for Rational
    std::cout << "-p = " << to_string(negate(p)) << std::endl;
}
```

Wouldn't it be nice to write `p + q` instead of `add(p, q)`?

### **Solution: Operator Overloading**

**Operator overloading** lets you define how operators work with your classes:

```cpp
// Instead of:
Rational add(Rational const &p, Rational const &q);

// Write:
Rational operator+(Rational const &p, Rational const &q) {
    Rational result;
    result.numer_ = p.numer_*q.denom_ + q.numer_*p.denom_;
    result.denom_ = p.denom_ * q.denom_;
    return result;
}
```

Now you can write: `Rational sum = p + q;`

### **Overloading Arithmetic Operators**

```cpp
// Addition
Rational operator+(Rational const &p, Rational const &q) {
    return Rational{
        p.numer_*q.denom_ + q.numer_*p.denom_,
        p.denom_*q.denom_
    };
}

// Subtraction
Rational operator-(Rational const &p, Rational const &q) {
    return Rational{
        p.numer_*q.denom_ - q.numer_*p.denom_,
        p.denom_*q.denom_
    };
}

// Multiplication
Rational operator*(Rational const &p, Rational const &q) {
    return Rational{
        p.numer_ * q.numer_,
        p.denom_ * q.denom_
    };
}

// Division
Rational operator/(Rational const &p, Rational const &q) {
    return Rational{
        p.numer_ * q.denom_,
        p.denom_ * q.numer_
    };
}

// Unary negation
Rational operator-(Rational const &p) {
    return Rational{-p.numer_, p.denom_};
}
```

**Usage:**
```cpp
Rational a{1, 2};  // 1/2
Rational b{3, 4};  // 3/4

Rational sum = a + b;      // 10/8
Rational diff = a - b;     // -2/8
Rational prod = a * b;     // 3/8
Rational quot = a / b;     // 4/6
Rational neg = -a;         // -1/2
```

### **Overloading Comparison Operators**

```cpp
bool operator==(Rational const &p, Rational const &q) {
    return p.numer_*q.denom_ == q.numer_*p.denom_;
}

bool operator!=(Rational const &p, Rational const &q) {
    return !(p == q);  // Reuse operator==
}

bool operator<(Rational const &p, Rational const &q) {
    return p.numer_*q.denom_ < q.numer_*p.denom_;
}

bool operator>(Rational const &p, Rational const &q) {
    return q < p;  // Reuse operator<
}

bool operator<=(Rational const &p, Rational const &q) {
    return !(q < p);
}

bool operator>=(Rational const &p, Rational const &q) {
    return !(p < q);
}
```

### **Overloading Stream Operators**

**Output operator <<:**
```cpp
std::ostream& operator<<(std::ostream &out, Rational const &p) {
    out << p.numer_ << "/" << p.denom_;
    return out;  // Return for chaining
}

// Usage:
Rational r{3, 4};
std::cout << "The rational is: " << r << std::endl;
// Output: The rational is: 3/4
```

**Input operator >>:**
```cpp
std::istream& operator>>(std::istream &in, Rational &p) {
    in >> p.numer_;
    in.ignore();  // Skip the '/'
    in >> p.denom_;
    return in;
}

// Usage:
Rational r;
std::cout << "Enter a rational (e.g., 3/4): ";
std::cin >> r;
```

### **Vector Operators**

```cpp
// Vector addition
Vector_3d operator+(Vector_3d const &u, Vector_3d const &v) {
    return Vector_3d{u.x_ + v.x_, u.y_ + v.y_, u.z_ + v.z_};
}

// Scalar multiplication (both orders)
Vector_3d operator*(double a, Vector_3d const &u) {
    return Vector_3d{a*u.x_, a*u.y_, a*u.z_};
}

Vector_3d operator*(Vector_3d const &u, double a) {
    return a * u;  // Reuse
}

// Usage:
Vector_3d v1{1, 2, 3};
Vector_3d v2{4, 5, 6};
Vector_3d sum = v1 + v2;        // {5, 7, 9}
Vector_3d scaled = 2.0 * v1;    // {2, 4, 6}
Vector_3d scaled2 = v1 * 2.0;   // {2, 4, 6}
```

### **Key Points About Operator Overloading**

1. **Makes code more readable** - `p + q` vs. `add(p, q)`
2. **Consistent with built-in types** - Your classes behave like `int`, `double`
3. **Operator precedence maintained** - `a + b * c` still does multiplication first
4. **Can't create new operators** - Can't make `***` operator
5. **Some operators can't be overloaded** - `.`, `::`, `?:`, `sizeof`

---

## 5. Issues with Member Variables

### **Problem 1: Uninitialized Objects**

```cpp
class Rational {
public:
    int numer_;
    int denom_;
};

int main() {
    Rational p{};  // What are numer_ and denom_?
    // Could be 0/0 - NOT a valid rational number!
    
    Rational q = p + p;  // Undefined behavior
}
```

### **Problem 2: Invalid States**

User can create invalid objects:

```cpp
Rational p{1, 2};  // Valid: 1/2
p.denom_ = 0;      // Now INVALID: 1/0 (division by zero!)

Rational q = p + p;  // Disaster!
```

### **Problem 3: No Guarantee of Normalization**

These all represent -1/2, but aren't equal:

```cpp
Rational p1{-1, 2};     // -1/2
Rational p2{100, -200}; // 100/-200 = -1/2
Rational p3{-47, 94};   // -47/94 = -1/2
Rational p4{99, -198};  // 99/-198 = -1/2

// But:
if (p1 == p2) {  // Might return FALSE because numerators/denominators differ
    // ...
}
```

### **Problem 4: Direct Access Allows Corruption**

```cpp
void dangerous_function(Rational &r) {
    r.numer_ = 42;
    r.denom_ = 0;  // Oops! Now r is invalid
}

Rational p{1, 2};
dangerous_function(p);  // p is now corrupted
```

### **The Solution: Encapsulation**

**Encapsulation** means:
1. **Hide** member variables (make them private)
2. **Control access** through member functions
3. **Maintain invariants** (rules that must always be true)

**Three Critical Phases in Object Lifetime:**
1. **Construction**: Initialize object correctly when created
2. **Manipulation**: Provide controlled ways to use/modify object
3. **Destruction**: Clean up when object is destroyed

```cpp
class Rational {
private:    // Users CANNOT access these directly
    int numer_;
    int denom_;
    
public:     // Users CAN use these functions
    // Constructor: ensures valid initialization
    Rational(int n, int d);
    
    // Accessors: read-only access
    int numerator() const;
    int denominator() const;
    
    // Operations: maintain validity
    Rational operator+(Rational const &other) const;
    
    // Destructor: clean up
    ~Rational();
};
```

With encapsulation:
- Can't create `Rational p{1, 0}` - constructor checks
- Can't do `p.denom_ = 0` - denom_ is private
- All operations maintain invariant: `denom_ != 0`

---

## 6. Using Member Functions

### **What are Member Functions?**

**Member functions** are functions that belong to a class and can access its private members.

### **Calling Member Functions**

Use the **dot operator** (`.`) with the object:

```cpp
object.member_function(arguments);
```

### **Example: std::cout Member Functions**

```cpp
#include <iostream>
#include <cmath>

int main() {
    std::cout << M_PI << std::endl;
    // Output: 3.14159 (default precision)
    
    std::cout.precision(16);  // Set precision to 16 digits
    std::cout << M_PI << std::endl;
    // Output: 3.141592653589793
    
    std::cout.width(10);      // Set field width to 10
    std::cout << 42 << std::endl;
    // Output: "        42" (padded with spaces)
    
    std::cout << 42 << std::endl;
    // Output: "42" (width only applies once)
    
    std::cout.fill('?');      // Change padding character
    std::cout.width(10);
    std::cout << 42 << std::endl;
    // Output: "????????42"
    
    return 0;
}
```

**Member functions of std::ostream:**
- `precision(n)`: Set number of significant digits
- `width(n)`: Set field width for next output
- `fill(c)`: Set padding character
- `flush()`: Force output immediately

### **Example: std::string Member Functions**

```cpp
#include <string>
#include <iostream>

int main() {
    std::string str = "Hello, World!";
    
    // Length
    std::cout << str.length() << std::endl;     // 13
    std::cout << str.size() << std::endl;       // 13 (same as length)
    
    // Access characters
    std::cout << str[0] << std::endl;           // 'H'
    std::cout << str.at(7) << std::endl;        // 'W'
    
    // Substring
    std::string sub = str.substr(0, 5);         // "Hello"
    std::cout << sub << std::endl;
    
    // Find
    size_t pos = str.find("World");             // 7
    std::cout << pos << std::endl;
    
    // Replace
    str.replace(7, 5, "C++");                   // "Hello, C++!"
    std::cout << str << std::endl;
    
    // Append
    str.append(" Rocks");                       // "Hello, C++! Rocks"
    std::cout << str << std::endl;
    
    // Insert
    str.insert(5, " there");                    // "Hello there, C++! Rocks"
    std::cout << str << std::endl;
    
    // Erase
    str.erase(5, 6);                            // "Hello, C++! Rocks"
    std::cout << str << std::endl;
    
    // Clear
    str.clear();                                 // ""
    std::cout << "Empty: " << str.empty() << std::endl;  // 1 (true)
    
    return 0;
}
```

### **Example: std::set Member Functions**

A `std::set` is a container that stores **unique** elements in **sorted** order:

```cpp
#include <set>
#include <iostream>

int main() {
    std::set<int> numbers;
    
    // Insert elements
    numbers.insert(5);
    numbers.insert(2);
    numbers.insert(8);
    numbers.insert(2);  // Duplicate - won't be added
    
    // Size
    std::cout << "Size: " << numbers.size() << std::endl;  // 3
    
    // Check if empty
    std::cout << "Empty: " << numbers.empty() << std::endl;  // 0 (false)
    
    // Find element
    if (numbers.find(5) != numbers.end()) {
        std::cout << "Found 5" << std::endl;
    }
    
    // Count (0 or 1 for set)
    std::cout << "Count of 2: " << numbers.count(2) << std::endl;  // 1
    std::cout << "Count of 7: " << numbers.count(7) << std::endl;  // 0
    
    // Erase element
    numbers.erase(2);
    std::cout << "Size after erase: " << numbers.size() << std::endl;  // 2
    
    // Iterate (elements are sorted!)
    for (int num : numbers) {
        std::cout << num << " ";  // 5 8
    }
    std::cout << std::endl;
    
    // Clear all
    numbers.clear();
    std::cout << "Size: " << numbers.size() << std::endl;  // 0
    
    return 0;
}
```

### **Key Points**

1. **You don't need to know implementation** - Just the interface
2. **Encapsulation in action** - Internal details are hidden
3. **Member functions maintain invariants** - Operations keep object valid
4. **Consistent interface** - Similar functions across different classes

---

## 7. Throwing and Catching Exceptions

### **Review: Assertions**

**Assertions** check conditions during development:

```cpp
#include <cassert>

void divide(int a, int b) {
    assert(b != 0);  // Program terminates if b == 0
    int result = a / b;
    std::cout << result << std::endl;
}

int main() {
    divide(10, 2);  // OK: 5
    divide(10, 0);  // CRASH: assertion failed
}
```

**Problems with Assertions:**
1. **Terminates immediately** - No chance to recover
2. **Disabled in release builds** - Can use `#define NDEBUG`
3. **Not user-friendly** - Shows cryptic error messages

```
assertion "b != 0" failed: file "divide.cpp", line 4
Aborted (core dumped)
```

### **Exceptions: A Better Solution**

**Exceptions** allow:
1. **Signaling errors** without immediate termination
2. **Separating** error detection from error handling
3. **Graceful recovery** or cleanup before termination

### **Standard Exception Classes**

Defined in `<stdexcept>`:

```cpp
std::exception              // Base class
├── std::logic_error       // Programming errors
│   ├── std::invalid_argument
│   ├── std::domain_error
│   ├── std::length_error
│   ├── std::out_of_range
│   └── std::future_error
└── std::runtime_error     // Runtime errors
    ├── std::range_error
    ├── std::overflow_error
    └── std::underflow_error
```

### **Throwing Exceptions**

Use `throw` to signal an error:

```cpp
#include <stdexcept>

void divide(int a, int b) {
    if (b == 0) {
        throw std::invalid_argument("Division by zero");
    }
    int result = a / b;
    std::cout << result << std::endl;
}

int main() {
    divide(10, 2);  // OK: 5
    divide(10, 0);  // Throws exception - program terminates
}
```

Without catching, this **terminates** with a better error message:
```
terminate called after throwing an instance of 'std::invalid_argument'
  what():  Division by zero
Aborted
```

### **Catching Exceptions**

Use `try-catch` to handle exceptions:

```cpp
#include <stdexcept>
#include <iostream>

void divide(int a, int b) {
    if (b == 0) {
        throw std::invalid_argument("Division by zero");
    }
    int result = a / b;
    std::cout << result << std::endl;
}

int main() {
    try {
        divide(10, 2);  // OK: 5
        divide(10, 0);  // Throws exception
        divide(8, 4);   // Never executed
    } catch (std::invalid_argument const &e) {
        std::cout << "Error: " << e.what() << std::endl;
        // Program continues here!
    }
    
    std::cout << "Program continues normally" << std::endl;
    return 0;
}
```

**Output:**
```
5
Error: Division by zero
Program continues normally
```

### **Multiple Catch Blocks**

```cpp
try {
    // Code that might throw
} catch (std::invalid_argument const &e) {
    std::cout << "Invalid argument: " << e.what() << std::endl;
} catch (std::out_of_range const &e) {
    std::cout << "Out of range: " << e.what() << std::endl;
} catch (std::exception const &e) {
    std::cout << "General exception: " << e.what() << std::endl;
} catch (...) {
    std::cout << "Unknown exception" << std::endl;
}
```

**Order matters!** Catch more specific exceptions first.

### **Exception in Constructors**

```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    Array(size_t size) {
        if (size == 0) {
            throw std::invalid_argument("Size cannot be zero");
        }
        data_ = new int[size];
        size_ = size;
    }
    
    ~Array() {
        delete[] data_;
    }
};

int main() {
    try {
        Array arr(0);  // Throws exception
    } catch (std::invalid_argument const &e) {
        std::cout << "Cannot create array: " << e.what() << std::endl;
    }
    return 0;
}
```

### **Exception Safety Guarantees**

1. **No-throw guarantee**: Never throws
2. **Strong guarantee**: Operation succeeds completely or has no effect
3. **Basic guarantee**: No resources leaked, object in valid state
4. **No guarantee**: Might leak resources or leave object invalid

### **Best Practices**

1. **Use exceptions for exceptional conditions** - Not normal flow control
2. **Catch by const reference** - Avoid copying, prevent slicing
3. **Provide meaningful messages** - Help debugging
4. **Clean up resources** - Use RAII (Resource Acquisition Is Initialization)
5. **Don't throw in destructors** - Can cause program termination

```cpp
// Good: Specific exception with message
throw std::out_of_range("Index " + std::to_string(i) + " out of bounds");

// Bad: Generic exception
throw std::exception();
```

---

## 8. Classes, Encapsulation and Constructors

### **The Three Critical Phases**

Every object has three critical phases:
1. **Construction**: How it's initialized
2. **Lifetime**: How it's used and manipulated
3. **Destruction**: How it's cleaned up

### **Private Member Variables**

Make member variables **private** to enforce encapsulation:

```cpp
class Rational {
private:    // Can only be accessed within the class
    int numer_;
    int denom_;
    
public:     // Can be accessed from anywhere
    // Member functions go here
};
```

Now users CANNOT do:
```cpp
Rational p;
p.numer_ = 5;   // ERROR: numer_ is private
p.denom_ = 0;   // ERROR: denom_ is private
```

### **Constructors**

A **constructor** is a special member function that initializes objects:

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // Constructor
    Rational(int n, int d) {
        if (d == 0) {
            throw std::invalid_argument("Denominator cannot be zero");
        }
        numer_ = n;
        denom_ = d;
        normalize();  // Simplify the fraction
    }
};
```

**Constructor Rules:**
1. **Same name as class**
2. **No return type** (not even `void`)
3. **Called automatically** when object is created
4. **Can be overloaded** (multiple constructors with different parameters)

### **Using Constructors**

```cpp
int main() {
    Rational p{3, 4};       // Calls Rational(3, 4)
    Rational q(5, 6);       // Also valid syntax
    Rational r = {7, 8};    // Also valid
    
    // Rational bad{1, 0};  // Throws exception!
    
    return 0;
}
```

### **Member Initializer List (Preferred)**

More efficient than assignment in constructor body:

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // With member initializer list
    Rational(int n, int d) : numer_{n}, denom_{d} {
        if (denom_ == 0) {
            throw std::invalid_argument("Denominator cannot be zero");
        }
        normalize();
    }
};
```

**Why use initializer lists?**
- **More efficient**: Direct initialization, not assignment
- **Required for const members**: Can't assign to const after creation
- **Required for reference members**: Can't rebind references
- **Required for members without default constructor**

### **Default Constructor**

Constructor with **no parameters** or **all parameters have defaults**:

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // Default constructor: represents 0/1
    Rational() : numer_{0}, denom_{1} {
    }
    
    // Constructor with parameters
    Rational(int n, int d) : numer_{n}, denom_{d} {
        if (denom_ == 0) {
            throw std::invalid_argument("Denominator cannot be zero");
        }
        normalize();
    }
};

int main() {
    Rational p;       // Calls Rational() - default constructor
    Rational q{3, 4}; // Calls Rational(int, int)
}
```

**Alternative: Default Parameters**

```cpp
class Rational {
public:
    Rational(int n = 0, int d = 1) : numer_{n}, denom_{d} {
        if (denom_ == 0) {
            throw std::invalid_argument("Denominator cannot be zero");
        }
        normalize();
    }
};

int main() {
    Rational p;       // n=0, d=1
    Rational q{3};    // n=3, d=1
    Rational r{3, 4}; // n=3, d=4
}
```

### **Normalizing Rational Numbers**

Keep fractions in **normal form**:
1. Denominator is positive
2. Numerator and denominator are coprime (GCD = 1)
3. Zero is represented as 0/1

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
    // Helper function: Greatest Common Divisor
    int gcd(int a, int b) {
        a = std::abs(a);
        b = std::abs(b);
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }
    
    // Helper function: Normalize the fraction
    void normalize() {
        if (denom_ < 0) {
            numer_ = -numer_;
            denom_ = -denom_;
        }
        
        int g = gcd(numer_, denom_);
        numer_ /= g;
        denom_ /= g;
    }
    
public:
    Rational(int n = 0, int d = 1) : numer_{n}, denom_{d} {
        if (denom_ == 0) {
            throw std::invalid_argument("Denominator cannot be zero");
        }
        normalize();
    }
};
```

**Examples:**
```cpp
Rational p{6, 8};      // Normalized to 3/4
Rational q{-3, -4};    // Normalized to 3/4
Rational r{10, -20};   // Normalized to -1/2
Rational s{0, 5};      // Normalized to 0/1
```

### **Accessor (Getter) Member Functions**

Provide **read-only access** to private members:

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // ... constructor ...
    
    // Accessor functions
    int numerator() const {
        return numer_;
    }
    
    int denominator() const {
        return denom_;
    }
};
```

**The `const` keyword:**
- Promises the function won't modify the object
- Allows calling on `const` objects
- Good practice for all accessor functions

```cpp
int main() {
    Rational p{3, 4};
    std::cout << p.numerator() << "/" << p.denominator() << std::endl;
    // Output: 3/4
    
    Rational const q{5, 6};
    std::cout << q.numerator();  // OK: numerator() is const
}
```

### **Mutator (Setter) Member Functions**

Provide **controlled modification** of private members:

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // ... constructor and accessors ...
    
    // Mutator functions
    void numerator(int n) {
        numer_ = n;
        normalize();  // Maintain invariant
    }
    
    void denominator(int d) {
        if (d == 0) {
            throw std::invalid_argument("Denominator cannot be zero");
        }
        denom_ = d;
        normalize();  // Maintain invariant
    }
};
```

**Usage:**
```cpp
Rational p{3, 4};
p.numerator(5);    // Now 5/4
p.denominator(8);  // Now 5/8 (still normalized)
```

### **Arithmetic as Member Functions**

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // ... constructor and accessors ...
    
    // Arithmetic member functions
    Rational add(Rational const &other) const {
        return Rational{
            numer_ * other.denom_ + other.numer_ * denom_,
            denom_ * other.denom_
        };
    }
    
    Rational multiply(Rational const &other) const {
        return Rational{
            numer_ * other.numer_,
            denom_ * other.denom_
        };
    }
    
    Rational negate() const {
        return Rational{-numer_, denom_};
    }
};

// Usage:
Rational p{1, 2};
Rational q{3, 4};
Rational sum = p.add(q);  // 10/8 = 5/4
```

### **Operator Overloading as Member Functions**

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // ... constructor ...
    
    // Operators as member functions
    Rational operator+(Rational const &other) const {
        return Rational{
            numer_ * other.denom_ + other.numer_ * denom_,
            denom_ * other.denom_
        };
    }
    
    Rational operator*(Rational const &other) const {
        return Rational{
            numer_ * other.numer_,
            denom_ * other.denom_
        };
    }
    
    Rational operator-() const {
        return Rational{-numer_, denom_};
    }
};

// Usage:
Rational p{1, 2};
Rational q{3, 4};
Rational sum = p + q;   // Much more natural!
Rational prod = p * q;
Rational neg = -p;
```

### **Friend Functions**

Sometimes non-member functions need access to private members:

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
public:
    // ... constructor and other functions ...
    
    // Declare friend function
    friend std::ostream& operator<<(std::ostream &out, Rational const &r);
};

// Friend function can access private members
std::ostream& operator<<(std::ostream &out, Rational const &r) {
    out << r.numer_ << "/" << r.denom_;
    return out;
}

// Usage:
Rational p{3, 4};
std::cout << p << std::endl;  // 3/4
```

### **Static Member Functions**

**Static member functions**:
- Belong to the class, not any object
- Can't access non-static members
- Called using class name, not object

```cpp
class Rational {
private:
    int numer_;
    int denom_;
    
    static int gcd(int a, int b) {
        a = std::abs(a);
        b = std::abs(b);
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }
    
public:
    Rational(int n = 0, int d = 1) : numer_{n}, denom_{d} {
        if (denom_ == 0) {
            throw std::invalid_argument("Denominator cannot be zero");
        }
        int g = gcd(numer_, denom_);  // Call static function
        numer_ /= g;
        denom_ /= g;
    }
};
```

---

## 9. The Need for Encapsulation

### **Why Encapsulation?**

Consider the `Vector_3d` class with public members:

```cpp
class Vector_3d {
public:
    double x;
    double y;
    double z;
};
```

**Seems simpler, right?** Why bother with private members and accessor functions?

### **Reason 1: Future Changes**

Suppose you want to optimize by using **spherical coordinates** instead:

```cpp
class Vector_3d {
private:
    double r_;      // radius
    double theta_;  // angle from z-axis
    double phi_;    // angle in xy-plane
    
public:
    // Convert to Cartesian on access
    double x() const {
        return r_ * std::sin(theta_) * std::cos(phi_);
    }
    
    double y() const {
        return r_ * std::sin(theta_) * std::sin(phi_);
    }
    
    double z() const {
        return r_ * std::cos(theta_);
    }
};
```

**With private members:**
- Change internal representation
- All existing code still works!
- Users call `v.x()` instead of `v.x`

**With public members:**
- Can't change representation
- Would break all user code
- Stuck with original design forever

### **Reason 2: Maintaining Invariants**

An **invariant** is a condition that must **always** be true.

**Example: Rational class invariant**
```
denom_ != 0  (denominator is never zero)
```

**With public members:**
```cpp
Rational p{1, 2};  // Valid
p.denom_ = 0;      // User breaks invariant!
// Now p is invalid, operations will crash
```

**With private members:**
```cpp
Rational p{1, 2};  // Valid
// p.denom_ = 0;   // ERROR: can't access private member

p.denominator(0);  // Throws exception instead of silently breaking
```

### **Reason 3: Performance Optimization**

**Compiler optimizations** work better with encapsulation:

**Inline functions:**
```cpp
class Vector_3d {
private:
    double x_, y_, z_;
    
public:
    double x() const { return x_; }  // Tiny function
};
```

Compiler can **inline** this - replaces function call with direct access:
```cpp
Vector_3d v{1, 2, 3};
double val = v.x();

// Compiler generates same code as:
double val = v.x_;  // No function call overhead!
```

**Result:** Zero performance penalty for encapsulation!

### **Reason 4: Clear Interface**

**Interface**: The public member functions users can call

**Good interface:**
```cpp
class Array {
private:
    int* data_;
    size_t size_;
    size_t capacity_;
    
public:
    Array(size_t size);
    int at(size_t index) const;
    void push_back(int value);
    size_t size() const;
};
```

Users know **exactly** what they can do:
- Create array with size
- Access elements
- Add elements
- Get size

Users **don't need to know**:
- How memory is managed
- What capacity means
- How resizing works

### **Reason 5: Easy Debugging**

**With public members:**
```cpp
class Array {
public:
    int* data;
    size_t size;
};

// Bug: Who set size incorrectly?
// Could be anywhere in the entire program!
```

**With private members:**
```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    void resize(size_t new_size) {
        // Only place size_ can change
        // Put breakpoint here to find bug
    }
};
```

### **Example: Vector_3d with Encapsulation**

**Implementation:**
```cpp
class Vector_3d {
private:
    double x_;
    double y_;
    double z_;
    
public:
    // Constructor
    Vector_3d(double x = 0.0, double y = 0.0, double z = 0.0)
        : x_{x}, y_{y}, z_{z} {
    }
    
    // Accessors (inline - zero overhead)
    double x() const { return x_; }
    double y() const { return y_; }
    double z() const { return z_; }
    
    // Mutators
    void x(double new_x) { x_ = new_x; }
    void y(double new_y) { y_ = new_y; }
    void z(double new_z) { z_ = new_z; }
    
    // Operations
    double norm() const {
        return std::sqrt(x_*x_ + y_*y_ + z_*z_);
    }
    
    Vector_3d operator+(Vector_3d const &other) const {
        return Vector_3d{x_ + other.x_, y_ + other.y_, z_ + other.z_};
    }
};
```

**Usage (looks natural!):**
```cpp
Vector_3d v1{1, 2, 3};
Vector_3d v2{4, 5, 6};

double x_val = v1.x();       // Get x component
v1.x(10);                     // Set x component

Vector_3d sum = v1 + v2;
double length = v1.norm();
```

### **Summary: Benefits of Encapsulation**

1. **Flexibility**: Change implementation without breaking user code
2. **Safety**: Maintain invariants, prevent invalid states
3. **Performance**: Compiler optimizations eliminate overhead
4. **Clarity**: Clear interface, hidden complexity
5. **Debugging**: Easy to track down bugs

**Cost**: Slightly more code to write (but worth it!)

---

## 10. Copies, Assignment and the Destructor

### **Default Copying Behavior**

C++ automatically copies objects **member by member**:

```cpp
class Pair {
private:
    int first_;
    int second_;
    
public:
    Pair(int f, int s) : first_{f}, second_{s} {}
};

int main() {
    Pair p1{1, 2};
    Pair p2 = p1;  // Copies first_ and second_
    Pair p3{p1};   // Same thing
}
```

### **Default Assignment Behavior**

C++ automatically assigns **member by member**:

```cpp
int main() {
    Pair p1{1, 2};
    Pair p2{3, 4};
    
    p2 = p1;  // Assigns first_ and second_ from p1 to p2
}
```

### **When Default Behavior is WRONG**

**Problem with pointers/dynamic memory:**

```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    Array(size_t size) : size_{size} {
        data_ = new int[size];
    }
    
    ~Array() {
        delete[] data_;
    }
};

int main() {
    Array a1{10};
    Array a2 = a1;  // DISASTER!
}
```

**What happens:**
```
a1:  data_ = 0x1000  size_ = 10
         |
         v
     [memory block]
     
a2:  data_ = 0x1000  size_ = 10  (same pointer!)
         |
         v
     [same memory block]
```

**Problems:**
1. **Shallow copy**: Both point to same memory
2. **Double delete**: Both destructors try to delete same memory
3. **Crash or corruption**

### **The Destructor**

**Destructor** cleans up when object is destroyed:

```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    Array(size_t size) : size_{size} {
        data_ = new int[size];
        std::cout << "Constructed Array" << std::endl;
    }
    
    ~Array() {  // Destructor: ~ClassName()
        delete[] data_;
        std::cout << "Destroyed Array" << std::endl;
    }
};

int main() {
    Array arr{10};
    // ... use arr ...
}  // Destructor called automatically here
```

**Destructor rules:**
1. **Name**: `~ClassName()`
2. **No parameters**
3. **No return type**
4. **Called automatically** when object goes out of scope or is deleted
5. **Only one destructor** per class (can't overload)

### **When Destructors are Called**

```cpp
void function() {
    Array local{5};
    // ...
}  // local destroyed here (end of scope)

int main() {
    Array a{10};
    
    if (true) {
        Array b{20};
        // ...
    }  // b destroyed here
    
    Array* ptr = new Array{30};
    delete ptr;  // Destructor called explicitly
    
    // ...
}  // a destroyed here
```

### **The Copy Constructor**

**Copy constructor** creates a new object as a copy of an existing one:

```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    // Regular constructor
    Array(size_t size) : size_{size} {
        data_ = new int[size];
    }
    
    // Copy constructor
    Array(Array const &other) : size_{other.size_} {
        data_ = new int[size_];  // Allocate NEW memory
        for (size_t i = 0; i < size_; ++i) {
            data_[i] = other.data_[i];  // Copy elements
        }
    }
    
    // Destructor
    ~Array() {
        delete[] data_;
    }
};

int main() {
    Array a1{10};
    Array a2 = a1;   // Calls copy constructor
    Array a3{a1};    // Also calls copy constructor
}
```

**Now each has its own memory:**
```
a1:  data_ = 0x1000  size_ = 10
         |
         v
     [memory block 1]
     
a2:  data_ = 0x2000  size_ = 10
         |
         v
     [memory block 2] (independent copy!)
```

### **The Assignment Operator**

**Assignment operator** assigns one existing object to another:

```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    // ... constructors and destructor ...
    
    // Assignment operator
    Array& operator=(Array const &other) {
        // Check for self-assignment
        if (this == &other) {
            return *this;
        }
        
        // Delete old data
        delete[] data_;
        
        // Copy new data
        size_ = other.size_;
        data_ = new int[size_];
        for (size_t i = 0; i < size_; ++i) {
            data_[i] = other.data_[i];
        }
        
        return *this;  // Return reference for chaining
    }
};

int main() {
    Array a1{10};
    Array a2{20};
    
    a2 = a1;  // Calls operator=
    
    // Can chain assignments
    Array a3{30};
    a3 = a2 = a1;  // a3 = (a2 = a1)
}
```

**Key steps in assignment:**
1. **Check self-assignment**: `if (this == &other)`
2. **Clean up old resources**: `delete[] data_`
3. **Copy new resources**: allocate and copy
4. **Return `*this`**: allow chaining

### **The Rule of Three**

If you need to define **one** of these, you probably need **all three**:
1. **Destructor** (`~ClassName()`)
2. **Copy constructor** (`ClassName(ClassName const &)`)
3. **Assignment operator** (`operator=`)

**Why?**
- If managing resources (memory, files, etc.)
- Default behavior does shallow copy
- Need custom code for proper cleanup and copying

### **Move Semantics (C++11)**

**Move** transfers ownership instead of copying:

```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    // ... other functions ...
    
    // Move constructor
    Array(Array &&other) noexcept 
        : data_{other.data_}, size_{other.size_} {
        // Steal resources
        other.data_ = nullptr;
        other.size_ = 0;
    }
    
    // Move assignment
    Array& operator=(Array &&other) noexcept {
        if (this == &other) {
            return *this;
        }
        
        // Delete old resources
        delete[] data_;
        
        // Steal new resources
        data_ = other.data_;
        size_ = other.size_;
        
        // Leave other in valid state
        other.data_ = nullptr;
        other.size_ = 0;
        
        return *this;
    }
};

Array create_array() {
    Array temp{100};
    return temp;  // Move, not copy!
}

int main() {
    Array a = create_array();  // Move constructor called
}
```

**Move vs Copy:**
```
Copy: Allocate new memory + copy all elements (SLOW)
Move: Just copy pointer (FAST)
```

### **The Rule of Five (C++11)**

If you need custom resource management:
1. **Destructor**
2. **Copy constructor**
3. **Copy assignment**
4. **Move constructor**
5. **Move assignment**

### **Indexing Operator**

Allow array-style access:

```cpp
class Array {
private:
    int* data_;
    size_t size_;
    
public:
    // ... constructors, destructor, copy/move ...
    
    // Indexing (read-write)
    int& operator[](size_t index) {
        return data_[index];
    }
    
    // Indexing (read-only for const objects)
    int operator[](size_t index) const {
        return data_[index];
    }
    
    // Safe access with bounds checking
    int& at(size_t index) {
        if (index >= size_) {
            throw std::out_of_range("Index out of bounds");
        }
        return data_[index];
    }
};

int main() {
    Array arr{10};
    
    arr[0] = 42;           // Write using operator[]
    int val = arr[0];      // Read using operator[]
    
    arr.at(0) = 100;       // Safe write
    // arr.at(20) = 5;     // Throws exception
}
```

---

## 11. Classes, Arrays and Dynamic Allocation

### **Arrays of Objects**

Just like arrays of `int`, you can have arrays of objects:

```cpp
class C {
private:
    double x_;
    
public:
    C(double x = 0.0) : x_{x} {
        std::cout << "C(double) called with " << x << std::endl;
    }
    
    C(int m, int n = 0) : x_{m + n} {
        std::cout << "C(int, int) called with " << m << ", " << n << std::endl;
    }
    
    ~C() {
        std::cout << "~C() called for " << x_ << std::endl;
    }
};

int main() {
    C data[4]{ {1}, {}, {5, 7}, {2.5} };
    // Calls: C(int,int) for {1}
    //        C(double) for {} → C(0.0)
    //        C(int,int) for {5,7}
    //        C(double) for {2.5}
    
    return 0;
}  // All 4 destructors called in reverse order
```

### **Default Constructor Requirement**

For arrays, class needs a **default constructor**:

```cpp
class NoDefault {
public:
    NoDefault(int x) {}  // No default constructor!
};

int main() {
    // NoDefault arr[5];  // ERROR: No default constructor
    
    NoDefault arr[3]{ {1}, {2}, {3} };  // OK: Explicit initialization
}
```

### **Calling Member Functions on Array Elements**

```cpp
class C {
public:
    C(double x = 0.0) : x_{x} {}
    
    std::string about() const {
        return "Value: " + std::to_string(x_);
    }
    
private:
    double x_;
};

int main() {
    C data[3]{ {1.0}, {2.0}, {3.0} };
    
    // Access each element
    for (size_t i = 0; i < 3; ++i) {
        std::cout << data[i].about() << std::endl;
    }
}
```

### **Dynamic Allocation of Single Objects**

Use `new` to allocate on the **heap**:

```cpp
class C {
public:
    C(double x) : x_{x} {
        std::cout << "Constructed C(" << x << ")" << std::endl;
    }
    
    ~C() {
        std::cout << "Destroyed C(" << x_ << ")" << std::endl;
    }
    
private:
    double x_;
};

int main() {
    C* ptr = new C{42.0};  // Allocate on heap
    
    std::cout << "Using object..." << std::endl;
    
    delete ptr;  // Must manually delete!
    
    return 0;
}
```

**Output:**
```
Constructed C(42)
Using object...
Destroyed C(42)
```

### **Dynamic Allocation of Arrays**

```cpp
int main() {
    size_t n = 5;
    C* arr = new C[n];  // Array of n objects
    
    // ... use arr ...
    
    delete[] arr;  // MUST use delete[] for arrays!
    
    return 0;
}
```

**Critical:** Use `delete[]` not `delete` for arrays!

### **The Arrow Operator (->)**

For pointers to objects, use `->` instead of `.`:

```cpp
class C {
public:
    C(double x) : x_{x} {}
    
    double value() const { return x_; }
    
    void set_value(double x) { x_ = x; }
    
private:
    double x_;
};

int main() {
    C obj{10.0};
    C* ptr = &obj;
    
    // Using dot operator on object
    std::cout << obj.value() << std::endl;
    obj.set_value(20.0);
    
    // Using arrow operator on pointer
    std::cout << ptr->value() << std::endl;
    ptr->set_value(30.0);
    
    // Arrow operator is shorthand for:
    // (*ptr).value()
    // (*ptr).set_value(30.0)
    
    return 0;
}
```

**Equivalence:**
```cpp
ptr->member()    ≡    (*ptr).member()
```

### **When Constructor/Destructor are Called**

```cpp
class Tracker {
public:
    Tracker(int id) : id_{id} {
        std::cout << "Construct " << id_ << std::endl;
    }
    
    ~Tracker() {
        std::cout << "Destruct " << id_ << std::endl;
    }
    
private:
    int id_;
};

int main() {
    std::cout << "=== Start ===" << std::endl;
    
    Tracker a{1};  // Construct 1
    
    {
        Tracker b{2};  // Construct 2
    }  // Destruct 2 (end of scope)
    
    Tracker* ptr = new Tracker{3};  // Construct 3
    
    std::cout << "=== Before delete ===" << std::endl;
    delete ptr;  // Destruct 3
    
    std::cout << "=== End ===" << std::endl;
    return 0;
}  // Destruct 1 (end of main)
```

**Output:**
```
=== Start ===
Construct 1
Construct 2
Destruct 2
Construct 3
=== Before delete ===
Destruct 3
=== End ===
Destruct 1
```

### **Pointer Subtleties**

**Dangling pointer:**
```cpp
C* ptr;
{
    C temp{42};
    ptr = &temp;
}  // temp destroyed here!

// ptr->value();  // UNDEFINED BEHAVIOR: temp is destroyed
```

**Memory leak:**
```cpp
void leak() {
    C* ptr = new C{42};
    // ... use ptr ...
    // Forgot to delete!
}  // ptr goes out of scope, but object still in memory!
```

**Double delete:**
```cpp
C* ptr = new C{42};
delete ptr;
delete ptr;  // UNDEFINED BEHAVIOR: already deleted
```

### **Best Practices**

1. **Prefer stack allocation** - Automatic cleanup
2. **Use smart pointers** - `std::unique_ptr`, `std::shared_ptr`
3. **Always pair new with delete** - No memory leaks
4. **Use delete[] for arrays** - Not just delete
5. **Set pointers to nullptr after delete** - Prevent double delete

```cpp
#include <memory>

int main() {
    // Smart pointer - automatic cleanup
    std::unique_ptr<C> ptr = std::make_unique<C>(42.0);
    
    // No need for manual delete!
    
    return 0;
}  // ptr automatically deleted
```

---

## Summary

These slides cover the **fundamental concepts** of object-oriented programming in C++:

1. **Classes**: Bundle related data together
2. **Encapsulation**: Hide implementation details, control access
3. **Constructors**: Initialize objects correctly
4. **Destructors**: Clean up resources
5. **Member functions**: Operations on objects
6. **Operator overloading**: Natural syntax for custom types
7. **Exception handling**: Robust error management
8. **Copy/Move semantics**: Efficient resource management
9. **Dynamic allocation**: Heap memory management

Mastering these concepts is essential for writing professional, maintainable C++ code!
