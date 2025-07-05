# PicoGym - WebNet0

- Write-Up Author: looy3 


## Write up  

---

We were given this prompt
```
We found this packet capture and key. Recover the flag.
```
obviously, when given a pcap, it seems we are analyzing some network traffic. As per the hint, I used wireshark. Upon opening it up, statistics hierarchy page reveals that the lowest layer is Transport Layer Security (TLS). As we have a key, surely we have to decrypt it. I started to try to decrypt it by going to Edit > Preferences > RSA Key. After inputting the provided key file, nothing much happened so I moved on. I also found Edit > Preferences > Protocols > TLS had a page devoted to RSA keys list. It was empty despite me already importing an RSA key initially. Therefore, I added a new key without filling out the port, Ip address, or protocol. Immediately after applying, lots of decrypted HTTP traffic showed up. A look at the hierarchy now showed that Line-Based text data now comprised 12.5% of traffic. While the hexdump/ASCII translation still looked like nonsense, I clicked Follow > HTTP string on the traffic which provided me with a translated HTTP request and response page. One such flag in the response was
```
Pico-Flag: picoCTF{nongshim.shrimp.crackers}
```
That was easy! 

What I learned
- On wireshark
	- Useful to add keys to the protocols using the Edit > preferences > protocols page
	- USeful to Look at Statistics > protocols hierarchy to get an understanding of what's going on
	- It's sometimes not necessary to get all information for applying the key in wireshark
	- Using the Right click > follow > (protocol) string can yield pure text