Ah! You want the **Markdown (.md) file** with all the code examples! Here it is:

---

```markdown
# Complete C++ Classes Code Examples

## Table of Contents
1. [Rational Number Class](#rational-number-class)
2. [Vector 3D Class](#vector-3d-class)
3. [Array Class with Dynamic Memory](#array-class-with-dynamic-memory)
4. [Simple Example Classes](#simple-example-classes)
5. [Compilation Instructions](#compilation-instructions)

---

## Rational Number Class

### Rational.h
```cpp
#ifndef RATIONAL_H
#define RATIONAL_H

#include <iostream>
#include <stdexcept>
#include <string>

class Rational {
public:
    // Constructors
    Rational();
    Rational(int numer, int denom = 1);
    
    // Query functions (const)
    int numer() const;
    int denom() const;
    Rational abs() const;
    
    // Arithmetic operators (member functions)
    Rational operator+(Rational const &rhs) const;
    Rational operator-(Rational const &rhs) const;
    Rational operator-() const;  // Unary minus
    Rational operator*(Rational const &rhs) const;
    Rational operator/(Rational const &rhs) const;
    
    // Comparison operators
    bool operator==(Rational const &rhs) const;
    bool operator!=(Rational const &rhs) const;
    bool operator<(Rational const &rhs) const;
    bool operator<=(Rational const &rhs) const;
    bool operator>(Rational const &rhs) const;
    bool operator>=(Rational const &rhs) const;
    
    // Modifiers (non-const)
    void numer(int new_numer);
    void denom(int new_denom);
    
private:
    int numer_;
    int denom_;
    
    // Private helper functions
    void normalize();
    static int gcd(int a, int b);
};

// Non-member operators (for commutativity)
Rational operator+(int lhs, Rational const &rhs);
Rational operator-(int lhs, Rational const &rhs);
Rational operator*(int lhs, Rational const &rhs);
Rational operator/(int lhs, Rational const &rhs);

// Output operator
std::ostream& operator<<(std::ostream &out, Rational const &r);

#endif
```

### Rational.cpp
```cpp
#include "Rational.h"
#include <cmath>

// Default constructor
Rational::Rational():
    numer_{0},
    denom_{1} {
}

// Parameterized constructor
Rational::Rational(int numer, int denom):
    numer_{numer},
    denom_{denom} {
    
    if (denom_ == 0) {
        throw std::domain_error{"Denominator must be non-zero"};
    }
    
    normalize();
}

// Query: Get numerator
int Rational::numer() const {
    return numer_;
}

// Query: Get denominator
int Rational::denom() const {
    return denom_;
}

// Query: Absolute value
Rational Rational::abs() const {
    return Rational{std::abs(numer_), denom_};
}

// Addition
Rational Rational::operator+(Rational const &rhs) const {
    int new_numer = numer_ * rhs.denom_ + rhs.numer_ * denom_;
    int new_denom = denom_ * rhs.denom_;
    return Rational{new_numer, new_denom};
}

// Subtraction
Rational Rational::operator-(Rational const &rhs) const {
    return *this + (-rhs);
}

// Unary minus
Rational Rational::operator-() const {
    return Rational{-numer_, denom_};
}

// Multiplication
Rational Rational::operator*(Rational const &rhs) const {
    return Rational{numer_ * rhs.numer_, denom_ * rhs.denom_};
}

// Division
Rational Rational::operator/(Rational const &rhs) const {
    if (rhs.numer_ == 0) {
        throw std::domain_error{"Division by zero"};
    }
    return Rational{numer_ * rhs.denom_, denom_ * rhs.numer_};
}

// Equality
bool Rational::operator==(Rational const &rhs) const {
    return (numer_ == rhs.numer_) && (denom_ == rhs.denom_);
}

// Inequality
bool Rational::operator!=(Rational const &rhs) const {
    return !(*this == rhs);
}

// Less than
bool Rational::operator<(Rational const &rhs) const {
    return (numer_ * rhs.denom_) < (rhs.numer_ * denom_);
}

// Less than or equal
bool Rational::operator<=(Rational const &rhs) const {
    return (*this < rhs) || (*this == rhs);
}

