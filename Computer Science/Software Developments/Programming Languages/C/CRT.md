---
~
---
```table-of-contents
option1: value1
option2: value2
```
<a id="introduction"></a>
# Background
When writing a simple C program like:
```cpp
#include <stdio.h>
int main(void) {  
    printf("Hello, world!\n");  
    return 0;  
}
```

compile it (e.g., `gcc hello.c -o hello`)

you might assume that your `main()` function is the first piece of code to execute once the program starts. However, there are actually a few special pieces of code that run _before_ and _after_ `main()`, preparing the environment for your program. 

These pieces of code are part of the C runtime (CRT) startup objects. Among them, you may have encountered file names like `**crt0.o**`, `**crt1.o**`, `**crti.o**`, and `**crtn.o**`. 

This note will discuss what each one does, why they exist, and how they work together to ensure your C (and C++) programs run smoothly.
<a id="Definition"></a>
# What is the C Runtime ?
<font color="#2DC26B">The C runtime (CRT)</font> is a collection of <font color="#2DC26B">startup</font> routines, <font color="#2DC26B">initialization</font> code, <font color="#2DC26B">standard library </font>support, and sometimes system call wrappers that form the environment in which a C program executes. Most of this code lives _outside_ your application’s own source but is automatically linked in by the compiler driver (e.g., `gcc` or `clang`).

When you compile a program with a command such as:
```bash
gcc main.c -o main
```

or
```shell
clang main.c -o main
```

the compiler driver and linker _implicitly_ include startup object files and libraries, including one or more CRT object files. These files contain assembly-level entry points and routines that:

1. Initialize registers and the stack.
2. Set up the program arguments (`argc`, `argv`, `envp`).
3. Invoke global constructors (in C++ programs).
4. Call your `main()` function.
5. Handle the return from `main()` and pass the exit status to the operating system.


# crt0.o or crt1.o in Modern Toolchains
Historically, `crt0.o` (C runtime zero) is a small object file containing the actual entry point routine, often named `_start`. Its responsibilities include:

1. **Program Initialization**

- Initializing the stack (on some architectures and OSes, though typically the kernel arranges the stack pointer).
- Setting up memory segments if necessary (e.g., data, BSS).
- Preparing `argc`, `argv`, and environment pointers from the kernel-provided data.
- Invoking constructors for global and static objects (especially in C++).
- Possibly calling library initialization functions (for the standard I/O library, etc.).

1. **Transferring Control to** `main()`

- After the environment is set up, `crt0.o` calls `main(argc, argv, envp)`.

1. **Cleaning Up**

- When `main()` returns, `crt0.o` (or the final exit routine) calls the OS-specific exit syscall (like `_exit` or similar) to terminate the process with the return code from `main()`.

Because `crt0.o` was often a large, monolithic file, many modern toolchains now split it up into more modular components. You might see `**crt1.o**` being used instead of `crt0.o`. The name `crt1.o` typically indicates it’s the “first” (or primary) startup object. Despite the naming differences, they serve the same core purpose: they contain the `_start` symbol, which is the default entry point used by the linker.

## Typical Content of `crt0.o` / `crt1.o`

- Low-level assembly code responsible for setting up the runtime.
- A symbol named `_start` (or sometimes `__start`) that acts as the entry point.
- A call to `main()` (or `_main`, depending on the convention).

## Linking Phase

When you link your program, the linker automatically pulls in `crt0.o` (or `crt1.o`) from the C library implementation (e.g., glibc or musl) or from the compiler toolchain. This happens behind the scenes unless you explicitly disable it (e.g., with certain compiler flags like `-nostartfiles`).

#  `crti.o`: C Runtime Initialization

`crti.o` typically contains the _prologue_ for the runtime initialization procedure. Its primary tasks include:

- **Platform-Specific Setup**  
    For instance, initializing special registers, CPU features, or other architecture-specific resources.
- **Environment Preparation**  
    It lays the groundwork needed to call the constructors (`.ctors` section for C++).
- **Hooks for Early Setup**  
    These can be initialization routines required by the OS or the platform, such as thread-local storage (TLS) setup on some systems.

Conceptually, you can think of `crti.o` as the place where the runtime says, “I am starting up the environment; here’s some prologue code.” Once done, control eventually proceeds to `main()` or other initial routines

# `crtn.o`: C Runtime Termination
`crtn.o` contains the _epilogue_ of the runtime initialization process and handles finalization routines. It:

- **Finalizes the Initialization Sequence**  
    Completes what `crti.o` started, ensuring all global constructors have been called.
- **Manages Destructors**  
    For C++ programs, global destructors (`.dtors`) must be invoked at the end of the program. By wrapping the prologue and epilogue around these sections, `crti.o` and `crtn.o` manage that logic properly.

When the program finishes, the destructors of global objects are called, ensuring resources are cleaned up before the program truly exits.

# Putting It All Together

To visualize how these files fit into the program startup flow, here is a simplified diagram:

        ┌─────────────────────┐  
        │ Program Entry Point │  (Defined in crt1.o or crt0.o)  
        │     _start()        │  
        └──────────┬──────────┘  
                   │  
                   │ (1) Initialize environment, memory, etc.  
                   │  
        ┌──────────┴──────────┐  
        │   crti.o (Prologue) │   
        │  Calls constructors │  
        └──────────┬──────────┘  
                   │  
                   │ (2) Jump to main()  
                   │  
        ┌──────────┴──────────┐  
        │        main()       │  
        └──────────┬──────────┘  
                   │  
                   │ (3) main returns  
                   │  
        ┌──────────┴──────────┐  
        │   crtn.o (Epilogue) │  
        │  Calls destructors  │  
        └──────────┬──────────┘  
                   │  
                   │ (4) exit syscall  
                   │  
             ┌─────┴──────┐  
             │   OS Exit  │
            └────────────┘

**Key Steps**:

1. `_start` (from `crt1.o` or `crt0.o`) does low-level setup, then calls the prologue code from `crti.o`.
2. Initialization code from `crti.o` finishes, then we jump into `main()`.
3. When `main()` returns, the epilogue from `crtn.o` is executed, triggering finalizers and destructors.
4. A final exit syscall terminates the process with the return value from `main()`.