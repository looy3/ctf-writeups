# PicoGym - Hash only 1

- Write-Up Author: looy3 


## Write up  

---
Upon starting the challenge instance, we are instructed to run 
```
ssh ctf-player@shape-facility.picoctf.net -p 55274
```
, enter the password, and run the binary named flaghasher. I immediately ran ls -al and confirmed that we were in local directory with flaghasher. Running flaghasher simply printed the hash. The challenge didn't seem to indicate that it was necessary to download the binary or source (we don't have) locally, so I assumed I could probably solve this challenge even with a relatively limited Unix. 
I proceeded by printing the contents of flaghasher with cat. Of course, as a binary, it was mostly nonsense. So I used strings on flaghasher. I quickly noticed that the binary was running 
```/bin/bash -c 'md5sum /root/flag.txt'```
. I tried cat-ing /root/flag.txt, despite knowing I almost certainly would not have permissions to do so. So I confirmed that
1. I don't have perms for root directory
2. flaghasher has perms to execute commands on root directory
The simplest next step was to modify the binary so it would print flag.txt, if it was running md5sum on it in plaintext. I'm not sure if this would work but I tried anyways. Unfortunately, the system didn't have nano, vim, or even ed installed. 
So I realized I'd have to take a more novel (for me) approach. I created a local file named `md5sum` to try to fool the binary. I first created it through
```echo '#~/bin/bash' > md5sum```
After that, I appended the command which I hoped would give me the flag:
```echo 'cat /root/flag.txt' >> md5sum```
Next, I made the file executable
```chmod +x md5sum```
Finally, I modified the PATH environment variable so that the current directory (.) is searched first when looking for executable files
```export PATH=.:$PATH```

This tricked the binary into printing the flag!
```
ctf-player@pico-chall$ ./flaghasher
Computing the MD5 hash of /root/flag.txt....

# cat /root/flag.txt
picoCTF{sy5teM_b!n@riEs_4r3_5c@red_0f_yoU_9722baa4}
```

