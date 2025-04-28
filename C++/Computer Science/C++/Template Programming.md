#ComputerScience #TemplateProgramming
***Table of content:***
- [Introduction](#introduction)
- [Required](#required)

<a id="introduction" ></a>
# Introduction
<a id="required" ></a>
# Required
- [[Value Categories]]

# Template Arguments
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
# Template Parameters

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

##  Type Template Parameter