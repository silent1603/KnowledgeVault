#Allocator #cpp 
```table-of-contents
``````table-of-contents
option1: value1
option2: value2
```
<a id="introduction"></a>
# Background
In cpp, the Allocators are object responsible for encapsulating memory management. std::allocator is used when you want to separate allocation and do construction in two steps. It is else used when separate destruction and deallocation is done in two steps. 

All the STL containers in C++ have a type parameter Allocator that is by default std::allocator.
The default allocator simply uses the operator new and delete to obtain and release memory
<a id="Definition"></a>
# Overview of Allocators

Allocators are component of the C++ Standard Library
Allocators provide a common interface to memory allocation strategies
 - delegate to C++ runtime: new/delete
 - pool of blocks
 - pool of blocks with different sizes

## Why Use Custom Allocators?

Custom allocators exist for several reasons:

1. **Performance Optimization**: By tailoring memory allocation to specific patterns, you can reduce fragmentation and improve cache locality.
2. **Memory Pooling**: Allocators can manage a pool of memory for quick allocation and deallocation, which is useful in real-time systems.
3. **Debugging and Profiling**: Custom allocators can help track memory usage and detect leaks or corruption.
4. **Specialized Hardware**: Allocators can be designed to interact with non-standard memory, such as GPU memory or shared memory regions.

## When to Consider Custom Allocators in C++

Custom allocators can be beneficial in various scenarios:

1. **High-Performance Applications**: Applications requiring low-latency and high-throughput can benefit from custom allocators designed for specific access patterns.
2. **Embedded Systems**: Systems with limited memory resources can use allocators to optimize memory usage.
3. **Gaming**: Games often use custom memory allocators to manage resources efficiently.
4. **Real-Time Systems**: Systems with real-time constraints can use memory pools to ensure deterministic memory allocation times.

## Implementing Custom Allocators in C++

When implementing custom allocators, consider the following steps:

1. **Define the Allocator Class**: Implement the allocator by defining its member types and methods.
2. **Handle Memory Allocation and Deallocation**: Customize the `allocate` and `deallocate` methods to manage memory as needed.
3. **Integrate with Data Structures**: Use the custom allocator with standard library data structures like `std::vector`.
4. **Test for Performance and Correctness**: Ensure your allocator improves performance without introducing bugs.

Declaration:
```cpp
template <class T> class allocator;
```


The essential members of `std::allocator<T>` :

```cpp
// Attributes
value_type                               T
pointer                                  T*
const_pointer                            const T*
reference                                T&
const_reference                          const T&
size_type                                std::size_t 
difference_type                          std::ptrdiff_t
propagate_on_container_move_assignment   std::true_ty
rebind                                   template< class U > struct rebind { typedef allocator<U> other; };
is_always_equal                          std::true_type

// Methods
constructor
destructor
address
allocate
deallocate
max_size
construct
destroy

// Functions
operator==
operator!=
```


The standards then require allocator have
- types of pointer to `T`(`pointer`)
- pointer to constant `T`(`const_pointer`),
- reference to `T`(`reference`)
- reference to constant `T`
- type of `T` itself (`value_type`)
- an unsigned integral type that can represent the size of the largest object in the allocation model (`size_type`)
- a signed integral type that can represent the difference between any two pointers in the allocation model (`difference_type`).
- a template class rebind member Allows your allocator to work with different types ( e.g  nodes in `std::list<T>` )

```cpp
A* a = new A;
delete a;
```

is actually interpreted by the compiler as

```cpp
// assuming new throws std::bad_alloc upon failure
A* a = ::operator new(sizeof(A)); 
a->A::A(); //cóntructor
if ( a != 0 ) {  // a check is necessary for delete
    a->~A(); //destructor
    ::operator delete(a);
}
```

in C++11, When creating a minimal allocator, do not implement any members except the ones shown in the example below:
1. a converting copy constructor
2. `operator==`
3. `operator!=`
4. `allocate`
5. `deallocate`

