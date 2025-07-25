# PicoGym - Picker-III

- Write-Up Author: looy3 


## Write up  

---

We were given this prompt
```
Can you figure out how this program works to get the flag?

```
With this python code:
```

import re



USER_ALIVE = True
FUNC_TABLE_SIZE = 4
FUNC_TABLE_ENTRY_SIZE = 32
CORRUPT_MESSAGE = 'Table corrupted. Try entering \'reset\' to fix it'

func_table = ''

def reset_table():
  global func_table

  # This table is formatted for easier viewing, but it is really one line
  func_table = \
'''\
print_table                     \
read_variable                   \
write_variable                  \
getRandomNumber                 \
'''

def check_table():
  global func_table

  if( len(func_table) != FUNC_TABLE_ENTRY_SIZE * FUNC_TABLE_SIZE):
    return False

  return True


def get_func(n):
  global func_table

  # Check table for viability
  if( not check_table() ):
    print(CORRUPT_MESSAGE)
    return

  # Get function name from table
  func_name = ''
  func_name_offset = n * FUNC_TABLE_ENTRY_SIZE
  for i in range(func_name_offset, func_name_offset+FUNC_TABLE_ENTRY_SIZE):
    if( func_table[i] == ' '):
      func_name = func_table[func_name_offset:i]
      break

  if( func_name == '' ):
    func_name = func_table[func_name_offset:func_name_offset+FUNC_TABLE_ENTRY_SIZE]
  
  return func_name


def print_table():
  # Check table for viability
  if( not check_table() ):
    print(CORRUPT_MESSAGE)
    return

  for i in range(0, FUNC_TABLE_SIZE):
    j = i + 1
    print(str(j)+': ' + get_func(i))


def filter_var_name(var_name):
  r = re.search('^[a-zA-Z_][a-zA-Z_0-9]*$', var_name)
  if r:
    return True
  else:
    return False


def read_variable():
  var_name = input('Please enter variable name to read: ')
  if( filter_var_name(var_name) ):
    eval('print('+var_name+')')
  else:
    print('Illegal variable name')


def filter_value(value):
  if ';' in value or '(' in value or ')' in value:
    return False
  else:
    return True


def write_variable():
  var_name = input('Please enter variable name to write: ')
  if( filter_var_name(var_name) ):
    value = input('Please enter new value of variable: ')
    if( filter_value(value) ):
      exec('global '+var_name+'; '+var_name+' = '+value)
      print('global '+var_name+'; '+var_name+' = '+value)
    else:
      print('Illegal value')
  else:
    print('Illegal variable name')


def call_func(n):
  """
  Calls the nth function in the function table.
  Arguments:
    n: The function to call. The first function is 0.
  """

  # Check table for viability
  if( not check_table() ):
    print(CORRUPT_MESSAGE)
    return

  # Check n
  if( n < 0 ):
    print('n cannot be less than 0. Aborting...')
    return
  elif( n >= FUNC_TABLE_SIZE ):
    print('n cannot be greater than or equal to the function table size of '+FUNC_TABLE_SIZE)
    return

  # Get function name from table
  func_name = get_func(n)

  # Run the function
  eval(func_name+'()')


def dummy_func1():
  print('in dummy_func1')

def dummy_func2():
  print('in dummy_func2')

def dummy_func3():
  print('in dummy_func3')

def dummy_func4():
  print('in dummy_func4')

def getRandomNumber():
  print(4)  # Chosen by fair die roll.
            # Guaranteed to be random.
            # (See XKCD)

def win():
  # This line will not work locally unless you create your own 'flag.txt' in
  #   the same directory as this script
  flag = open('flag.txt', 'r').read()
  #flag = flag[:-1]
  flag = flag.strip()
  str_flag = ''
  for c in flag:
    str_flag += str(hex(ord(c))) + ' '
  print(str_flag)

def help_text():
  print(
  '''
This program fixes vulnerabilities in its predecessor by limiting what
functions can be called to a table of predefined functions. This still puts
the user in charge, but prevents them from calling undesirable subroutines.

* Enter 'quit' to quit the program.
* Enter 'help' for this text.
* Enter 'reset' to reset the table.
* Enter '1' to execute the first function in the table.
* Enter '2' to execute the second function in the table.
* Enter '3' to execute the third function in the table.
* Enter '4' to execute the fourth function in the table.

Here's the current table:
  '''
  )
  print_table()



reset_table()

while(USER_ALIVE):
  choice = input('==> ')
  if( choice == 'quit' or choice == 'exit' or choice == 'q' ):
    USER_ALIVE = False
  elif( choice == 'help' or choice == '?' ):
    help_text()
  elif( choice == 'reset' ):
    reset_table()
  elif( choice == '1' ):
    call_func(0)
  elif( choice == '2' ):
    call_func(1)
  elif( choice == '3' ):
    call_func(2)
  elif( choice == '4' ):
    call_func(3)
  else:
    print('Did not understand "'+choice+'" Have you tried "help"?')

```
I first played around with the program a little bit to understand what it did more intuitively.
```
Rev % python3 picker-III.py
==> ?

This program fixes vulnerabilities in its predecessor by limiting what
functions can be called to a table of predefined functions. This still puts
the user in charge, but prevents them from calling undesirable subroutines.

* Enter 'quit' to quit the program.
* Enter 'help' for this text.
* Enter 'reset' to reset the table.
* Enter '1' to execute the first function in the table.
* Enter '2' to execute the second function in the table.
* Enter '3' to execute the third function in the table.
* Enter '4' to execute the fourth function in the table.

Here's the current table:

1: print_table
2: read_variable
3: write_variable
4: getRandomNumber
==> 1
1: print_table
2: read_variable
3: write_variable
4: getRandomNumber
```
so these are our options. then I tried to read some nonsense to see what would happen.
```

==> 2
Please enter variable name to read: asdf
Traceback (most recent call last):
  File "/home/tjesa/PicoCTF/Medium/Rev/picker-III.py", line 192, in <module>
    call_func(1)
  File "/home/tjesa/PicoCTF/Medium/Rev/picker-III.py", line 126, in call_func
    eval(func_name+'()')
  File "<string>", line 1, in <module>
  File "/home/tjesa/PicoCTF/Medium/Rev/picker-III.py", line 78, in read_variable
    eval('print('+var_name+')')
  File "<string>", line 1, in <module>
NameError: name 'asdf' is not defined
Rev % python3 picker-III.py
```
so it's only reading valid variables. I decided to try a function next
```
==> 2
Please enter variable name to read: win
<function win at 0x7c92c15e5d00>
```
so it's outputting the address of valid variables. What does 3 do?
```
==> 3
Please enter variable name to write: win
Please enter new value of variable: 0x7cd41b3ed620
==> 1
1: print_table
2: read_variable
3: write_variable
4: getRandomNumber
==> 2
Please enter variable name to read: win
137250432013856
```
Setting the address of the win variable into the win variable seems to just give me a long somewhat useless int. Maybe the data loaded into that address or something. Still, I tried to execute it.
```
==> 3
Please enter variable name to write: getRandomNumber
Please enter new value of variable: 0x745c01bedd00
==> 4
Traceback (most recent call last):
  File "/home/tjesa/PicoCTF/Medium/Rev/picker-III.py", line 196, in <module>
    call_func(3)
  File "/home/tjesa/PicoCTF/Medium/Rev/picker-III.py", line 126, in call_func
    eval(func_name+'()')
  File "<string>", line 1, in <module>
TypeError: 'int' object is not callable
```
So that int probably cannot be used. Since we have the source code, I added a line of code to give me the output of 
      exec('global '+var_name+'; '+var_name+' = '+value)
	  with
	        print('global '+var_name+'; '+var_name+' = '+value)
```
==> 3
Please enter variable name to write: getRandomNumber
Please enter new value of variable: 0x7ae0039e9da0
global getRandomNumber; getRandomNumber = 0x7ae0039e9da0
==> 2
Please enter variable name to read: getRandomNumber
135102551989664
==> 3
Please enter variable name to write: getRandomNumber
Please enter new value of variable: win
global getRandomNumber; getRandomNumber = win
```
As you can see here, I found that the address is read correctly into the variable. So I tried to read variable names themselves. executing this way got me 
```
==> 4
0x70 0x69 0x63 0x6f 0x43 0x54 0x46 0x7b 0x37 0x68 0x31 0x35 0x5f 0x31 0x35 0x5f 0x77 0x68 0x34 0x37 0x5f 0x77 0x33 0x5f 0x67 0x33 0x37 0x5f 0x77 0x31 0x37 0x68 0x5f 0x75 0x35 0x33 0x72 0x35 0x5f 0x31 0x6e 0x5f 0x63 0x68 0x34 0x72 0x67 0x33 0x5f 0x32 0x32 0x36 0x64 0x64 0x32 0x38 0x35 0x7d
```
yay!