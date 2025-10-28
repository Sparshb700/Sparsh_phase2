# 1. GDB baby step 1

> Can you figure out what is in the `eax` register at the end of the `main` function? Put your answer in the picoCTF flag format: `picoCTF{n}` where `n` is the contents of the `eax` register in the decimal number base. If the answer was `0x11` your flag would be `picoCTF{17}`. Disassemble [this](https://artifacts.picoctf.net/c/512/debugger0_a).

## Solution:

1. On downloading the binary file, I launched `gdb` ("GNU debugger") with binary file provided.
```
➜  babystep1 gdb debugger0_a
GNU gdb (Ubuntu 15.0.50.20240403-0ubuntu1) 15.0.50.20240403-git
Copyright (C) 2024 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from debugger0_a...
(No debugging symbols found in debugger0_a)
(gdb)
```

2. Then I disassembled the `main` function.
```
(gdb) disas main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   %rbp
   0x000000000000112e <+5>:     mov    %rsp,%rbp
   0x0000000000001131 <+8>:     mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:    mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:    mov    $0x86342,%eax
   0x000000000000113d <+20>:    pop    %rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
(gdb)
```
	From this the value stored at the `eax` register could be seen which was `0x86342`

3. Converting `0x86342` to decimal, I got `549698`, putting it in the picoCTF format, `picoCTF{549698}`

## Flag:

```
picoCTF{549698}
```

## Concepts learnt:

- Learnt the usage of GNU debugger , `gdb`

## Notes:
- Under `gdb`, `disassemble` or `disas` command can be used to look at the assembly code of a specific function


# 2. ARMssembly 1



# 3. Vault Door 3
>This vault uses for-loops and byte arrays. The source code for this vault is here: [VaultDoor3.java](https://jupiter.challenges.picoctf.org/static/a4018cec1446761cb2e8cce05db925fa/VaultDoor3.java)

## Solve:

- Opening up the java file provided, I could see how this program asks for the password to be inputted.

Contents of `VaultDoor3.java`
```java
import java.util.*;

class VaultDoor3 {
    public static void main(String args[]) {
        VaultDoor3 vaultDoor = new VaultDoor3();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
        String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
        if (vaultDoor.checkPassword(input)) {
            System.out.println("Access granted.");
        } else {
            System.out.println("Access denied!");
        }
    }

    // Our security monitoring team has noticed some intrusions on some of the
    // less secure doors. Dr. Evil has asked me specifically to build a stronger
    // vault door to protect his Doomsday plans. I just *know* this door will
    // keep all of those nosy agents out of our business. Mwa ha!
    //
    // -Minion #2671
    public boolean checkPassword(String password) {
        if (password.length() != 32) {
            return false;
        }
        char[] buffer = new char[32];
        int i;
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
        String s = new String(buffer);
        return s.equals("jU5t_a_sna_3lpm12g94c_u_4_m7ra41");
    }
}
```
- Here the string `jU5t_a_sna_3lpm12g94c_u_4_m7ra41` was to be compared with the user input put into the program to get into the `Vault`. But before that check, the user input goes through a series of changes that modified it completely.
- It changed the input like so -
	- The first 8 characters were left as it is.
	- Then next 8 characters were reversed
	- From characters 16 to 32, the characters were reversed with step of two.
	- Then the rest of the characters from 31 to 17 were also reversed the same way with the step value of two.
- To crack the real password, I created this python script that mimics the source code

Contents of `solver.py`
```python
vault = "jU5t_a_sna_3lpm12g94c_u_4_m7ra41"
passwd = ["" for x in range(32)]

for i in range(0, 8):
    passwd.insert(i, vault[i])

for i in range(8, 16):
    passwd.insert(i, vault[23-i])

for i in range(16, 32, 2):
    passwd.insert(i, vault[46-i])

for i in range(31, 16, -2):
    passwd.insert(i, vault[i])

decoded = "".join(passwd)

print("Flag: picoCTF{"+ decoded + "}")
```

- Running my program, I got the following output:`
```zsh
sparsh@LAPTOP-F80QI4V2 ~/ctf2 $ python3 solver.py
Flag: picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}
```
## Flag:
```
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}
```


## Notes and Concepts Learnt:
- I already had basic knowledge of both Python and Java, so this challenge was on the easier side.

