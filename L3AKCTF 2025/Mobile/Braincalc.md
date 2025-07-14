# L3AKCTF 2025 - Brain Calc

- Write-Up Author: looy3 


## Write up  

---

This is the description:
```
Quick and fun math quiz app for practicing.
```
With an attached zip. Inside the zip there was an apk. I used apktool on terminal to unpack this apk. AndroidManifest.xml indicated that the apk was not stripped for debugging when compiled. As the only directory in /assets was chaquopy, I knew this was a python-powered app. Inside the assets folder, we have a lot of .imy folders. To read the contents of these, I ran
```
cp stdlib-common.imy stdlib-common.zip
unzip stdlib-common.zip
```
As a result, I got a ton of .pyc and subfolders. I changed to the app_code subdirectory, moved into the BrainCalc subdirectory, and saw its contents.
```
app.pyc  resources  __init__.pyc  __main__.pyc
```

From here, I guessed I should probably decompile app.pyc. I used an online decompiler [here](https://pylingual.io/)) to convert it to readable python. There, I found a function I was looking for:
```
def get_secret_reward():
    compressed_flag = 'eJzzMXb0rvYqLS6JN4kPNynKjQ8tiHfOMMnJqQUAeHcJQA=='
    try:
        decoded = base64.b64decode(compressed_flag)
        flag = zlib.decompress(decoded).decode('utf-8')
        return flag
    except:
        return 'Error: Could not decode secret'
```

I just transferred this to my own script
```
import random
import zlib
import base64

compressed_flag = 'eJzzMXb0rvYqLS6JN4kPNynKjQ8tiHfOMMnJqQUAeHcJQA=='

decoded = base64.b64decode(compressed_flag)
flag = zlib.decompress(decoded).decode('utf-8')
print(flag)
```

and got the flag!

```
L3AK{Just_4_W4rm_Up_Ch4ll}
```