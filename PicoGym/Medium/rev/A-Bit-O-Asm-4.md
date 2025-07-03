# PicoGym - A-Bit-O-Asm-4

- Write-Up Author: looy3 


## Write up  

---

The challenge asked me 
```
Can you figure out what is in the eax register? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Download the assembly dump here.
```
the assembly dump was this:
```
<+0>:     endbr64
<+4>:     push   rbp
<+5>:     mov    rbp,rsp
<+8>:     mov    DWORD PTR [rbp-0x14],edi
<+11>:    mov    QWORD PTR [rbp-0x20],rsi
<+15>:    mov    DWORD PTR [rbp-0x4],0x9fe1a
<+22>:    cmp    DWORD PTR [rbp-0x4],0x2710
<+29>:    jle    0x55555555514e <main+37>
<+31>:    sub    DWORD PTR [rbp-0x4],0x65
<+35>:    jmp    0x555555555152 <main+41>
<+37>:    add    DWORD PTR [rbp-0x4],0x65
<+41>:    mov    eax,DWORD PTR [rbp-0x4]
<+44>:    pop    rbp
<+45>:    ret
```
So this is how I translated those instructions
+ 11 and before - ignore
+ 15 - Set memory at 0x4 address to 0x9fe1a
+ 22 - I didn't know this but google said it is a comparison which sets flags based on subtraction:
```
Flags Affected
The comparison affects several flags in the CPU's status register:

Zero Flag (ZF): Set if the result is zero (i.e., the two operands are equal).
Sign Flag (SF): Set if the result is negative (i.e., the first operand is less than the second).
Carry Flag (CF): Set if there is a borrow in the subtraction (i.e., if the first operand is less than the second in unsigned comparison).
Overflow Flag (OF): Set if there is a signed overflow (i.e., the result of the subtraction cannot be represented in the destination).
Usage
After a cmp instruction, you typically follow it with a conditional jump instruction (like je, jne, jg, jl, etc.) to control the flow of the program based on the result of the comparison. For example:

assembly

Copy
cmp eax, ebx
je equal_label  ; Jump to equal_label if eax == ebx
```
so 
+ 22 compares 0x9fe1a to 0x2710 and sets the flag for the next instruction, which is
+ 29 - je jumps to main + 37 address if the first value is less than or equal to the second, which it is not.
+ 31 subtract 0x65 from 0x9fe1a
+ 35 jump (unconditional) to the end of the modification of eax

and the rest of the instructs were jumped.



I learned
 - cmp compares using subtraction and setting CPU flags
 - jle jump based on those flags
 - jmp is an unconditional jump