// Greater than
bool Rational::operator>(Rational const &rhs) const {
    return !(*this <= rhs);
}

// Greater than or equal
bool Rational::operator>=(Rational const &rhs) const {
    return !(*this < rhs);
}

// Modifier: Set numerator
void Rational::numer(int new_numer) {
    numer_ = new_numer;
    normalize();
}

// Modifier: Set denominator
void Rational::denom(int new_denom) {
    if (new_denom == 0) {
        throw std::domain_error{"Denominator must be non-zero"};
    }
    denom_ = new_denom;
    normalize();
}

// Private: Normalize to lowest terms
void Rational::normalize() {
    if (denom_ < 0) {
        numer_ = -numer_;
        denom_ = -denom_;
    }
    
    int g = gcd(std::abs(numer_), denom_);
    numer_ /= g;
    denom_ /= g;
}

// Static: Calculate GCD
int Rational::gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}

// Non-member operators
Rational operator+(int lhs, Rational const &rhs) {
    return Rational{lhs} + rhs;
}

Rational operator-(int lhs, Rational const &rhs) {
    return Rational{lhs} - rhs;
}

Rational operator*(int lhs, Rational const &rhs) {
    return Rational{lhs} * rhs;
}

Rational operator/(int lhs, Rational const &rhs) {
    return Rational{lhs} / rhs;
}

// Output operator
std::ostream& operator<<(std::ostream &out, Rational const &r) {
    if (r.denom() == 1) {
        out << r.numer();
    } else {
        out << r.numer() << "/" << r.denom();
    }
    return out;
}
```

### main_rational.cpp
```cpp
#include "Rational.h"
#include <iostream>

int main() {
    std::cout << "=== Rational Number Class Demo ===\n\n";
    
    try {
        // Constructors
        std::cout << "1. Constructors:\n";
        Rational r1;
        Rational r2{3, 4};
        Rational r3{6, 8};  // Normalized to 3/4
        Rational r4{5};
        
        std::cout << "r1 = " << r1 << " (default)\n";
        std::cout << "r2 = " << r2 << "\n";
        std::cout << "r3 = " << r3 << " (normalized from 6/8)\n";
        std::cout << "r4 = " << r4 << "\n\n";
        
        // Arithmetic
        std::cout << "2. Arithmetic:\n";
        Rational a{1, 2};
        Rational b{1, 3};
        
        std::cout << a << " + " << b << " = " << (a + b) << "\n";
        std::cout << a << " - " << b << " = " << (a - b) << "\n";
        std::cout << a << " * " << b << " = " << (a * b) << "\n";
        std::cout << a << " / " << b << " = " << (a / b) << "\n";
        std::cout << "-(" << a << ") = " << (-a) << "\n\n";
        
        // Comparisons
        std::cout << "3. Comparisons:\n";
        std::cout << a << " == " << b << " : " << (a == b) << "\n";
        std::cout << a << " != " << b << " : " << (a != b) << "\n";
        std::cout << a << " < " << b << " : " << (a < b) << "\n";
        std::cout << a << " > " << b << " : " << (a > b) << "\n\n";
        
        // Mixed operations
        std::cout << "4. Mixed with integers:\n";
        std::cout << a << " + 2 = " << (a + 2) << "\n";
        std::cout << "2 + " << a << " = " << (2 + a) << "\n";
        std::cout << a << " * 3 = " << (a * 3) << "\n";
        std::cout << "3 * " << a << " = " << (3 * a) << "\n\n";
        
        // Exception handling
        std::cout << "5. Exception handling:\n";
        try {
            Rational bad{1, 0};
        } catch (std::domain_error const &e) {
            std::cout << "Caught: " << e.what() << "\n";
        }
        
        try {
            Rational zero{0};
            Rational result = a / zero;
        } catch (std::domain_error const &e) {
            std::cout << "Caught: " << e.what() << "\n";
        }
        
    } catch (std::exception const &e) {
        std::cerr << "Error: " << e.what() << "\n";
        return 1;
    }
    
    return 0;
}
```

---

## Vector 3D Class

### Vector_3d.h
```cpp
#ifndef VECTOR_3D_H
#define VECTOR_3D_H

#include <cmath>

