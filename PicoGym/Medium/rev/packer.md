# PicoGym - packer

- Write-Up Author: looy3 


## Write up  

---
prompt:

```
Reverse this linux executable?

```
As I found in unpackme, another medium rev challenge, UPX packed binaries tend to be a series of 4-6 bytes when you run "strings" on them. So when I saw this (and based on the name of the challenge), I suspected similar. Therefore, I ran upx -d out. Then, I ran
```
strings "out" | grep "pico"
```
Which returned nothing. So I used
```
strings "out" | grep "flag"
```
which gave me this:
```
Password correct, please see flag: 7069636f4354467b5539585f556e5034636b314e365f42316e34526933535f35646565343434317d
```
Using cyberchef, I translated this hex to the flag:
```
picoCTF{U9X_UnP4ck1N6_B1n4Ri3S_5dee4441}
```
