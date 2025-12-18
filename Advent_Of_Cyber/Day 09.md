# Passwords - A Cracking Christmas

**Process creation:** Password cracking has a small set of well-known binaries and command patterns that we can look out for. A mix of process events, file activity, GPU signals, and network touches tied to tooling and wordlists. Our goal is to make the activity obvious without drowning in noise.

- Binaries and aliases: `john`, `hashcat`, `fcrackzip`, `pdfcrack`, `zip2john`, `pdf2john.pl`, `7z`, `qpdf`, `unzip`, `7za`, `perl` invoking `pdf2john.pl`.
- Command‑line traits: `--wordlist`, `-w`, `--rules`, `--mask`, `-a 3`, `-m` in `Hashcat`, references to `rockyou.txt`, `SecLists`, `zip2john`, `pdf2john`.
- `Potfiles` and state: `~/.john/john.pot`, `.hashcat/hashcat.potfile`, `john.rec`.

## Solve:
1. To crack the `pdf` file,  I used `pdfcrack` command with `rockyou.txt` to get the password and then opening the pdf with the password, I got the flag.
	- Command used: `pdfcrack -f flag.pdf -w /usr/share/wordlists/rockyou.txt`
	- Flag: `THM{Cr4ck1ng_PDFs_1s_34$y}`
2. To crack the zip file, I first used the `zip2john` command and outputted it to `ziphash.txt`, then used the `john` using `ziphash.txt` and `rockyou.txt` to get the password, then unzipping with the password, I got a `txt` file which contained the flag.
	- Command used: `zip2john flag.zip > ziphash.txt`
	- Command used: `john --wordlist=/usr/share/wordlists/rockyou.txt ziphash.txt`
	- Flag: `THM{Cr4ck1n6_z1p$_1s_34$yyyy}`
