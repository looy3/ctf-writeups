# PicoGym - ASCIIFTW

- Write-Up Author: looy3 


## Write up  

---

The hint:
```
This program has constructed the flag using hex ascii values. Identify the flag text by disassembling the program.
You can download the file from here.
```
I first ran file on the file...
```

asciiftw: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=055f4d31f776ff9fba2b38d7e67a7d8a65cdd301, for GNU/Linux 3.2.0, not stripped
```
So it's an ELF binary in a rev challenge. I immediately expected I'd use Ghidra. But first, I cat-ted it to see if I could find anything there.
The only string I saw was
```
The flag starts with %x
```
So I brought it into Ghidra. In the main function, I immediately noted this block of assembly.
```
                             **************************************************************
                             *                          FUNCTION                          *
                             **************************************************************
                             undefined main()
             undefined         <UNASSIGNED>   <RETURN>
             undefined8        Stack[-0x10]:8 local_10                                XREF[2]:     0010117e(W), 
                                                                                                   0010121b(R)  
             undefined1        Stack[-0x1a]:1 local_1a                                XREF[1]:     001011fc(W)  
             undefined1        Stack[-0x1b]:1 local_1b                                XREF[1]:     001011f8(W)  
             undefined1        Stack[-0x1c]:1 local_1c                                XREF[1]:     001011f4(W)  
             undefined1        Stack[-0x1d]:1 local_1d                                XREF[1]:     001011f0(W)  
             undefined1        Stack[-0x1e]:1 local_1e                                XREF[1]:     001011ec(W)  
             undefined1        Stack[-0x1f]:1 local_1f                                XREF[1]:     001011e8(W)  
             undefined1        Stack[-0x20]:1 local_20                                XREF[1]:     001011e4(W)  
             undefined1        Stack[-0x21]:1 local_21                                XREF[1]:     001011e0(W)  
             undefined1        Stack[-0x22]:1 local_22                                XREF[1]:     001011dc(W)  
             undefined1        Stack[-0x23]:1 local_23                                XREF[1]:     001011d8(W)  
             undefined1        Stack[-0x24]:1 local_24                                XREF[1]:     001011d4(W)  
             undefined1        Stack[-0x25]:1 local_25                                XREF[1]:     001011d0(W)  
             undefined1        Stack[-0x26]:1 local_26                                XREF[1]:     001011cc(W)  
             undefined1        Stack[-0x27]:1 local_27                                XREF[1]:     001011c8(W)  
             undefined1        Stack[-0x28]:1 local_28                                XREF[1]:     001011c4(W)  
             undefined1        Stack[-0x29]:1 local_29                                XREF[1]:     001011c0(W)  
             undefined1        Stack[-0x2a]:1 local_2a                                XREF[1]:     001011bc(W)  
             undefined1        Stack[-0x2b]:1 local_2b                                XREF[1]:     001011b8(W)  
             undefined1        Stack[-0x2c]:1 local_2c                                XREF[1]:     001011b4(W)  
             undefined1        Stack[-0x2d]:1 local_2d                                XREF[1]:     001011b0(W)  
             undefined1        Stack[-0x2e]:1 local_2e                                XREF[1]:     001011ac(W)  
             undefined1        Stack[-0x2f]:1 local_2f                                XREF[1]:     001011a8(W)  
             undefined1        Stack[-0x30]:1 local_30                                XREF[1]:     001011a4(W)  
             undefined1        Stack[-0x31]:1 local_31                                XREF[1]:     001011a0(W)  
             undefined1        Stack[-0x32]:1 local_32                                XREF[1]:     0010119c(W)  
             undefined1        Stack[-0x33]:1 local_33                                XREF[1]:     00101198(W)  
             undefined1        Stack[-0x34]:1 local_34                                XREF[1]:     00101194(W)  
             undefined1        Stack[-0x35]:1 local_35                                XREF[1]:     00101190(W)  
             undefined1        Stack[-0x36]:1 local_36                                XREF[1]:     0010118c(W)  
             undefined1        Stack[-0x37]:1 local_37                                XREF[1]:     00101188(W)  
             undefined1        Stack[-0x38]:1 local_38                                XREF[2]:     00101184(W), 
                                                                                                   00101200(R)  
                             main                                            XREF[4]:     Entry Point(*), 
                                                                                          _start:001010a1(*), 0010204c, 
                                                                                          001020f8(*)  
        00101169 f3 0f 1e fa     ENDBR64
        0010116d 55              PUSH       RBP
        0010116e 48 89 e5        MOV        RBP,RSP
        00101171 48 83 ec 30     SUB        RSP,0x30
        00101175 64 48 8b        MOV        RAX,qword ptr FS:[0x28]
                 04 25 28 
                 00 00 00
        0010117e 48 89 45 f8     MOV        qword ptr [RBP + local_10],RAX
        00101182 31 c0           XOR        EAX,EAX
        00101184 c6 45 d0 70     MOV        byte ptr [RBP + local_38],0x70
        00101188 c6 45 d1 69     MOV        byte ptr [RBP + local_37],0x69
        0010118c c6 45 d2 63     MOV        byte ptr [RBP + local_36],0x63
        00101190 c6 45 d3 6f     MOV        byte ptr [RBP + local_35],0x6f
        00101194 c6 45 d4 43     MOV        byte ptr [RBP + local_34],0x43
        00101198 c6 45 d5 54     MOV        byte ptr [RBP + local_33],0x54
        0010119c c6 45 d6 46     MOV        byte ptr [RBP + local_32],0x46
        001011a0 c6 45 d7 7b     MOV        byte ptr [RBP + local_31],0x7b
        001011a4 c6 45 d8 41     MOV        byte ptr [RBP + local_30],0x41
        001011a8 c6 45 d9 53     MOV        byte ptr [RBP + local_2f],0x53
        001011ac c6 45 da 43     MOV        byte ptr [RBP + local_2e],0x43
        001011b0 c6 45 db 49     MOV        byte ptr [RBP + local_2d],0x49
        001011b4 c6 45 dc 49     MOV        byte ptr [RBP + local_2c],0x49
        001011b8 c6 45 dd 5f     MOV        byte ptr [RBP + local_2b],0x5f
        001011bc c6 45 de 49     MOV        byte ptr [RBP + local_2a],0x49
        001011c0 c6 45 df 53     MOV        byte ptr [RBP + local_29],0x53
        001011c4 c6 45 e0 5f     MOV        byte ptr [RBP + local_28],0x5f
        001011c8 c6 45 e1 45     MOV        byte ptr [RBP + local_27],0x45
        001011cc c6 45 e2 41     MOV        byte ptr [RBP + local_26],0x41
        001011d0 c6 45 e3 53     MOV        byte ptr [RBP + local_25],0x53
        001011d4 c6 45 e4 59     MOV        byte ptr [RBP + local_24],0x59
        001011d8 c6 45 e5 5f     MOV        byte ptr [RBP + local_23],0x5f
        001011dc c6 45 e6 37     MOV        byte ptr [RBP + local_22],0x37
        001011e0 c6 45 e7 42     MOV        byte ptr [RBP + local_21],0x42
        001011e4 c6 45 e8 43     MOV        byte ptr [RBP + local_20],0x43
        001011e8 c6 45 e9 44     MOV        byte ptr [RBP + local_1f],0x44
        001011ec c6 45 ea 39     MOV        byte ptr [RBP + local_1e],0x39
        001011f0 c6 45 eb 37     MOV        byte ptr [RBP + local_1d],0x37
        001011f4 c6 45 ec 31     MOV        byte ptr [RBP + local_1c],0x31
        001011f8 c6 45 ed 44     MOV        byte ptr [RBP + local_1b],0x44
        001011fc c6 45 ee 7d     MOV        byte ptr [RBP + local_1a],0x7d
        00101200 0f b6 45 d0     MOVZX      EAX,byte ptr [RBP + local_38]

```
The binary prints "the flag starts with 0x70", the same value as the first bit of this chunk. So the flag is probably just here. Accordingly, I copied it, brought it to Notepad++, and found/replaced 
```".*," with " "``` 
That way I used wildcard/newline features to isolate the hex. I then brought the hex over to cyberchef and translated From hex. This gave me the flag!