Example: Minimal Allocator

```cpp
template<typename T>
struct MyAllocator {
    using value_type = T;

    MyAllocator() = default;

    template<typename U>
    MyAllocator(const MyAllocator<U>&) {}

    T* allocate(std::size_t n) {
        return static_cast<T*>(::operator new(n * sizeof(T)));
    }

    void deallocate(T* p, std::size_t) {
        ::operator delete(p);
    }
    bool operator==(const MyAllocator&) const { return true; }
    bool operator!=(const MyAllocator&) const { return false; }
};

```

Below is a definition as well as implementation of an allocator that conforms to the C++ standards. 

```cpp
template<typename T>
class Allocator {
public : 
    //    typedefs
    typedef T value_type;
    typedef value_type* pointer;
    typedef const value_type* const_pointer;
    typedef value_type& reference;
    typedef const value_type& const_reference;
    typedef std::size_t size_type;
    typedef std::ptrdiff_t difference_type;

public : 
    //    convert an allocator<T> to allocator<U>
    template<typename U>
    struct rebind {
        typedef Allocator<U> other;
    };

public : 
    inline explicit Allocator() {}
    inline ~Allocator() {}
    inline explicit Allocator(Allocator const&) {}
    template<typename U>
    inline explicit Allocator(Allocator<U> const&) {}

    //    address
    inline pointer address(reference r) { return &r; }
    inline const_pointer address(const_reference r) { return &r; }

    //    memory allocation
    inline pointer allocate(size_type cnt, 
       typename std::allocator<void>::const_pointer = 0) { 
      return reinterpret_cast<pointer>(::operator new(cnt * sizeof (T))); 
    }
    inline void deallocate(pointer p, size_type) { 
        ::operator delete(p); 
    }

    //    size
    inline size_type max_size() const { 
        return std::numeric_limits<size_type>::max() / sizeof(T);
 }

    //    construction/destruction
    inline void construct(pointer p, const T& t) { new(p) T(t); }
    inline void destroy(pointer p) { p->~T(); }

    inline bool operator==(Allocator const&) { return true; }
    inline bool operator!=(Allocator const& a) { return !operator==(a); }
};    //    end of class Allocator 
```

Allocators deal with **two separate concerns**:

1. **Memory management**: How memory is allocated and deallocated.
    
2. **Object lifetime management**: How objects are constructed and destroyed, and how their addresses are taken.


To better manage these concerns and improve flexibility, you:

- Use **Allocation Policies** (like `StandardAllocPolicy`, `TrackAllocPolicy`, `SmallObjectAllocPolicy`) to manage memory.
    
- Use **Object Traits** (`ObjectTraits`) to manage construction, destruction, and address resolution.

### Allocation Policies
Each policy defines:
- How memory is **allocated/deallocated**
- The maximum allocatable size
- Equality comparisons for allocator interop

####  StandardAllocPolicy

Uses raw `operator new`/`delete`, mimics `std::allocator`.

#### TrackAllocPolicy
Tracks:
- Total allocations
- Current allocations
- Peak allocations

Useful for **profiling memory usage**.
####  SmallObjectAllocPolicy

Optimized for small, frequent allocations using `Loki::SmallObjAllocator`.
- It hands out memory from preallocated blocks.
- Ideal for containers like `std::list`, not `std::vector` (due to bulk allocation).
- **Note**: Not thread-safe due to shared static pool.


### ObjectTraits
ObjectTraits lets the user specialize behavior for constructing, destroying, or getting the address of objects. Why?

- If a class overloads `operator&`, `&obj` won’t give the real address. So `ObjectTraits::address(obj)` ensures you get the actual address.
    
- Traits can be specialized per type `T`.
```cpp
template<typename T>
class ObjectTraits {
public:
    T* address(T& r) { return &r; }
    void construct(T* p, const T& t) { new(p) T(t); }
    void destroy(T* p) { p->~T(); }
};

```

