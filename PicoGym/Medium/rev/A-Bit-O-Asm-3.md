# PicoGym - A-Bit-O-Asm-3

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
<+15>:    mov    DWORD PTR [rbp-0xc],0x9fe1a
<+22>:    mov    DWORD PTR [rbp-0x8],0x4
<+29>:    mov    eax,DWORD PTR [rbp-0xc]
<+32>:    imul   eax,DWORD PTR [rbp-0x8]
<+36>:    add    eax,0x1f5
<+41>:    mov    DWORD PTR [rbp-0x4],eax
<+44>:    mov    eax,DWORD PTR [rbp-0x4]
<+47>:    pop    rbp
<+48>:    ret
```
Seems the value of the PTR 0xc (0x9fe1a as per instruction 15) was moved to the eax. The imul operation is applied on the value of the eax with the value of the PTR 0x8 (which is defined as 0x4). A quick google search revealed to me that imul = integer multiplication where imul [arg1] [arg2] arg1 = where result is stored and first value to be multiplied, arg2 = multiplier. I got the integers like so and multiplied:
```
A-Bit-O-Asm-2 % printf "%d" 0x9fe1a
654874%                                                                                                                                                                                                          A-Bit-O-Asm-2 % printf "%d" 0x4
4%   
```
The next instruction told me to add 0x1f5 to the result. This gave me the flag successfully!


I learned
 - imul and add operations in assembly use the encoded decimal number base