# 1. RSA Oracle

>Can you abuse the oracle? An attacker was able to intercept communications between a bank and a fintech company. They managed to get the [message](https://artifacts.picoctf.net/c_titan/151/secret.enc) (ciphertext) and the [password](https://artifacts.picoctf.net/c_titan/151/password.enc) that was used to encrypt the message.
>After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here `nc titan.picoctf.net 52430`. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.


## Solve: 

First I read about RSA, how it works and how it encrypts and decrypts a message.
- Then booting up the challenge through the `nc` port, I was shown a program that could decrypt and encrypt text through its RSA encryption algorithm. But when inputting the password given, it would not provide the decrypted message for it.
```
sparsh@LAPTOP-F80QI4V2 ~/ctf2/cusen $ nc titan.picoctf.net 52430
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
E
enter text to encrypt (encoded length must be less than keysize): password123
password123

encoded cleartext as Hex m: 70617373776f7264313233

ciphertext (m ^ e mod n) 3671275645359602079321918958559826502400235535142294324062114725227883806163370270675649450924937463525094676155610801615375868221054370485869798703144128

what should we do for you?
E --> encrypt D --> decrypt.
D
Enter text to decrypt: 3671275645359602079321918958559826502400235535142294324062114725227883806163370270675649450924937463525094676155610801615375868221054370485869798703144128
decrypted ciphertext as hex (c ^ d mod n): 70617373776f7264313233
decrypted ciphertext: password123
```
- Here in no way, the algorithm was exposed, I couldn't figure out the private key using this so I looked at one of the hints.

- Then looking at one of the hints, it said that the attack is a "chosen plaintext attack"

- So I searched RSA CPA on google and landed on a `crypto stack exchange` page which briefly explained the same problem.
![Ref1.png](images/Ref1.png)

- This was the solution given,
![Ref2.png](images/Ref2.png)

- Through this, I solved the challenge in the following steps-
	- First I encrypted the string "2" through the `Oracle`, that resulted in `4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505`
	- Then I multiplied this with the encrypted password, that resulted in `19905104266461674438427749120035481333430848061235340017498754943327360989060077822957076637919794979148279735224896131374733698562836139551270909235193224215437233163101052237584916525504858889351339402233392061749581481707426238265002525969310836283110708030433599547806496303497230632152297549255724839730`
	- Then I ran this through the decryption and got this decrypted hex - `0x139afb6b2d22`
	- Then I divided this hex with the string "2" which is 50 in `ascii`, this resulted in `431127279929` or `6461303939` in hex.
	- Converting this to `ascii` text, I got the decrypted password which was `da099`
- After finding the password, it was easy, I just ran the `openssl` command provided and got the flag
```zsh
sparsh@LAPTOP-F80QI4V2 ~/ctf2/rsao $ openssl enc -aes-256-cbc -d -in secret.enc -k da099
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_da099d93}
```
## Flag:
```css
picoCTF{su((3ss_(r@ck1ng_r3@_da099d93}
```
