# L3AKCTF 2025 - Whitespace

- Write-Up Author: looy3 


## Write up  

---

Description:

I used [this site](https://patorjk.com/software/taag/#p=display&f=3x5&t=Type%20Something%20)) to make some ASCII art to save my super awesome flag, but something happened and all the spaces got deleted and I lost it. Luckily, I do remember that the MD5 hash of my flag was a7bf5f833c3e4ceff2e006ff801ec16b, so maybe you can help me.

The site, as mentioned, creates ASCII art. Here is an example
```
                            
#   ###  #  # #  ## ### ### 
#     # # # # # #    #  #   
#    ## ### ##  #    #  ##  
#     # # # # # #    #  #   
### ### # # # #  ##  #  #   
```
The provided flag.txt was the following:
```
####
##
###
##
######

###
####
#####
####
####

###
#
###
##
###


####
###
#####


###
#
#
#
####

##
#####
#####
####
##

##
######
##
####


###
####
###
##
###


##
##
##
###

##
#####
######
###
#

#
######
##
#####


##
#####
###
###
###


#####
####
####
###

##
##
###
#
####

#
###
#
##
###

###
###
####
##
#####


#####
##
###


###
#
#
#
####

#
#####
####
######


###
#
#
#
####

##
######
###
####


###
###
####
###
###


##
###
#
######

###
####
####
#####
###


##
##
##
###

######
##
####
##
######

#
###
##
###
###

##
##
###
#
####

####
####
####
###
###


#####
###
####


#
####
##
###
###

##
#####
##
###
###


###
#
###
###

####
#####
###
####
###


##
##
##
###

##
#####
####
##
#

###
#####
###
###
###


##
###
###
###

##
#####
##
###
###

#
###
##
###
###

###
####
#####
#####
###

##
###
####
###
##
```
As mentioned, 0 white space. I tried checking to see if the numbers of pound signs matched up with what I expected. I expected the flag would have a LEAK{flag} format. But that would require a different number of pound signs:
```
    
#   
#   
#   
#   
### 
    
### 
#   
##  
#   
### 
    
 #  
# # 
### 
# # 
# # 
    
```
i.e. typing an L would require just 7 characters rather than the expected 17. I first looked through all possible fonts which included 17 # signs for the L letter. None were a match. After a bit of deliberation, I decided I'd look to see what the sums were. It's possible he did two per line when he lost his whitespace. This turned out to give me the correct numbers.

```
    
#   
#   
#   
#   
###              7 +
    
### 
#   
##  
#   
###               10 = 17
    
 #  
# # 
### 
# # 
# #              10 +
    
# #               10 = 20
# # 
##  
# # 
# # 
```
This matched the number of # per two lines. As the font was 3x5, I wrote a script which would count the total number of # per two lines for the entire flag file:

```
def count_hashes(input_filename, output_filename):
    with open(input_filename, 'r') as file:
        lines = file.readlines()

    with open(output_filename, 'w') as output_file:
        for i in range(0, len(lines), 6):
            # Get the current group of 6 lines
            group = lines[i:i+6]
            # Count the number of '#' characters in the group
            hash_count = sum(line.count('#') for line in group)
            output_file.write(f"{hash_count}\n")  # Write the count to the output file

# Replace 'input.txt' and 'output.txt' with your actual file paths
count_hashes('flag.txt', 'output.txt')
```
After removing the first two (as they were known), I got 
```
12
12
10
18
14
15
9
17
14
16
16
12
10
17
10
10
16
10
15
16
12
19
9
20
12
12
18
12
13
15
10
19
9
14
17
11
15
12
20
14
```
My first strategy to translate this was the following:
1. Calculate every combination of hashes for each letter (A=10, a=7... Aa=17) 
2. Brute force based on the possible combination per each slot and hash the result, comparing with the hash provided.

Unfortunately, while using every possible ASCII combination, the enormity of the numbers required some 999,999,999,999,999,999,999,999,999,999,999,999,999,999,999,999 possible combinations. My pathetic i5-8250u simply could not handle this kind of math. Thus, I realized I needed a different strategy. Thus, I decided I could create a mapping for each letter combination. For example, L3 = 
```
#   ### 4
#     # 2
#    ## 3
#     # 2
### ### 6
0
OR
[ 4, 2, 3, 2, 6, 0]
```
By creating this mapping, I assumed I'd drastically reduce the total number of possible options as ultimately the sum of these numbers is equal to the operations I would be doing previously. To do this, I first generated every possible ascii combination:
```
import itertools

def generate_ascii_combinations(output_file):
    # Define non-whitespace printable ASCII characters (ASCII 33 to 126)
    ascii_chars = [chr(i) for i in range(33, 127)]  # Printable ASCII excluding space
    combinations = itertools.product(ascii_chars, repeat=2)

    # Write combinations to the specified output file
    with open(output_file, 'w') as file:
        for combo in combinations:
            file.write(''.join(combo) + '\n')  # Join tuple into string and write to file

def main():
    output_file = 'asciiCombo.txt'  # Output file name
    generate_ascii_combinations(output_file)

if __name__ == "__main__":
    main()
```
Then I put it into the ascii generated, split it using the previously mentioned mapping:
```
def count_hashes(file, output_file):
    with open(output_file, 'w') as out_file:
        while True:
            counts = []
            for _ in range(6):
                try:
                    line = next(file).strip()  # Read the next line and strip whitespace
                    counts.append(line.count('#'))  # Count the number of '#' in the line
                except StopIteration:
                    if counts:  # If we have some counts, break to write them
                        break
                    else:  # If no counts, we're done
                        return

            if counts:  # Only write if there are counts to display
                # Create the formatted output
                formatted_counts = f"[ {', '.join(map(str, counts))} ]\n"
                out_file.write(formatted_counts)


def main():
    input_file_path = 'art.txt'  # Replace with your input file path
    output_file_path = 'artNums.txt'  # Output file path
    with open(input_file_path, 'r') as file:
        count_hashes(file, output_file_path)

if __name__ == "__main__":
    main()
```

And then did the same for the flag. Then I compared the two of them, and generated every possible combination for each position in the flag:
```
____
3L
EL
L3
LE
____
2p
2q
5p
5q
AB
AD
AK
AR
BA
DA
KA
RA
p2
p5
q2
q5
____
j{
j}
{j
}j
____
as
ax
az
sa
su
us
ux
uz
xa
xu
za
zu
____
.I
.Z
7_
I.
T_
Z.
_7
_T
____
4p
4q
Ah
Bm
Dm
Km
Mn
Rm
hA
mB
mD
mK
mR
nM
p4
q4
____
tt
____
3r
Er
r3
rE
____
_n
n_
____
4m
m4
____
=h
ct
h=
tc
____
1h
Ur
h1
rU
____
cy
gn
ng
yc
____
4_
_4
____
1:
:1
_t
t_
____
1f
f1
____
rs
rx
rz
sr
xr
zr
____
.I
.Z
7_
I.
T_
Z.
_7
_T
____
ab
ad
ba
bu
da
du
ub
ud
____
.I
.Z
7_
I.
T_
Z.
_7
_T
____
ht
th
____
!W
$(
$)
$/
$<
$>
$\
$|
($
(H
)$
)H
/$
/H
2s
2x
2z
3n
5s
5x
5z
<$
<H
>$
>H
En
H(
H)
H/
H<
H>
H\
H|
Mj
O{
O}
Q{
Q}
W!
\$
\H
jM
n3
nE
s2
s5
x2
x5
z2
z5
{O
{Q
|$
|H
}O
}Q
____
_y
y_
____
0a
0u
6c
a0
c6
u0
____
_n
n_
____
2I
2Z
33
3E
5I
5Z
E3
EE
I2
I5
Z2
Z5
____
Ls
Lx
Lz
_b
_d
b_
d_
sL
xL
zL
____
4_
_4
____
$Y
2t
3h
4X
5t
BP
DP
Eh
HY
KP
PB
PD
PK
PR
RP
X4
Y$
YH
h3
hE
t2
t5
____
ar
cv
ra
ru
ur
vc
____
1s
1x
1z
s1
x1
z1
____
1t
t1
____
_c
c_
____
#Y
0t
Y#
t0
____
_n
n_
____
4r
r4
____
0r
r0
____
_w
w_
____
1t
t1
____
Ls
Lx
Lz
_b
_d
b_
d_
sL
xL
zL
____
0w
6e
6o
8a
8u
OW
QW
WO
WQ
a8
e6
o6
u8
w0
____
+X
Gv
X+
fs
fx
fz
n{
n}
sf
vG
xf
zf
{n
}n
```
using
```
def find_matching_lines(art_nums_file, combo_file, flag_nums_file):
    with open(art_nums_file, 'r') as art_file, open(combo_file, 'r') as combo_file, open(flag_nums_file, 'r') as flag_file:
        art_lines = art_file.readlines()
        combo_lines = combo_file.readlines()
        flag_lines = flag_file.readlines()

        for flag_line in flag_lines:
            print("____")
            flag_value = flag_line.strip()  # Remove whitespace
            for index, art_line in enumerate(art_lines):
                if art_line.strip() == flag_value:  # Compare stripped lines
                    print(combo_lines[index].strip())  # Print corresponding line from combo.txt

def main():
    art_nums_file = 'artNums.txt'  # Path to artNums.txt
    combo_file = 'asciiCombo.txt'  # Path to 2combo.txt
    flag_nums_file = 'flagNums.txt'  # Path to flagNums.txt

    find_matching_lines(art_nums_file, combo_file, flag_nums_file)

if __name__ == "__main__":
    main()
	```
I then calculated the total number of possibilities. Unfortunately, it remained quite high at 10,147,616,633,877,474,956,437,094,400. Thus, I combed through the possible combinations by hand and tried to handwrite as much of the flag as possible, reducing the total number of required calculations. I knew the flag started with L3AK{ and ended with }. I quickly found this quite easy to do and came up with
```
L3AK{jus7_p4tt3rn_m4tch1ng_4t_f1rs7_bu7_th3n_y0u_n33d_4_
```
But was unable to get the next work. This left the total number of possible combinations at 794,787,840. From there, my script to find the correct flag was easy enough:
```
import hashlib
import time
import math

def print_possible_combinations(art_nums_file, combo_file, flag_nums_file):
    with open(art_nums_file, 'r') as art_file, open(combo_file, 'r') as combo_file, open(flag_nums_file, 'r') as flag_file:
        art_lines = art_file.readlines()
        combo_lines = combo_file.readlines()
        flag_lines = flag_file.readlines()[28:]  # Start from position 29

        matching_combos = []
        print("\nPossible combinations for remaining positions:")
        print("--------------------------------------------")

        for position, flag_line in enumerate(flag_lines, 29):
            flag_value = flag_line.strip()
            current_matches = []
            print(f"\nPosition {position}:")
            for index, art_line in enumerate(art_lines):
                if art_line.strip() == flag_value:
                    combo = combo_lines[index].strip()
                    current_matches.append(combo)
                    print(f"  {combo}")
            if current_matches:
                matching_combos.append(len(current_matches))

        total = math.prod(matching_combos) if matching_combos else 0
        return total, matching_combos

def find_matching_lines(art_nums_file, combo_file, flag_nums_file, target_hash):
    with open(art_nums_file, 'r') as art_file, open(combo_file, 'r') as combo_file, open(flag_nums_file, 'r') as flag_file:
        art_lines = art_file.readlines()
        combo_lines = combo_file.readlines()
        flag_lines = flag_file.readlines()[28:]  # Start from position 29

        matching_combos = []

        for flag_line in flag_lines:
            flag_value = flag_line.strip()
            current_matches = []
            for index, art_line in enumerate(art_lines):
                if art_line.strip() == flag_value:
                    current_matches.append(combo_lines[index].strip())
            if current_matches:
                matching_combos.append(current_matches)

        counter = [0]
        prefix = "L3AK{jus7_p4tt3rn_m4tch1ng_4t_f1rs7_bu7_th3n_y0u_n33d_4_"
        if matching_combos:
            generate_combinations(matching_combos, 0, prefix, target_hash, counter)

def generate_combinations(matching_combos, depth, current_combination, target_hash, counter):
    if depth == len(matching_combos):
        counter[0] += 1

        combo_hash = hashlib.md5(current_combination.encode()).hexdigest()

        if combo_hash == target_hash:
            print(f"\nFOUND MATCHING HASH!")
            print(f"Combination: {current_combination}")
            print(f"Hash: {combo_hash}")
            return True

        if counter[0] % 5000000 == 0:
            print(f"\nProgress update - Combinations tried: {counter[0]:,}")
            print(f"Current combination: {current_combination}")
            print(f"Current hash: {combo_hash}")

        return False

    for combo in matching_combos[depth]:
        new_combination = current_combination + combo
        if generate_combinations(matching_combos, depth + 1, new_combination, target_hash, counter):
            return True

    return False

def main():
    art_nums_file = 'artNums.txt'
    combo_file = 'asciiCombo.txt'
    flag_nums_file = 'flagNums.txt'
    target_hash = 'a7bf5f833c3e4ceff2e006ff801ec16b'

    # Print all possible combinations and calculate total
    total_combinations, combinations_per_position = print_possible_combinations(art_nums_file, combo_file, flag_nums_file)
    print(f"\nTotal possible combinations for remaining positions: {total_combinations:,}")
    print(f"Number of combinations per remaining position: {combinations_per_position}")
    print("\nStarting hash search...\n")

    start_time = time.time()
    find_matching_lines(art_nums_file, combo_file, flag_nums_file, target_hash)
    end_time = time.time()

    print(f"\nTotal execution time: {end_time - start_time:.2f} seconds")

if __name__ == "__main__":
    main()
```

And the flag was
```
L3AK{jus7_p4tt3rn_m4tch1ng_4t_f1rs7_bu7_th3n_y0u_n33d_4_h3ur1st1c_t0_n4rr0w_1t_d0wn}
```

I learned
- The importance of trying the more advanced/annoying solution, I suppose.