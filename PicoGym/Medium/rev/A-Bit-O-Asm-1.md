# PicoGym - A-Bit-O-Asm-1

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
<+8>:     mov    DWORD PTR [rbp-0x4],edi
<+11>:    mov    QWORD PTR [rbp-0x10],rsi
<+15>:    mov    eax,0x30
<+20>:    pop    rbp
<+21>:    ret
```
Clearly 0x30 was the answer as it's being moved to eax register. Using cyberchef, I first converted From Hex using UTF-8. This gave me 0. Using the "To Decimal" operator gave me the answer for the flag (ordinal integer array).

Looking at other writeups, it looked like I could have instead just done
```
printf "%d" 0x30
```
I learned
 - Usually better (more efficient) to just printf off shell to convert hex to decimal.