This keeps allocator logic clean and extensible.

## Final Allocator Class

```cpp
std::vector<int, Allocator<int, TrackAllocPolicy<int>>> v;

```
or
```cpp
std::list<MyType, Allocator<MyType, SmallObjectAllocPolicy<MyType>>> l;
```
Which is powerful because:
- You can **mix and match** memory allocation policies and object construction traits.
- You can extend functionality (e.g., memory debugging, pooling, alignment).

Example

```cpp
#include <iostream>
#include <vector>
#include <limits>
#include <new>      // For ::operator new
#include <cstddef>  // For std::size_t, std::ptrdiff_t

// === ObjectTraits ===
template<typename T>
class ObjectTraits {
public:
    template<typename U>
    struct rebind { typedef ObjectTraits<U> other; };

    T* address(T& r) const { return &r; }
    const T* address(const T& r) const { return &r; }

    void construct(T* p, const T& value) const { new(p) T(value); }
    void destroy(T* p) const { p->~T(); }
};

// === Standard Allocation Policy ===
template<typename T>
class StandardAllocPolicy {
public:
    typedef T value_type;
    typedef T* pointer;
    typedef std::size_t size_type;
    typedef std::ptrdiff_t difference_type;

    template<typename U>
    struct rebind { typedef StandardAllocPolicy<U> other; };

    pointer allocate(size_type n, const void* = 0) {
        return reinterpret_cast<pointer>(::operator new(n * sizeof(T)));
    }

    void deallocate(pointer p, size_type) {
        ::operator delete(p);
    }

    size_type max_size() const {
        return std::numeric_limits<size_type>::max() / sizeof(T);
    }
};

// === Tracking Allocation Policy ===
template<typename T, typename BasePolicy = StandardAllocPolicy<T>>
class TrackAllocPolicy : public BasePolicy {
public:
    typedef typename BasePolicy::pointer pointer;
    typedef typename BasePolicy::size_type size_type;

    template<typename U>
    struct rebind {
        typedef TrackAllocPolicy<U, typename BasePolicy::template rebind<U>::other> other;
    };

    TrackAllocPolicy() : total_(0), current_(0), peak_(0) {}

    pointer allocate(size_type n, const void* hint = 0) {
        pointer p = BasePolicy::allocate(n, hint);
        total_ += n;
        current_ += n;
        if (current_ > peak_) peak_ = current_;
        std::cout << "[Allocate] count = " << n << ", total = " << total_ 
                  << ", current = " << current_ << ", peak = " << peak_ << "\n";
        return p;
    }

    void deallocate(pointer p, size_type n) {
        BasePolicy::deallocate(p, n);
        current_ -= n;
        std::cout << "[Deallocate] count = " << n << ", current = " << current_ << "\n";
    }

private:
    size_type total_, current_, peak_;
};

// === Allocator<T, Policy, Traits> ===
template<
    typename T, 
    typename Policy = StandardAllocPolicy<T>, 
    typename Traits = ObjectTraits<T>
>
class Allocator : public Policy, public Traits {
public:
    typedef typename Policy::value_type value_type;
    typedef typename Policy::pointer pointer;
    typedef typename Policy::size_type size_type;

    template<typename U>
    struct rebind {
        typedef Allocator<U, typename Policy::template rebind<U>::other, 
                                typename Traits::template rebind<U>::other> other;
    };

    Allocator() {}
    template<typename U>
    Allocator(const Allocator<U, typename Policy::template rebind<U>::other,
                                    typename Traits::template rebind<U>::other>&) {}
};

// === Usage Example ===
int main() {
    std::vector<int, Allocator<int, TrackAllocPolicy<int>>> v;

    for (int i = 0; i < 10; ++i)
        v.push_back(i);

    v.clear();
    v.shrink_to_fit(); // triggers deallocation
    return 0;
}

```