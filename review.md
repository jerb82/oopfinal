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


## RAII
### Resource Acquisition Is Initialization
Constructor: Allocate resource
Destructor: Deallocate resource

Prevents resource leaks, double-free, use when invalid

How to implement?


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

## PIMPL

### ***P***ointer to ***IMPL***ementation

- *Use a pointer to a class/struct declaration for an implementation class in the target class declaration file (i.e., .hpp)*
- *Define the implementation class in the target class definition file (i.e., .cp*p)*  
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
