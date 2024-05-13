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
- A calling convention has prologue and epilogue

Different calling conventions implementations are available, every other compiler has it's own kind of implementation, also conventions might change based of compiler optimizations if possible (lets say it can not be changed when variadic functions are used, since the caller is always responsible for handling memory, etc.). A calling convention has it's prologue and epilogue, which are implemented by compiler and basically is the job of pushing parameters on to the stack or registers and freeing up the space after usage, thus a programmer does not have to manually implement any of it, he/she can just declare a function (which automatically will be assigned a convention) and let the compiler decide what calling convention should be used or explicitly put a convention type before function, suggesting the compiler it should use that exact convention (although it might change according to compiler optimizations).
  
In this paper we are only going to discuss commonly used calling conventions since several of them exist and have different purposes, also one might use a custom calling convention to obfuscated the flow and make analysts job harder, by obfuscating how parameters are passed and how memory is handled (which is commonly correctly interpreted by IDA but sometimes requires custom embellishment to fully uncover the functionality and characteristics).

### CDECL
### STDCALL
### FastCall
### UserCall
### Other Calling Conventions

Imma finnish this paper soon...