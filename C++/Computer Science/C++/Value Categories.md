#ComputerScience #cpp #valuecategories
***Table of content*:**
- [Introduction](#introduction)
- [Types](#types)
- [Properties](#properties)
	- [Lvalue](#lvalue)
	- [PRvalue](#prvalue)
	- [Xvalue](#xvalue)
	- [GLvalue](#glvalue)
	- [Rvalue](#rvalue)

<a id="introduction"></a>
# Introduction 

It is result from [[Expressions]] 
<a id="types"></a>
# Types
It has 2 types:
- Primary
	- lvalue
	- xvalue
	- prvalue
- Mixed
	- glvalue
	- rvalue
 ![[Value Categories visual.png]]
 
<a id="properties "></a>
# Properties 
 
 independent properties:
	- **"has identity"** : 
		- denoted with a lower-case **i**. 
		- does not have the identity property, it will denoted with a upper-case **I**
		- value is _identified_ by a name.
		- Pointers and references also represent identities.
	- **"can be moved from"**  : 
		- denoted with a lower-case **m**
		-  does not have move, it will denoted with a upper-case **m**
		- the ownership can be move [[RAII]]


![[Value categories properties visual.png]]
<a id="lvalue "></a>
## LValue
- name by an identifier (**i**)  
- cannot be moved (**M**)
- has a location in memory
- sometimes referred to as locator values

```cpp
int i = 3;                       // i is an lvalue.
std::string s = "Hello, world!"; // s is an lvalue.
int* p = nullptr;                // p is an lvalue.
int& r = *p;                     // r is an lvalue.
const int i = 3; // i is an lvalue.
int j = i;       // i is an lvalue on the right side of the assignment operator.
```

> [!Note]
> An easy way to remember if something is an lvalue is to ask the question “is it possible to take the address of this value?”. If the answer is yes, then the value is an lvalue.

<a id="prvalue "></a>
## PRvalue
-  it is a pure rvalue. 
- do not have an identifier (**I**)
- can be moved (**m**)
- **literal values** are examples of prvalues
 ```cpp
	int i = 3;                       // 3 is a prvalue.
	std::string s = "Hello, world!"; // "Hello, world!" is a prvalue.
	int* p = nullptr;                // nullptr is a prvalue.
	bool b = true;                   // true is a prvalue.
```

- **possible** to assign a prvalue **to** an lvalue but **not vice versa**
```cpp
bool* b = &true;      // error: lvalue required as unary '&' operand
int* i = &3;          // error: lvalue required as unary '&' operand
int* j = &( 3 + 4 );  // error: lvalue required as unary '&' operand
```

- **result** of **an expression** using **built-in operators**
```cpp
(1 + 3);         // prvalue
(true || false); // prvalue
(true && false); // prvalue
```
> [!NOTE]
> It is possible to override the built-in operators to return non-prvalues, but that is not important in order to describe value categories.

-  move operations
```cpp
int MoveMe(int&& i) // MoveMe takes an rvalue reference.
{
    int j = i;
    return j;
}

int main()
{
    int i = 0;
    i = MoveMe(3); // prvalue (3) is used where an rvalue reference is expected.
    return 0;
}
```
<a id="xvalue "></a>
## XValue
-  named using an identifier **(i)**
- movable (**m**)
- function that returns an rvalue reference (such as `std::move`)
```cpp
int i = 0;              // i is an lvalue.
int&& j = std::move(i); // The result of std::move is an xvalue.
```
<a id="glvalue "></a>
## GLValue

-  “generalized lvalue”
-  either an lvalue or an xvalue
-  movable (**m**) lvalue
-  named by an identifier (**i**)
-  not a prvalue

```cpp
int i;       // i is a glvalue (lvalue)
int* p = &i; // p is a glvalue (lvalue)
int& f();    // the result of f() is a glvalue (lvalue)
int&& g();   // the result of g() is an glvalue (xvalue)
```
<a id="rvalue "></a>
## Rvalue
- a generalization of prvalues and xvalues
- can be moved (**m**)
- **not** named with an identifier (**I**)
```cpp
int i = 3;                       // 3 is an rvalue (prvalue).
std::string s = "Hello, world!"  // "Hello, world!" is an rvalue (prvalue)
int&& g();                       // The result of g() is an rvalue (xvalue)
```