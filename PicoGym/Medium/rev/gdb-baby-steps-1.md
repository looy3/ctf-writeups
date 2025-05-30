# PicoGym - GDB-Baby-Steps-1

- Write-Up Author: looy3 


## Write up  

---

This was kinda easy but helped me learn some GDB. The challenges asked me
```
Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
```
Using GDB, I simply ran "disassemble main" and got
```
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   rbp
   0x000000000000112e <+5>:     mov    rbp,rsp
   0x0000000000001131 <+8>:     mov    DWORD PTR [rbp-0x4],edi
   0x0000000000001134 <+11>:    mov    QWORD PTR [rbp-0x10],rsi
   0x0000000000001138 <+15>:    mov    eax,0x86342
   0x000000000000113d <+20>:    pop    rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
```
Seems 15 bytes into the main function, 0x86342 (in hex) is being moved into eax. So how do we convert it into the decimal number base? Google revealed I could simply run 
```
(gdb) p 0x86342
```
to get the  decimal value. Thus, the flag was picoCTF{549698}.

I learned
- mov      eax indicates the program is trying to move the value after eax into the eax register. 
- Can see the decimal value of a hexidecimal (indicated by 0x prefix) using pwndbg by running ```p 0x(value)```
- eax is used for I/O port access, arithmetic, interrupt calls