# L3AKCTF 2025 - BabyRev

- Write-Up Author: looy3 


## Write up  

---

This was a pretty easy challenge. It wasn't stripped so we could easily see the names of all functions. The description was 
```
They always give you strings challenges, we are not the same, we do better.
```
This was the main function:
```
undefined8 main(void)

{
  int iVar1;
  time_t tVar2;
  size_t __n;
  int local_c;
  
  tVar2 = time((time_t *)0x0);
  srand((uint)tVar2);
  init_remap();
  signal(2,sigint_handler);
  printf("Enter flag: ");
  fflush(stdout);
  fgets(input,0x40,stdin);
  for (local_c = 0; input[local_c] != '\0'; local_c = local_c + 1) {
    if (-1 < (char)input[local_c]) {
      input[local_c] = remap[(int)(uint)(byte)input[local_c]];
    }
  }
  __n = strlen(flag);
  iVar1 = strncmp(input,flag,__n);
  if (iVar1 == 0) {
    puts("Correct! Here is your prize.");
  }
  else {
    puts("Wrong flag. Try harder.");
  }
  return 0;
}
```
Pretty clearly, it is making sure the length of the string was the same as the input and if a boolean equaled 0 (when the flag and input were the same), it'd give the flag. But before that, it basically ran a function named remap on the input. Reading the remap function:
```

void init_remap(void)

{
  int local_c;
  
  for (local_c = 0; local_c < 0x80; local_c = local_c + 1) {
    remap[local_c] = (char)local_c;
  }
  remap[0x61] = 0x71;
  remap[0x62] = 0x77;
  remap[99] = 0x65;
  remap[100] = 0x72;
  remap[0x65] = 0x74;
  remap[0x66] = 0x79;
  remap[0x67] = 0x75;
  remap[0x68] = 0x69;
  remap[0x69] = 0x6f;
  remap[0x6a] = 0x70;
  remap[0x6b] = 0x61;
  remap[0x6c] = 0x73;
  remap[0x6d] = 100;
  remap[0x6e] = 0x66;
  remap[0x6f] = 0x67;
  remap[0x70] = 0x68;
  remap[0x71] = 0x6a;
  remap[0x72] = 0x6b;
  remap[0x73] = 0x6c;
  remap[0x74] = 0x7a;
  remap[0x75] = 0x78;
  remap[0x76] = 99;
  remap[0x77] = 0x76;
  remap[0x78] = 0x62;
  remap[0x79] = 0x6e;
  remap[0x7a] = 0x6d;
  return;
}

```	
So it seems it just remapped some hex values (probably ascii) to others. I wrote a python script to reverse strings using this mapping:

