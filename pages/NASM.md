- # Structure of a NASM Program
- directive
- section
- label
- instruction
	- 명령어
- operand
	- 피연산자
- # Your First Few Instructions
- mov
- and
- or
- xor
- add
- sub
- inc
- dec
- syscall n
	- Invoke operating system routine n
- db
	- A pseudo-instruction that declares bytes that will be in memory when the program runs
- # The Three Kinds of Operands
- register operands
	- integer registers; 64 bits wide
		- R0
			- RAX
			- values are returned from functions in this register
		- R1
			- RCX
			- typical scratch register
			- some instructions also use it as a counter
		- R2
			- RDX
			- scratch register
		- R3
			- RBX
			- preserved register
			- don't use it without saving it
		- R4
			- RSP
			- the stack pointer
		- R5
			- RBP
			- preserved register
			- sometimes used to store the old value of the stack pointer, or the base
		- R6
			- RSI
			- scratch register used to pass function argument 2 in 64-bit linux
			- in 64-bit windows, a preserved register
		- R7
			- RDI
			- scratch register and function argument 2 in 64-bit linux
			- in 64-bit windows, a preserved register
		- R8-R15
		- R0D-R15D; lowest 32-bits
			- EAX-EDI
		- R0W-R15W; lowest 16-bits
			- AX-DI
		- R0B-R15B; lowest 8-bits
			- AL-DIL
		- AH-BH; second lowest 8-bits
	- flag register
	- xmm registers; 128 bits wide
		- XMM0-XMM15
- memory operands
	- ```asm
	  [750]                  ; displacement only
	  [rbp]                  ; base register only
	  [rcx + rsi*4]          ; base + index * scale
	  [rbp + rdx]            ; scale is 1
	  [rbx - 8]              ; displacement is -8
	  [rax + rdi*8 + 500]    ; all four components
	  [rbx + counter]        ; uses the address of the variable 'counter' as the displacement
	  ```
- immediate operands
	- ```asm
	  200          ; decimal
	  0200         ; still decimal - the leading 0 does not make it octal
	  0200d        ; explicitly decimal - d suffix
	  0d200        ; also decimal - 0d prefex
	  0c8h         ; hex - h suffix, but leading 0 is required because c8h looks like a var
	  0xc8         ; hex - the classic 0x prefix
	  0hc8         ; hex - for some reason NASM likes 0h
	  310q         ; octal - q suffix
	  0q310        ; octal - 0q prefix
	  11001000b    ; binary - b suffix
	  0b1100_1000  ; binary - 0b prefix, and by the way, underscores are allowed
	  ```
- # Instructions with two memory operands are extremely rare
- add reg, reg
- add reg, mem
- add reg, imm
- add mem, reg
- add mem, imm
- # Understanding Calling Conventions
- From left to right, pass as many parameters as will fit in registers. The order in which
  registers are allocated, are:
	- For integers and pointers, `rdi`, `rsi`, `rdx`,
	        `rcx`, `r8`, `r9`.
	- For floating-point (float, double), `xmm0`, `xmm1`, `xmm2`,
	        `xmm3`, `xmm4`, `xmm5`, `xmm6`, `xmm7`.
- Additional parameters are pushed on the stack, right to left, and are to be *removed by the caller* after the call.
- After the parameters are pushed, the call instruction is made, so when the called function gets control, the return address is at `[rsp]`, the first memory parameter is at `[rsp+8]`, etc.
- **The stack pointer `rsp` must be aligned to a 16-byte boundary before making a call**. Fine, but the process of making a call pushes the return address (8 bytes) on the stack, so when a function gets control, `rsp` is not aligned. You have to make that extra space yourself, by pushing something or subtracting 8 from `rsp`.
- The only registers that the called function is required to preserve (the calle-save registers) are: `rbp`, `rbx`, `r12`, `r13`, `r14`, `r15`. All others are free to be changed by the called function.
- The callee is also supposed to save the control bits of the XMCSR and the x87 control word, but x87 instructions are rare in 64-bit code so you probably don’t have to worry about this.
- Integers are returned in `rax` or `rdx:rax`, and floating point values are returned in `xmm0` or `xmm1:xmm0`.
- push
	- Decrement `rsp` by the size of the operand, then store *𝑥* in `[rsp]`
- pop
	- Move `[rsp]` into *𝑥*, then increment `rsp` by the size of the operand
- jnz
	- If the processor’s Z (zero) flag is set, jump to the given label
- call
	- Push the address of the next instruction, then jump to the label
- ret
	- Pop into the instruction pointer