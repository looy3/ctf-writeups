# PicoGym - DISKO 1	

- Write-Up Author: looy3 


## Write up  

---

We were given this prompt
```
Can you find the flag in this disk image?
Download the disk image here.

Hint:
Maybe Strings could help? If only there was a way to do that?
```
I first started by using ```strings disko-1.dd.gz``` which returned a lot of 4 byte long nonsense. For example:
```
-G/Io
NC5e
&f:]
6IP6m
RiA;
|J~l
*)U5
Bg1Z
2(ZQ.
&Fsd
f?~7
 @B{D
ji!6|
u*:E
ga@G
\~g2
#HEJ
?rSoO"
?j2t@
/U2U_
#Syh
Nz%d
'y}N
xvFw
=R^1
'+\_
3tZ.
B!4KV
c|tV
[5(L
IM[6
'18?#
h0.H*
0v#&:D
XL8E
-]s9k
*mV0H
TD:`
vs;K
%M(S

```
I doublechecked to see if I could see anything valuable by running ```strings disko-1.dd.gz | grep "F{"``` which returned similarly useless information. A quick google search revealed I could unpack .dd.gz files using gunzip into the local directory. I ran ```gunzip disko-1.dd.gz``` and it worked. Now there was disko-1.dd in the current directory. Unlikely unzipping most people are familiar with, gunzip does not create a directory. It simply creates a more readable image. I then ran ```strings disko-1.dd``` and got the following, which included the flag:


```
:/icons/appicon
# $Id: piconv,v 2.8 2016/08/04 03:15:58 dankogai Exp $
piconv -- iconv(1), reinvented in perl
  piconv [-f from_encoding] [-t to_encoding]
  piconv -l
  piconv -r encoding_alias
  piconv -h
B<piconv> is perl version of B<iconv>, a character encoding converter
a technology demonstrator for Perl 5.8.0, but you can use piconv in the
piconv converts the character encoding of either STDIN or files
Therefore, when both -f and -t are omitted, B<piconv> just acts
picoCTF{1t5_ju5t_4_5tr1n9_e3408eef}
```
That was easy! 

What I learned
- gunzip for .dd.gz
- you can strings through an unzipped image.