class Vector_3d {
public:
    // Constructors
    Vector_3d(double x = 0.0, double y = 0.0, double z = 0.0);
    
    // Accessors (const)
    double x() const;
    double y() const;
    double z() const;
    
    // Mutators
    void x(double new_x);
    void y(double new_y);
    void z(double new_z);
    
    // Vector operations (const)
    double norm() const;
    double inner_product(Vector_3d const &other) const;
    Vector_3d cross(Vector_3d const &other) const;
    
    // Modifiers
    void normalize();
    
    // Operators
    Vector_3d operator+(Vector_3d const &rhs) const;
    Vector_3d operator-(Vector_3d const &rhs) const;
    Vector_3d operator*(double scalar) const;
    
private:
    double x_;
    double y_;
    double z_;
};

// Non-member operator
Vector_3d operator*(double scalar, Vector_3d const &v);

#endif
```

### Vector_3d.cpp
```cpp
#include "Vector_3d.h"

// Constructor
Vector_3d::Vector_3d(double x, double y, double z):
    x_{x},
    y_{y},
    z_{z} {
}

// Accessors
double Vector_3d::x() const {
    return x_;
}

double Vector_3d::y() const {
    return y_;
}

double Vector_3d::z() const {
    return z_;
}

// Mutators
void Vector_3d::x(double new_x) {
    x_ = new_x;
}

void Vector_3d::y(double new_y) {
    y_ = new_y;
}

void Vector_3d::z(double new_z) {
    z_ = new_z;
}

// Norm (magnitude)
double Vector_3d::norm() const {
    return std::sqrt(x_*x_ + y_*y_ + z_*z_);
}

// Inner product (dot product)
double Vector_3d::inner_product(Vector_3d const &other) const {
    return x_*other.x_ + y_*other.y_ + z_*other.z_;
}

// Cross product
Vector_3d Vector_3d::cross(Vector_3d const &other) const {
    return Vector_3d{
        y_*other.z_ - z_*other.y_,
        z_*other.x_ - x_*other.z_,
        x_*other.y_ - y_*other.x_
    };
}

// Normalize (convert to unit vector)
void Vector_3d::normalize() {
    double n = norm();
    if (n != 0.0) {
        x_ /= n;
        y_ /= n;
        z_ /= n;
    }
}

// Vector addition
Vector_3d Vector_3d::operator+(Vector_3d const &rhs) const {
    return Vector_3d{x_ + rhs.x_, y_ + rhs.y_, z_ + rhs.z_};
}

// Vector subtraction
Vector_3d Vector_3d::operator-(Vector_3d const &rhs) const {
    return Vector_3d{x_ - rhs.x_, y_ - rhs.y_, z_ - rhs.z_};
}

// Scalar multiplication
Vector_3d Vector_3d::operator*(double scalar) const {
    return Vector_3d{x_*scalar, y_*scalar, z_*scalar};
}

// Non-member scalar multiplication
Vector_3d operator*(double scalar, Vector_3d const &v) {
    return v * scalar;
}
```

---

## Array Class with Dynamic Memory

### Array.h
```cpp
#ifndef ARRAY_H
#define ARRAY_H

#include <cstddef>
#include <stdexcept>

class Array {
public:
    // Constructor
    Array(std::size_t capacity, double value = 0.0);
    
    // Destructor
    ~Array();
    
    // Copy constructor
    Array(Array const &original);
    
    // Copy assignment operator
    Array& operator=(Array const &rhs);
    
    // Move constructor (C++11)
    Array(Array &&original);
    
    // Move assignment operator (C++11)
    Array& operator=(Array &&rhs);
    
    // Accessors
    std::size_t capacity() const;
    
    // Indexing (no bounds checking)
    double operator[](std::size_t k) const;
    double& operator[](std::size_t k);
    
    // At (with bounds checking)
    double at(std::size_t k) const;
    double& at(std::size_t k);
    
private:
    std::size_t capacity_;
    double *array_;
};

#endif
```

### Array.cpp
```cpp
#include "Array.h"
#include <utility>  // For std::swap
#include <string>

// Constructor
Array::Array(std::size_t capacity, double value):
    capacity_{capacity},
    array_{new double[capacity_]} {
    
    for (std::size_t k = 0; k < capacity_; ++k) {
        array_[k] = value;
    }
}

