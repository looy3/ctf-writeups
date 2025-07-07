# PicoGym - unpackme

- Write-Up Author: looy3 


## Write up  

---
prompt:

```
Can you get the flag?
Reverse engineer this binary.

```
The binary is clearly UPX-packed. I installed upx and ran
```
upx -d unpackme
```
This made it easy to edit on ghidra. Looking at the main function:
```

undefined8 main(void)

{
  long in_FS_OFFSET;
  int local_44;
  char *local_40;
  undefined8 local_38;
  undefined8 local_30;
  undefined8 local_28;
  undefined4 local_20;
  undefined2 local_1c;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_38 = 0x4c75257240343a41;
  local_30 = 0x30623e306b6d4146;
  local_28 = 0x5f60643630486637;
  local_20 = 0x37666132;
  local_1c = 0x4e;
  printf("What\'s my favorite number? ");
  receiveInput(&DAT_004b3020,&local_44);
  if (local_44 == 0xb83cb) {
    local_40 = (char *)rotate_encrypt(0,&local_38);
    fputs(local_40,(FILE *)stdout);
    putchar(10);
    free(local_40);
  }
  ```
  Seems like receive input function does some check on the input and sets local_44 to something. Then local_44 is compared to a number. Well, considering the binary clearly asks what its favorite number is, might as well provide it this number and see if it works. I gave it 754635 and got 
  ```
  picoCTF{up><_m3_f7w_e510a27f}
  ```
  I learned
  - UPX is good for packing stuff to make it much smaller (and harder to read)
  - can unpack using upx -d (file)