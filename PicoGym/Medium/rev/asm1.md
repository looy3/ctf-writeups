# PicoGym - asm1

- Write-Up Author: looy3 


## Write up  

---

This one is pretty easy.

The hint:
```
What does asm1(0x6fa) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. Source
```

The assembly:
```
asm1:
        <+0>:   push   ebp
        <+1>:   mov    ebp,esp
        <+3>:   cmp    DWORD PTR [ebp+0x8],0x3a2
        <+10>:  jg     0x512 <asm1+37>
        <+12>:  cmp    DWORD PTR [ebp+0x8],0x358
        <+19>:  jne    0x50a <asm1+29>
        <+21>:  mov    eax,DWORD PTR [ebp+0x8]
        <+24>:  add    eax,0x12
        <+27>:  jmp    0x529 <asm1+60>
        <+29>:  mov    eax,DWORD PTR [ebp+0x8]
        <+32>:  sub    eax,0x12
        <+35>:  jmp    0x529 <asm1+60>
        <+37>:  cmp    DWORD PTR [ebp+0x8],0x6fa
        <+44>:  jne    0x523 <asm1+54>
        <+46>:  mov    eax,DWORD PTR [ebp+0x8]
        <+49>:  sub    eax,0x12
        <+52>:  jmp    0x529 <asm1+60>
        <+54>:  mov    eax,DWORD PTR [ebp+0x8]
        <+57>:  add    eax,0x12
        <+60>:  pop    ebp
        <+61>:  ret
```

```
<+0>:   push   ebp
<+1>:   mov    ebp,esp
<+3>:   cmp    DWORD PTR [ebp+0x8],0x3a2
<+10>:  jg     0x512 <asm1+37>
```
Let's start with these. We can kinda ignore the first two instructions. The next compares the PTR (0x6fa) which we got at first with a value 0x3a2. Entering these hexes into python reveals that 0x6fa is greater. +10 instruction says "if the first value of the cmp is larger, jump to instruction 37 in asm1". So we ignore everything until
```
<+37>:  cmp    DWORD PTR [ebp+0x8],0x6fa
<+44>:  jne    0x523 <asm1+54>
<+46>:  mov    eax,DWORD PTR [ebp+0x8]
```
Now we compare the PTR to a variable with the same value. +44 says jump if they are not equal. So we ignore it. +46 moves the PTR to the eax register.
```
<+49>:  sub    eax,0x12
<+52>:  jmp    0x529 <asm1+60>
```
Now we just subtract 0x12 from 0x6fa and replace the eax with the result (0x6e8). +52 says we jump to +60
```
<+60>:  pop    ebp
<+61>:  ret
```
We can ignore these. So the flag is 0x6e8