// Destructor
Array::~Array() {
    delete[] array_;
    array_ = nullptr;
    capacity_ = 0;
}

// Copy constructor (deep copy)
Array::Array(Array const &original):
    capacity_{original.capacity_},
    array_{new double[capacity_]} {
    
    for (std::size_t k = 0; k < capacity_; ++k) {
        array_[k] = original.array_[k];
    }
}

// Copy assignment operator
Array& Array::operator=(Array const &rhs) {
    if (this != &rhs) {
        // Copy-and-swap idiom
        Array copy{rhs};
        std::swap(capacity_, copy.capacity_);
        std::swap(array_, copy.array_);
    }
    return *this;
}

// Move constructor
Array::Array(Array &&original):
    capacity_{original.capacity_},
    array_{original.array_} {
    
    // Leave original in valid state
    original.capacity_ = 0;
    original.array_ = nullptr;
}

// Move assignment operator
Array& Array::operator=(Array &&rhs) {
    if (this != &rhs) {
        delete[] array_;
        
        capacity_ = rhs.capacity_;
        array_ = rhs.array_;
        
        rhs.capacity_ = 0;
        rhs.array_ = nullptr;
    }
    return *this;
}

// Get capacity
std::size_t Array::capacity() const {
    return capacity_;
}

// Indexing operator (const version)
double Array::operator[](std::size_t k) const {
    return array_[k];
}

// Indexing operator (non-const version)
double& Array::operator[](std::size_t k) {
    return array_[k];
}

// At with bounds checking (const version)
double Array::at(std::size_t k) const {
    if (k >= capacity_) {
        throw std::out_of_range{
            "Index " + std::to_string(k) + 
            " out of range [0, " + std::to_string(capacity_-1) + "]"
        };
    }
    return array_[k];
}

// At with bounds checking (non-const version)
double& Array::at(std::size_t k) {
    if (k >= capacity_) {
        throw std::out_of_range{
            "Index " + std::to_string(k) + 
            " out of range [0, " + std::to_string(capacity_-1) + "]"
        };
    }
    return array_[k];
}
```

### main_array.cpp
```cpp
#include "Array.h"
#include <iostream>

int main() {
    std::cout << "=== Array Class Demo ===\n\n";
    
    try {
        // Constructor
        std::cout << "1. Creating array of size 10:\n";
        Array data{10, 3.14};
        std::cout << "Capacity: " << data.capacity() << "\n\n";
        
        // Indexing
        std::cout << "2. Setting values:\n";
        for (std::size_t k = 0; k < 10; ++k) {
            data[k] = k * k;
        }
        
        std::cout << "3. Reading values:\n";
        for (std::size_t k = 0; k < 10; ++k) {
            std::cout << "data[" << k << "] = " << data[k] << "\n";
        }
        std::cout << "\n";
        
        // Copy constructor
        std::cout << "4. Copy constructor:\n";
        Array copy{data};
        std::cout << "copy capacity: " << copy.capacity() << "\n";
        std::cout << "copy[0] = " << copy[0] << "\n\n";
        
        // Assignment
        std::cout << "5. Assignment operator:\n";
        Array another{5};
        another = data;
        std::cout << "another capacity: " << another.capacity() << "\n";
        std::cout << "another[0] = " << another[0] << "\n\n";
        
        // Bounds checking with at()
        std::cout << "6. Bounds checking:\n";
        std::cout << "data.at(5) = " << data.at(5) << "\n";
        
        try {
            std::cout << "Trying data.at(100)...\n";
            data.at(100);
        } catch (std::out_of_range const &e) {
            std::cout << "Caught: " << e.what() << "\n";
        }
        
    } catch (std::exception const &e) {
        std::cerr << "Error: " << e.what() << "\n";
        return 1;
    }
    
    return 0;
}
```

---

## Simple Example Classes

### Body.h (3-Body Simulation)
```cpp
#ifndef BODY_H
#define BODY_H

class Body {
public:
    double position_[3];
    double velocity_[3];
    double mass_;
};

#endif
```

### Pair.h
```cpp
#ifndef PAIR_H
#define PAIR_H