```
def init_remap():
    # Create the remap array based on the provided mappings
    remap = list(range(128))  # Initialize remap with identity mapping

    # Apply the specific remappings
    remap[0x61] = 0x71  # 'a' -> 'q'
    remap[0x62] = 0x77  # 'b' -> 'w'
    remap[99] = 0x65    # 'c' -> 'e'
    remap[100] = 0x72   # 'd' -> 'r'
    remap[0x65] = 0x74  # 'e' -> 't'
    remap[0x66] = 0x79  # 'f' -> 'y'
    remap[0x67] = 0x75  # 'g' -> 'u'
    remap[0x68] = 0x69  # 'h' -> 'i'
    remap[0x69] = 0x6F  # 'i' -> 'o'
    remap[0x6a] = 0x70  # 'j' -> 'p'
    remap[0x6b] = 0x61  # 'k' -> 'a'
    remap[0x6c] = 0x73  # 'l' -> 's'
    remap[0x6d] = 100   # 'm' -> 'd'
    remap[0x6e] = 0x66  # 'n' -> 'f'
    remap[0x6f] = 0x67  # 'o' -> 'g'
    remap[0x70] = 0x68  # 'p' -> 'h'
    remap[0x71] = 0x6a  # 'q' -> 'j'
    remap[0x72] = 0x6b  # 'r' -> 'k'
    remap[0x73] = 0x6c  # 's' -> 'l'
    remap[0x74] = 0x7a  # 't' -> 'z'
    remap[0x75] = 0x78  # 'u' -> 'x'
    remap[0x76] = 99     # 'v' -> 'c'
    remap[0x77] = 0x76  # 'w' -> 'v'
    remap[0x78] = 0x62  # 'x' -> 'b'
    remap[0x79] = 0x6e  # 'y' -> 'n'
    remap[0x7a] = 0x6d  # 'z' -> 'm'

    return remap

def reverse_remap(input_string):
    remap = init_remap()
    reverse_map = [0] * 128

    # Create a reverse mapping
    for i in range(128):
        reverse_map[remap[i]] = i

    # Transform the input string back to its original form
    output_string = ''.join(chr(reverse_map[ord(char)]) for char in input_string)
    return output_string

if __name__ == "__main__":
    input_string = input("Enter the remapped string: ")
    original_string = reverse_remap(input_string)
    print("Original string:", original_string)
```
Now to find the flag before this conversion. Looking into the memory, it's pretty easy to spot something named "flag"
```
                             flag                                            XREF[5]:     Entry Point(*), main:00101516(*), 
                                                                                          main:0010151d(*), 
                                                                                          main:00101528(*), 
                                                                                          main:0010152f(*)  
        00104020 4c 33 41        undefine
                 4b 7b 6e 
                 67 78 5f 
           00104020 4c              undefined14Ch                     [0]                               XREF[5]:     Entry Point(*), main:00101516(*), 
                                                                                                                     main:0010151d(*), 
                                                                                                                     main:00101528(*), 
                                                                                                                     main:0010152f(*)  
           00104021 33              undefined133h                     [1]
           00104022 41              undefined141h                     [2]
           00104023 4b              undefined14Bh                     [3]
           00104024 7b              undefined17Bh                     [4]
           00104025 6e              undefined16Eh                     [5]
           00104026 67              undefined167h                     [6]
           00104027 78              undefined178h                     [7]
           00104028 5f              undefined15Fh                     [8]
           00104029 71              undefined171h                     [9]
           0010402a 6b              undefined16Bh                     [10]
           0010402b 74              undefined174h                     [11]
           0010402c 5f              undefined15Fh                     [12]
           0010402d 66              undefined166h                     [13]
           0010402e 67              undefined167h                     [14]
           0010402f 7a              undefined17Ah                     [15]
           00104030 5f              undefined15Fh                     [16]
           00104031 75              undefined175h                     [17]
           00104032 67              undefined167h                     [18]
           00104033 66              undefined166h                     [19]
           00104034 66              undefined166h                     [20]
           00104035 71              undefined171h                     [21]
           00104036 5f              undefined15Fh                     [22]
           00104037 75              undefined175h                     [23]
           00104038 78              undefined178h                     [24]
           00104039 74              undefined174h                     [25]
           0010403a 6c              undefined16Ch                     [26]
           0010403b 6c              undefined16Ch                     [27]
           0010403c 5f              undefined15Fh                     [28]
           0010403d 64              undefined164h                     [29]
           0010403e 74              undefined174h                     [30]
           0010403f 7d              undefined17Dh                     [31]
           00104040 00              undefined100h                     [32]
           00104041 00              undefined100h                     [33]
           00104042 00              undefined100h                     [34]
           00104043 00              undefined100h                     [35]
           00104044 00              undefined100h                     [36]
           00104045 00              undefined100h                     [37]
           00104046 00              undefined100h                     [38]
           00104047 00              undefined100h                     [39]
           00104048 00              undefined100h                     [40]
           00104049 00              undefined100h                     [41]
           0010404a 00              undefined100h                     [42]
           0010404b 00              undefined100h                     [43]
           0010404c 00              undefined100h                     [44]
           0010404d 00              undefined100h                     [45]
           0010404e 00              undefined100h                     [46]
           0010404f 00              undefined100h                     [47]
           00104050 00              undefined100h                     [48]
           00104051 00              undefined100h                     [49]
           00104052 00              undefined100h                     [50]
           00104053 00              undefined100h                     [51]
           00104054 00              undefined100h                     [52]
           00104055 00              undefined100h                     [53]
           00104056 00              undefined100h                     [54]
           00104057 00              undefined100h                     [55]
           00104058 00              undefined100h                     [56]
           00104059 00              undefined100h                     [57]
           0010405a 00              undefined100h                     [58]
           0010405b 00              undefined100h                     [59]
           0010405c 00              undefined100h                     [60]
           0010405d 00              undefined100h                     [61]
           0010405e 00              undefined100h                     [62]
           0010405f 00              undefined100h                     [63]
```
removing the junk and putting it into my script, I got the flag.