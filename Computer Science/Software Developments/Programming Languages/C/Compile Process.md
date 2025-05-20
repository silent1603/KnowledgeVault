---
~
---
Compiler #C #cpp 
```table-of-contents
option1: value1
option2: value2
```
<a id="introduction"></a>
# Background
<<<<<<< Updated upstream
There are 4 steps in compilation process:
1. Preprocessing 
2. Compiling
3. Assembling
4. Linking
=======
![[compilationProcess.png]]
>>>>>>> Stashed changes
<a id="Definition"></a>
# Overview
Here are the stages which are involved in C code building process in order regardless of the operating system/compiler.
## 1. Pre processing
![[Preprocessing.png]]
It processes:
	- include-files
	- conditional compilation instructions
	- macros
## 2.Compilation
![[Compilation.png]]
 It takes the output of the pre processor, and the source code, and generates **assembler source code** asm file.
 If there is syntactical errors in source code than such errors are called compilation error.
## 3. Assembly
![[Assembler.png]]
 It takes the assembly source code and produces an assembly listing with offsets. Assembler helps convert the assembly file into an object file containing machine-leveling code 
 The assembler output is stored in an object file. In this process **.obj** made from **.asm.**
## 4. Linking
![[linking.png]]
It takes one or more object files or libraries as input and combines them to produce a single (usually executable) file. In this process .exe made from .obj.

# Modern Compiler!
![[modernCompiler.png]]

## Frontend Analysis
- Lexical analysis (scanning): Sources -> Tokens
![[lexical.png]]
- Syntactic analysis (parsing): Tokens -> Syntax tree
![[syntactic.png]]  

 - Semantic analysis (mainly , type checking)
![[semantic.png]]


## Intermediate Representation (IR)
- Internal compiler language that is : 
	- language-independent
	- Machine-independent
	- Easy to optimize

![[AnatomyOfAModernCompiler.png]]

- Why yet another language ? 
	- assembly does not have enough info to optimize it well
	- enables modularity and reuse

![[IR_2.png]]

## Common IR: Control Flow Graph 
![[CommonIR.png]]


A common IR is to reorganize the syntax tree into what’s called a **control flow graph (CFG)**. Each node in the graph is a sequence of assignment and expression evaluations that ends with a branch. The nodes are called “basic blocks” and represent sequences of operations that are executed as a unit: once the first operation in a basic block is performed, the remaining operations will also be performed without any other intervening operations.

## IR Optimization

![[IROptimization.png]]

Example:

![[exampleIROptimizations_1.png]]

![[exampleIROptimizations_2.png]]

![[exampleIROptimizations_3.png]]

![[exampleIROptimizations_4.png]]

![[exampleIROptimizations_5.png]]

![[exampleIROptimizations_6.png]]

## Code Generation

![[codegeneration.png]]

Here’s how the code generator will process the optimized CFG.
First, it dedicates registers to hold the values for x and y.
Then, it emits the code for each of the basic blocks.
Next, reorganize the order of the basic blocks to eliminate unconditional branches wherever possible.

![[GCD.png]]

## Summary 

![[summary.png]]