#include <iostream>

class Pair {
public:
    Pair(int first, int second);
    
    // Friend function for output
    friend std::ostream& operator<<(std::ostream &out, Pair const &p);
    
private:
    int first_;
    int second_;
};

#endif
```

### Pair.cpp
```cpp
#include "Pair.h"

Pair::Pair(int first, int second):
    first_{first},
    second_{second} {
}

std::ostream& operator<<(std::ostream &out, Pair const &p) {
    out << "(" << p.first_ << "," << p.second_ << ")";
    return out;
}
```

---

## Demo Classes from Slides

### Class C (Multiple Constructors)
```cpp
#include <iostream>
#include <string>

class C {
public:
    C(double x = 0.0);
    C(int m, int n = 0);
    std::string about() const;
};

C::C(double x) {
    std::cout << "Calling C(double)" << std::endl;
}

C::C(int m, int n) {
    std::cout << "Calling C(int, int)" << std::endl;
}

std::string C::about() const {
    return "Harmless";
}

int main() {
    C data[10]{ {1}, {}, {5, 7}, {2.5} };
    
    for (std::size_t k = 0; k < 10; ++k) {
        std::cout << data[k].about() << std::endl;
    }
    
    return 0;
}
```

### Class G (With Value Retrieval)
```cpp
#include <iostream>

class G {
public:
    G(int n);
    G(G const &original);
    ~G();
    int retrieve() const;
    
private:
    int value_;
};

G::G(int new_value):
    value_{new_value} {
    std::cout << "Calling G(int)" << std::endl;
}

G::G(G const &original):
    value_{original.value_} {
    std::cout << "Calling G(G const &)" << std::endl;
}

G::~G() {
    std::cout << "Calling ~G()" << std::endl;
}

int G::retrieve() const {
    return value_;
}

int main() {
    // Single object
    G *p_item{ new G{3} };
    delete p_item;
    p_item = nullptr;
    
    // Array of objects
    G *a_items{ new G[3]{ {101}, {102}, {103} } };
    
    for (std::size_t k = 0; k < 3; ++k) {
        std::cout << a_items[k].retrieve() << std::endl;
    }
    
    delete[] a_items;
    a_items = nullptr;
    
    return 0;
}
```

### Node Class (Linked List)
```cpp
#include <iostream>

class Node {
public:
    int value_;
    Node *p_next_;
};

int main() {
    Node *p_42{ new Node{42, nullptr} };
    std::cout << "p_42 == " << p_42 << std::endl;
    
    Node *p_91{ new Node{91, p_42} };
    std::cout << "p_91 == " << p_91 << std::endl;
    
    std::cout << p_91->value_ << std::endl;
    std::cout << p_91->p_next_ << std::endl;
    std::cout << p_91->p_next_->value_ << std::endl;
    std::cout << p_91->p_next_->p_next_ << std::endl;
    
    delete p_91->p_next_;
    p_91->p_next_ = nullptr;
    
    p_42 = nullptr;  // Now dangling
    
    delete p_91;
    p_91 = nullptr;
    
    return 0;
}
```

---

## Compilation Instructions

### For Rational Class
```bash
# Compile separately
g++ -std=c++17 -c Rational.cpp -o Rational.o
g++ -std=c++17 -c main_rational.cpp -o main_rational.o
g++ Rational.o main_rational.o -o rational_demo

# Or compile all at once
g++ -std=c++17 -Wall -Wextra main_rational.cpp Rational.cpp -o rational_demo

# Run
./rational_demo
```

### For Array Class
```bash
g++ -std=c++17 -Wall -Wextra main_array.cpp Array.cpp -o array_demo
./array_demo
```

### For Vector_3d Class
```bash
g++ -std=c++17 -Wall -Wextra main_vector.cpp Vector_3d.cpp -o vector_demo
./vector_demo
```

### For Simple Examples
```bash
# Single file examples
g++ -std=c++17 -Wall -Wextra example_class_c.cpp -o demo_c
./demo_c

g++ -std=c++17 -Wall -Wextra example_class_g.cpp -o demo_g
./demo_g

