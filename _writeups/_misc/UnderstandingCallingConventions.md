[**< back**](/readme.md)

![UnderstandingCallingConventions](/_storage/_img/UnderstandingCallingConventions/calling_conventions_head.png)
# Understanding calling conventions...

This is just 101, not a deep calling conventions guide (might also be just a cheat sheat for others who just want to quickly recap calling conventions), dedicated to briefly summing up what calling conventions are, which are the most common in malware analysts/reverse engineers life and how to recognize them at ease, I am not going to thoroughly describe every one of them since documentation for each of them is available on the internet and can be digested easily. 

Different conventions exist for different environments, arch flavors, etc. The main focus for this paper is microsoft implementations since those are the ones used in malware commonly.


## Why calling conventions?!... :question:

So... A calling convention is nothing but a set of rules which describe how a function should be called, how arguments/parameters are passed to the function (which is either via **Registers** **and/or** **Stack**), also who handles memory procedures after the control returns to the caller.

Lets recap as bullets...  
- A calling convention describes set of rules which must be followed when calling a function  
- A convention describes how data is passed into and out of functions  
- Arguments are passed to functions either via **Registers** **and/or** **Stack**
- In every convention either a callee or a caller cleans up the stack  
- Convention implementations vary by compilers (compiler might change the convention type if compiler optimizations are enabled)

Different calling conventions implementations are available, every other compiler has it's own kind of implementation, also conventions might change based on compiler optimizations if possible (lets say it can not be changed when variadic functions are used, since the caller is always responsible for handling memory, etc.). A calling convention has it's prologue and epilogue, which are implemented by compiler and basically is the job of pushing parameters on to the stack or registers and freeing up the space after usage, thus a programmer does not have to manually implement any of it, he/she can just declare a function (which automatically will be assigned a convention) and let the compiler decide what calling convention should be used or explicitly put a convention type before function name in function definition, suggesting the compiler it should use that exact convention (although it might change according to compiler optimizations if enabled).
  
In this paper we are only going to discuss commonly used calling conventions since several of them exist and have different purposes, also one might use a custom calling convention to obfuscated the flow and make analysts job harder, by obfuscating how parameters are passed and how memory is handled (which is commonly correctly interpreted by IDA but sometimes requires custom embellishment to fully uncover the functionality and characteristics).

### CDECL
CDECL is a short term for C declaration, which is default calling convention for C/C++ (you can just simply put `__cdecl` to explicitely before function definition), in this calling convention arguments are pushed on the stack from right to left, in a reverse order, meaning that the first parameter is actually pushed on the stack last and the last one is pushed first. By default the return value is generally stored in `EAX` and the caller cleans up the stack (prologue). There are several use cases of cdecl convention, lets say when using variadic functions, since the callee does not have information about exact number of parameters, but the caller does.

So... :jigsaw:
- Arguments are pushed on the stack from right to left (in a REVERSE order), it is only true only for x86 only. For x64 first four integer or pointer values are passed via `RCX`,`RDX`,`R8` and `R9` registers and rest of the parameters ar pushed on stack (RTL)
- Caller cleans up the stack
- Return value is stored in `EAX` if it is an integer or a memory address, floating point values are put in ST0 x87 registers.
- keyword: `__cdecl`

So... Lets take a simple example of CDECL in x86 environment first, analyze what's in there and how it works.
Lets take a simple code in C/C++, I am using Microsoft Visual Studio Code, which compiler optimizations turned off to be able to strictly put a convention in the code and make sure it is compiled with this exact convention.

```
#include <stdio.h>

int __cdecl sq(int a1,int a2, int a3, int a4, int a5) {
    return a1 * a2 * a3 * a4 * a5;
}

int main()
{
    printf("%d", sq(1,2,3,4,5));
}
```

Once compiled and opened up in IDA we can notice couple of things...
![FirstGlanceInIda](/_storage/_img/UnderstandingCallingConventions/cdecl_first_prog_ida_open_1.png)  
Parameters are pushed on to the stack using RTL and the caller cleans up the stack in this case  

![CDECL_Example](/_storage/_img/UnderstandingCallingConventions/cdecl_first_prog_ida_parameters_and_cleanup.png)  
`add esp,14h` is exactly what does the stack cleanup, it adjusts stack by 20 bytes which is exactly five 4-BYTE arguments which were passed to the function.

We can take a look at the function sq in IDA assembly view.  

![SQ_Function_Glance](/_storage/_img/UnderstandingCallingConventions/cdecl_first_prog_sq_func_glance.png)

There we can see `prologue` and `epilogue` of the function, also how parameters are parsed in this function.

**prologue**
```
push ebp
move ebp, esp
```

**epilogue**
```
mov esp, ebp
pop ebp
ret
```




### STDCALL
STDCALL convention is a short term for Standard Call, it is a default calling convention for 32-bit WinAPI functions. This convention also uses RTL(Right to Left) stack procedure, arguments are pushed in a reverse order here too, pushing them from right to left, again meaning that the first element pushed is the last argument. It is similar to CDECL, but the difference is that in STDCALL stack is cleaned by callee, return value is by default returned in `EAX`, you can specify in C/C++ to specifically use this convention by putting `__stdcall` before a function name in function definition. 

- `STDCALL` is similar to `CDECL`, but the callee cleans up the stack.
- `STDCALL` is used in `Win32API`/`Windows 32 Api` (this is a default calling convention for `Win32API`)
- Arguments are passed in RTL manner, from right to left on stack 
- The stack is cleaned up by the callee
- Return value is passed through the `EAX` register (Hint: Which means Win32API functions return result in EAX)
- keyword: `__stdcall`

### FastCall
Fastcall serves a purpose of speading up function calls, first two arguments that require 32-bit or less are placed into `ECX` and `EDX`, rest of them are pushed to the stack in RTL manner, The stack is cleaned up by a callee function and return values are stored in `EAX` by default, this convention can be explicitly pointed to compiler by keyword `__fastcall`

- Arguments are first placed in registers rather than the stack
- The first two arguments get stored in `EAX` and `EDX`, rest of them are pushed on stack from right to left
- Callee cleans up the stack
- keyword: `__fastcall`

### ThisCall
ThisCall is a convention which is used by C++ (member functions), this convention intends to preserve a reference to this pointer `this->`, which is stored in `ECX`, other arguments are pushed on the stack in RTL manner, similarly to `CDECL` the caller is responsible for cleanup(stack adjustment to remove the arguments pushed onto the stack) and the return vallue is stored in `EAX` by default, you don't have to manually put __thiscall anywhere, it is automatically assigned to member functions when compiling C++ code.

- Used in C++ (Class member functions)
- Convention stores `this` pointer value on `ECX` and pushes other parameters/arguments on stack from right to left
- The caller is responsible for the cleanup procedure/stack adjustment
- For GNU compilers, the `this` pointer is pushed on the the stack last


### UserCall
UserCall is a users defined calling convention type, any user can create his/her own calling convention, define set of rules how parameters are passed and how memory is handled by the convention, it is widely used in malware for making analysts job a bit harder to not analyze function calls at ease and make it hardly digestable for an untrained eye, although some decompilers like IDAs decompiler does us a favor and recognizes such convention characteristics.

### Other Calling Conventions
There are some other calling conventions like __vectorcall which is out of this papers scope for now, you can just search online and find documentations about every other calling convention which you are interested in. 

Sum up of calling conventions from WikiPedia for x86arch, you can read more at [wiki](https://en.wikipedia.org/wiki/X86_calling_conventions)
![x86chart](/_storage/_img/UnderstandingCallingConventions/wikipedia_x86_conventions_chart.png)
