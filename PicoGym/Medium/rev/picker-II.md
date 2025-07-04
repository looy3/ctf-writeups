# PicoGym - Picker-II

- Write-Up Author: looy3 


## Write up  

---

We were given this prompt
```
Can you figure out how this program works to get the flag?

```
With this python code:
```
import sys



def getRandomNumber():
  print(4)  # Chosen by fair die roll.
            # Guaranteed to be random.
            # (See XKCD)

def exit():
  sys.exit(0)

def esoteric1():
  esoteric = \
  '''
  int query_apm_bios(void)
{
        struct biosregs ireg, oreg;

        /* APM BIOS installation check */
        initregs(&ireg);
        ireg.ah = 0x53;
        intcall(0x15, &ireg, &oreg);

        if (oreg.flags & X86_EFLAGS_CF)
                return -1;              /* No APM BIOS */

        if (oreg.bx != 0x504d)          /* "PM" signature */
                return -1;

        if (!(oreg.cx & 0x02))          /* 32 bits supported? */
                return -1;

        /* Disconnect first, just in case */
        ireg.al = 0x04;
        intcall(0x15, &ireg, NULL);

        /* 32-bit connect */
        ireg.al = 0x03;
        intcall(0x15, &ireg, &oreg);

        boot_params.apm_bios_info.cseg        = oreg.ax;
        boot_params.apm_bios_info.offset      = oreg.ebx;
        boot_params.apm_bios_info.cseg_16     = oreg.cx;
        boot_params.apm_bios_info.dseg        = oreg.dx;
        boot_params.apm_bios_info.cseg_len    = oreg.si;
        boot_params.apm_bios_info.cseg_16_len = oreg.hsi;
        boot_params.apm_bios_info.dseg_len    = oreg.di;

        if (oreg.flags & X86_EFLAGS_CF)
                return -1;

        /* Redo the installation check as the 32-bit connect;
           some BIOSes return different flags this way... */

        ireg.al = 0x00;
        intcall(0x15, &ireg, &oreg);

        if ((oreg.eflags & X86_EFLAGS_CF) || oreg.bx != 0x504d) {
                /* Failure with 32-bit connect, try to disconnect and ignore */
                ireg.al = 0x04;
                intcall(0x15, &ireg, NULL);
                return -1;
        }

        boot_params.apm_bios_info.version = oreg.ax;
        boot_params.apm_bios_info.flags   = oreg.cx;
        return 0;
}
  '''
  print(esoteric)


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


def esoteric2():
  esoteric = \
  '''
#include "boot.h"

#define MAX_8042_LOOPS  100000
#define MAX_8042_FF     32

static int empty_8042(void)
{
        u8 status;
        int loops = MAX_8042_LOOPS;
        int ffs   = MAX_8042_FF;

        while (loops--) {
                io_delay();

                status = inb(0x64);
                if (status == 0xff) {
                        /* FF is a plausible, but very unlikely status */
                        if (!--ffs)
                                return -1; /* Assume no KBC present */
                }
                if (status & 1) {
                        /* Read and discard input data */
                        io_delay();
                        (void)inb(0x60);
                } else if (!(status & 2)) {
                        /* Buffers empty, finished! */
                        return 0;
                }
        }

        return -1;
}

/* Returns nonzero if the A20 line is enabled.  The memory address
   used as a test is the int $0x80 vector, which should be safe. */

#define A20_TEST_ADDR   (4*0x80)
#define A20_TEST_SHORT  32
#define A20_TEST_LONG   2097152 /* 2^21 */

static int a20_test(int loops)
{
        int ok = 0;
        int saved, ctr;

        set_fs(0x0000);
        set_gs(0xffff);

        saved = ctr = rdfs32(A20_TEST_ADDR);

        while (loops--) {
                wrfs32(++ctr, A20_TEST_ADDR);
                io_delay();     /* Serialize and make delay constant */
                ok = rdgs32(A20_TEST_ADDR+0x10) ^ ctr;
                if (ok)
                        break;
        }

        wrfs32(saved, A20_TEST_ADDR);
        return ok;
}

/* Quick test to see if A20 is already enabled */
static int a20_test_short(void)
{
        return a20_test(A20_TEST_SHORT);
}
  '''
  print(esoteric)


def filter(user_input):
  if 'win' in user_input:
    return False
  return True


while(True):
  try:
    user_input = input('==> ')
    if( filter(user_input) ):
      eval(user_input + '()')
    else:
      print('Illegal input')
  except Exception as e:
    print(e)
    break
```

The ```eval(user_input + '()')``` pretty clearly allows us to call whatever function we desire. This time, there is some input filtering though. We cannot call the win function. Can we do what it does though? The win function runs
```
flag = open('flag.txt', 'r').read()
```
Considering eval effectively just gets user input and puts () at the end, we can call this. When I was inputting it, I stumbled a bit and forgot to put the () at the end of the read like so:
```==> print(open("flag.txt","r").read)
<built-in method read of _io.TextIOWrapper object at 0x798c53d82380>
'NoneType' object is not callable
```
This confused me a bit. Searching for the issue showed me an article which suggested I was simply referencing the method used rather than the value produced by the method
```
The brackets are important because they tell Python to actually call the method, and this produces the content of the file to be assigned to a. If you don't have the brackets, all you are doing is obtaining the read method and assigning it to a. Thus, when you print a you see a piece of text describing the method (which is what <built-in method read of _io.TextIOWrapper object at 0x02954558> means) instead of the content of the file.

```
So inputting the () should give me the value
```
==> print(open("flag.txt","r").read())
picoCTF{f1l73r5_f41l_c0d3_r3f4c70r_m1gh7_5ucc33d_b924e8e5}
```

Source:
https://wiki.python.org/moin/Asking%20for%20Help/Why%20when%20I%20read%20a%20text%20file%20python%20reads%20it%20as%20%22%3Cbuilt-in%20method%20read%20of%20_io.TextIOWrapper%20object%20at%200x02954558%3E%22%20and%20how%20do%20I%20stop%20this%3F