#ComputerScience #TemplateProgramming

- [[#[[Value Categories]]|[[Value Categories]]]]
- [[#Terminology|Terminology]]
	- [[#Terminology#Template Arguments|Template Arguments]]
	- [[#Terminology#Template Parameters|Template Parameters]]
	- [[#Terminology#Type Template Parameter|Type Template Parameter]]
	- [[#Terminology#Non-type Template Parameter|Non-type Template Parameter]]
	- [[#Terminology#Template Template Parameter|Template Template Parameter]]
	- [[#Terminology#Template Parameter Pack|Template Parameter Pack]]
- [[#Basic|Basic]]

# Introduction

## [[Value Categories]]
- lvalue
- prvalue
- xvalue
- glvalue
- rvalue

## Terminology
### Template Arguments
```cpp 
template<typename T, size_t N>
class Array
{
public:
    Array()
        : m_Data{}
    {}

    size_t size() const
    {
        return N;
    }

    T& operator[](size_t i)
    {
        assert(i < N);

        return m_Data[i];
    }

    const T& operator[](size_t i) const
    {
        assert(i < N);

        return m_Data[i];
    }

private:
    T m_Data[N];
};
```
the template parameters are `T` and `N`
### Template Parameters

This is instantiation of the template array class
```cpp
Array<float, 3> Position;
Array<float, 3> Normal;
Array<float, 2> TexCoord; // this is a template-id.
```
The _type_ template argument is `float` and the _non-type_ template arguments are `3`, and `2`.
we can say that “_parameters_ are initialized by _arguments_“.

The definition of a template with its arguments is called the _template-id_.
Examples **`Array<float, 3>`** is the _template instantiation ID.

We can check if two variables have the same type at compile time, we could write
```cpp
static_assert(std::is_same_v<decltype(Position), decltype(Normal)>, "Types are not the same!");

```
This will **compile successfully** — because they are indeed the same type.

Value of non-type template arguments must be determined at compile-time

Each parameter in a template parameter list can be one of following types:

- A type template parameter
- A non-type template parameter
- A template template parameter

The `Array` class template demonstrates the use of both type (`T`), and non-type (`N`) template parameters

###  Type Template Parameter
 - The most **common** template parameter
 - starts with either `typename` or `class`
 - (optionally) followed by the parameter name. The name of the parameter is used as an alias of the type within the template and has the same naming rules as an identifier used in a [typedef](https://en.cppreference.com/w/cpp/language/typedef) or a [type alias](https://en.cppreference.com/w/cpp/language/type_alias).
- A type template parameter may also define a **default argument**. If a type template parameter defines a default value, it must **appear** at **the end of the parameter list**.
- A type template parameter can also be a _parameter pack_. A parameter pack starts with `typename...` (or `class...`) and is used to list an unbounded number of template parameters.

The following example demonstrates the use of type template parameters.
```cpp
// typename introduces a type template parameter.
template<typename T> class Array { ... }; 

// class also introduces a type template parameter.
template<class T> struct MyStruct { ... }; 

// A type template parameter with a default argument.
template<typename T = void> struct RType { ... };

// The parameter name is optional.
template<typename> struct Tag { ... };
```

### Non-type Template Parameter
- Used to specify a _value_ rather than a _type_ in the template parameter list.
- It can be :
	- An integral type (`bool`, `char`, `int`, `size_t`, and unsigned variants of those types)
	- An enumeration type
	- An lvalue reference type (a references to an existing object or function)
	- A pointer type (a pointer to an existing object or function)
	- A pointer to a member type (a pointer to a member object or a member function of a class)
	- [std::nullptr_t](https://en.cppreference.com/w/cpp/types/nullptr_t)
-  The name of the template parameter is optional and non-type template parameters may also define default values.
- Non-type template parameters must be **constant values** that are evaluated at **compile-time**.

The following shows examples of non-type template parameters:
```cpp
// Integral non-type template parameter.
template<int C> struct Integral {};

// Also an Integral non-type template parameter.
template<bool B> struct Boolean {};

// Enum non-type template parameter
template<MyEnum E> struct Enumeration {};

// lvalue reference can also be used as a non-type template parameter.
template<const int& C> struct NumRef {};

// lvalue reference to an object is also allowed.
template<const std::string& S> struct StrRef {};

// Pointer to function
template<void(*Func)()> struct Functor {};

// Pointer to member object or member function.
template<typename T, void(T::*Func)()> struct MemberFunc {};

// std::nullptr_t is also allowed as a non-type template parameter.
template<std::nullptr_t = nullptr> struct NullPointer {};

// Floating-point types are allowed in C++20
template<double N> struct FloatingPoint {};

// Literal class types are allowed in C++20.
template<MyClass C> struct Class {};
```

### Template Template Parameter
-  used as template parameters:
```cpp
// C is a template class that takes a single type template parameter.
template<template<typename T> class C> struct TemplateClass {};

// C is a template class that takes a type and non-type template parameter.
template<template<typename T, size_t N> class C> struct ArrayClass {};

// keyword typename is allowed in C++17.
template<template<typename T> typename C> struct TemplateTemplateClass {};
```

> [!note] 
> Note that until C++17, unlike type template parameters, template template parameters can only use the keyword `class` and not `typename`.

### Template Parameter Pack
- A placeholder for zero or more template parameters
- Can be applied to type, non-type, and template template parameters
- The parameter types cannot be mixed in a single parameter pack.

A few examples of template parameter packs:
```cpp
// Function template with a parmeter pack.
template<typename... Args> void func(Args... args);

// Type template parameter pack.
template<typename... Args> struct TypeList {};

// Non-type template parameter pack.
template<size_t... Ns> struct IntegralList {};

// Template template parameter pack.
template<template<typename T> class... Ts> struct TemplateList {};
```

- Followed by an ellipsis expands the pack
- Expanded by replacing the pack with a comma-separated list of the template arguments in the pack.

For example, if a function template is defined with a parameter pack:
```cpp
template<typename... Args>
void func(Args... args)
{
    std::tuple<Args...> values(args...);
}
```

And invoking the function:

```cpp
func(4, 3.0, 5.0f);
```

Will result in the following expansion:

```cpp
void func(int arg1, double arg2, float arg3)
{
    std::tuple<int, double, float> values(arg1, arg2, arg3);
}
```


## Basic