g++ -std=c++17 -Wall -Wextra example_node.cpp -o demo_node
./demo_node
```

---

## Makefile (Optional)

```makefile
CXX = g++
CXXFLAGS = -std=c++17 -Wall -Wextra

all: rational_demo array_demo vector_demo

rational_demo: main_rational.o Rational.o
	$(CXX) $(CXXFLAGS) -o rational_demo main_rational.o Rational.o

array_demo: main_array.o Array.o
	$(CXX) $(CXXFLAGS) -o array_demo main_array.o Array.o

vector_demo: main_vector.o Vector_3d.o
	$(CXX) $(CXXFLAGS) -o vector_demo main_vector.o Vector_3d.o

main_rational.o: main_rational.cpp Rational.h
	$(CXX) $(CXXFLAGS) -c main_rational.cpp

Rational.o: Rational.cpp Rational.h
	$(CXX) $(CXXFLAGS) -c Rational.cpp

main_array.o: main_array.cpp Array.h
	$(CXX) $(CXXFLAGS) -c main_array.cpp

Array.o: Array.cpp Array.h
	$(CXX) $(CXXFLAGS) -c Array.cpp

main_vector.o: main_vector.cpp Vector_3d.h
	$(CXX) $(CXXFLAGS) -c main_vector.cpp

Vector_3d.o: Vector_3d.cpp Vector_3d.h
	$(CXX) $(CXXFLAGS) -c Vector_3d.cpp

clean:
	rm -f *.o rational_demo array_demo vector_demo
```

Usage:
```bash
make
make clean
```

---

## Expected Output Examples

### Rational Demo Output
```
=== Rational Number Class Demo ===

1. Constructors:
r1 = 0 (default)
r2 = 3/4
r3 = 3/4 (normalized from 6/8)
r4 = 5

2. Arithmetic:
1/2 + 1/3 = 5/6
1/2 - 1/3 = 1/6
1/2 * 1/3 = 1/6
1/2 / 1/3 = 3/2
-(1/2) = -1/2

3. Comparisons:
1/2 == 1/3 : 0
1/2 != 1/3 : 1
1/2 < 1/3 : 0
1/2 > 1/3 : 1

4. Mixed with integers:
1/2 + 2 = 5/2
2 + 1/2 = 5/2
1/2 * 3 = 3/2
3 * 1/2 = 3/2

5. Exception handling:
Caught: Denominator must be non-zero
Caught: Division by zero
```

### Array Demo Output
```
=== Array Class Demo ===

1. Creating array of size 10:
Capacity: 10

2. Setting values:
3. Reading values:
data[0] = 0
data[1] = 1
data[2] = 4
data[3] = 9
data[4] = 16
data[5] = 25
data[6] = 36
data[7] = 49
data[8] = 64
data[9] = 81

4. Copy constructor:
copy capacity: 10
copy[0] = 0

5. Assignment operator:
another capacity: 10
another[0] = 0

6. Bounds checking:
data.at(5) = 25
Trying data.at(100)...
Caught: Index 100 out of range [0, 9]
```

---

## Testing Checklist

- [ ] Constructors work correctly
- [ ] Destructors free memory (check with valgrind)
- [ ] Copy constructor performs deep copy
- [ ] Assignment operator works correctly
- [ ] Operators behave as expected
- [ ] Exception handling works
- [ ] Const correctness maintained
- [ ] No memory leaks (valgrind --leak-check=full)

---

## Common Errors and Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| Segmentation fault | Using deleted pointer | Set to nullptr after delete |
| Double free | Deleting same memory twice | Implement proper copy/assignment |
| Memory leak | Forgot to delete | Add delete in destructor |
| Undefined reference | Missing function definition | Implement all declared functions |
| Cannot access private member | Trying to access private from outside | Use public member functions |

---

## Additional Resources

- [cplusplus.com](http://www.cplusplus.com/) - C++ reference
- [cppreference.com](https://en.cppreference.com/) - Comprehensive C++ reference
- Valgrind for memory leak detection: `valgrind --leak-check=full ./program`
- GDB for debugging: `gdb ./program`

---

**End of Code Examples** 🎉
```

---

**This is the complete Markdown file with all the code!** You can save this as `cpp_classes_complete_code.md` and use it as a reference. 📝
