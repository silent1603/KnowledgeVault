---
~
---
#ExecutableFile #C #cpp 
```table-of-contents
option1: value1
option2: value2
```
<a id="introduction"></a>
# Background

<a id="Definition"></a>
# Definition
 Executable Formats:
- Old:
	- a.out (Assembler OUTput)
		- Oldest UNIX format 
		- No longer commonly used
	- COFF (Common Object File Format)
		- Older UNIX format
		- No longer commonly used
- Modern:
	- PE (Portable Executable)
		- Base on COFF
		- Used in 32-bit and 64-bit Windows
	- ELF (Executable and Linkable Format)
		- Linux/UNIX
	- Mach-O File
		- Mac

# Content of an Executable
1. exec header
2. text segment
3. data segment
4. text relocations
5. data relocations
6. symbol table
7. string table
**Here are the details for important fields of object files:**

**.text** :This section contains the executable instruction codes and is shared among every process running the same binary. This section usually has READ and EXECUTE permissions only. This section is the one most affected by optimization.

**.bss**: BSS stands for ‘Block Started by Symbol’. It holds un-initialized global and static variables. Since the BSS only holds variables that don't have any values yet, it doesn't actually need to store the image of these variables. The size that BSS will require at runtime is recorded in the object file, but the BSS (unlike the data section) doesn't take up any actual space in the object file.

**.data**: Contains the initialized global and static variables and their values. It is usually the largest part of the executable. It usually has READ/WRITE permissions.

**.reloc**: Stores the information required for relocating the image while loading.
### 1. Header
![[ExecutableHeader.png]]

### 2. symbol tables
![[symboltable_2.png]]

![[symboltable_1.png]]


### 3. Relocation Tables
![[relocationtable_2.png]]

![[relocationtable_1.png]]

# Process's Address Space 

![[ProcessAddressSpace.png]]