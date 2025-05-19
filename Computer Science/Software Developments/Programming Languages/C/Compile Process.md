---
~
---
#Compiler #C #cpp 
```table-of-contents
option1: value1
option2: value2
```
<a id="introduction"></a>
# Introduction
![[compilationProcess.png]]
<a id="Definition"></a>
# Definition
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