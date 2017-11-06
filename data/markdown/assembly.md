# Quick Assembly Overview


## Assembly types
Intel

AT&T <!-- .element: class="fragment shrink"--> 

Notes: I will use Intel. AT&T Syntax used by GNU Assembler


## Names
* rax, rbx, rcx, rdx, rsp, rbp, rsi, rdi, r8-r15
* xmm1-xmm15
* rdi, rsi, rdx, rcx `=>` arguments
* rax `=>` return

Notes: http://www.swansontec.com/sregisters.html
* RAX - Accumulator 
* RBX - Base 
* RCX - Counter 
* RDX - Data 
* RSI - Source Index
* RDI - Destination Index
* RBP - Base Pointer
* RSP - Stack Pointer
* XMM - Streaming SIMD Ext (SSE) - performing same op on mult data objs


## Sizes
![RAX, EAX, AX, AH, AL](/data/images/register_size.png)
<small>From "What Has My Compiler Done for Me Lately", M. Godbolt</small>


## Operations
```
	op
	op dest
	op dest, src
	op dest, src1, src2
```
* dest, src is register or memory reference
* [base + reg1<sub>opt</sub> + reg2*(1, 2, 4, 8)<sub>opt</sub>]
