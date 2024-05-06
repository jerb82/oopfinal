## Lambda Functions

### Be able to write with capture and parameters

| Lambda         | Description                | Parameter      |
|----------------|----------------------------|----------------|
| [](char c) {   | Output character           | IN             |
|    std::cout << c; |                          |                |
| }              |                            |                |
| [](char& c) {  | Convert to uppercase       | IN/OUT         |
|    c = std::toupper(c); |                    |                |
| }             |                            |                |

The captures of a lambda are whatever is within the [], there can be empty captures, or you have have it be when you want to pass in everything, or we can name everything that we pass in, one by one,
#### Lambda: `int` comparison
```C++

[](const void* e1, const void* e2)->int { 
    int n1 = *(int*) e1;
    int n2 = *(int*) e2;
    
    if (n1 < n2)
        return -1;
    else if (n1 > n2)
        return 1;
    else
        return 0;
}
```
## Extension Points
- The points in the program you can register a handler for
- Every extension point has a single handler
- The handler is passed all data as parameters
*Dealing with Handlers*
![alt text](image.png)
***note:*** *uhh a lot of this i am putting is incomplete this should all relate to lambdas but im still trying to figure how (maybe look over this for now)*  
  
## RAII
### Resource Acquisition Is Initialization

Holding a resource is tied to an object's lifetime

Constructor: Allocate resource
Destructor: Deallocate resource

Prevents resource leaks, double-free, use when invalid

---
#### How to implement?  
- Constructor: Allocate resource
- Destructor: Deallocate resource
- Access: Direct access to a resource
- Boolean: Check if the resource exists (i.e., has been allocated and is still valid)
- Copy, Assignment: Transfer resource control to new RAII object
- Deallocate: Deallocate resource before Destructor (safely)


**RAII: `std::ofstream`**
```C++
// non-RAII usage
{
    std::ofstream out;
    
    out.open("output.md");
    
    out << "# RAII" << '\n';
    
    out.close();

```

```C++
// RAII usage
{
    std::ofstream out("output.md");
    
    out << "# RAII" << '\n';
}
```
We use smart pointers
- Smart pointers are automatically initialized with `nullptr`
- Automatic destructor call for deallocation of the contained resource

## SOLID

Know the list of principles, both abbreviations and full names

### Principles 
* **S**ingle Responsibility Principle (SRP)
* **O**pen/closed Principle (OCP)
* **L**iskov Substitution Principal (LSP)
* **I**nterface Segregation Principle (ISP)
* **D**ependency Inversion Principle (DIP)


## Coupling
Definition, list of types of coupling, why decoupling is important

Degree of interdependence between software modules; a measure of how closely connected two routines or modules are; the strength of the relationships between modules

Types of Coupling
-  Message Coupling *(very best)*
- Data Coupling *(best)* 
- Stamp Coupling  
- Control Coupling  
- Common Coupling
- Content Coupling *(worst)*

Why is it important?
- Perhaps the most important characteristics of a system
- Affects development path
- Affects how we partition the system for testing
- Affects how much reuse is possible
- Significant effect on the complexity of a system

## API 

### Application Programming Interface

- Large-scale mechanism for *separation of concerns*
(need to add more, there wasn't much in the slides)  

**Good APIs**
- Low complexity
- High degree of safety
- Flexible enough
- Efficient enough

## PIMPL

### ***P***ointer to ***IMPL***ementation

- *Use a pointer to a class/struct declaration for an implementation class in the target class declaration file (i.e., .hpp)*
- *Define the implementation class in the target class definition file (i.e., .cp*p)*  

**Reasons**  
- Class implementation is hidden  
- Header file containing the class declaration only needs to include those files required for the class interface rather than for its implementation  
---
**Include**
- Implement using a class or struct.  
- Typically, it is a struct since there is no need to keep it private.
- A struct/class declaration forms an incomplete type
std::unique_ptr<> can work with incomplete types (with one issue)  
- std::unique_ptr<> used instead of raw pointer for RAII. I.e., don't depend on the destructor of the class to run
```C++

/*
    Good.hpp
    Declaration of class Good
    Is a good class... but does not have PIMPL
*/

#ifndef INCLUDED_GOOD_HPP
#define INCLUDED_GOOD_HPP

#include <vector>
#include <optional>

class Good {
public:
    // constructor
    Good();
    
    // operation
    void op(int n);

private:
    std::optional<int> firstnum;
    std::vector<int> numbers;
};

#endif
```

**PIMPL**
```C++

/*
    Good.hpp
    Declaration of class Good
*/

#ifndef INCLUDED_GOOD_HPP
#define INCLUDED_GOOD_HPP

#include <memory>

class Good {
public:
    // constructor
    Good();
    
    // operation
    void op(int n);

    // destructor
    ~Good();

private:
    struct GoodImpl;
    std::unique_ptr<GoodImpl> impl;
};

#endif
```
**Implementation**
- Define the PIMPL struct in the definition file
- Target class data member initiaization is now done in the PIMPL struct
- Access to "members" is through a pointer, as opposed to when they were just members  

```C++

/*
    Good.cpp
    Implementation of class Good
*/

#include "Good.hpp"
#include <vector>
#include <optional>

struct Good::GoodImpl {
    GoodImpl() : numbers(1000) {}
    std::optional<int> firstnum;
    std::vector<int> numbers;
};

// constructor
Good::Good()
    : impl(new GoodImpl)
{}
// destructor
Good::~Good() = default;

// operation
void Good::op(int n) {

    // record first number inserted
    if (!impl->firstnum)
        impl->firstnum = n;

    impl->numbers.push_back(n);
}
...
```

## Dispatch
Static vs. Dynamic, Given code and output, change code to generate the proper output


Dispatch:
Selecting which implementation of an operation (method or function) to call

### Static Dispatch
- Which operation (method or function) will be called is determined at compile time
- Fast as a call can be made
- Preferred by the compiler for these reasons

- The compiler uses static dispatch whenever it can: 
    * free functions
    * non-virtual method
    * static methods
    * method calls from non-pointer and non-reference variables

```C++
void f() {}

class C {
public:
    void f() {}
    virtual void m() {}
    static void s() {}
};

    f();
    C c;
    c.f();
    c.m();
    c.s();

    C* pc = &c;
    pc->f();
//    pc->m();
    pc->s();

    C& rc = c;
    rc.f();
//    rc.m();
    rc.s();
```  
---  
### Dynamic Dispatch
- For virtual methods call whose object is a *pointer* or *reference*
- Requires more code than static dispatch
- Slightly slower to call
- Compilers have optimized dynamic dispatch, so the speed disadvantage is smaller than it used to be

