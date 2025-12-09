# # Network Discovery - `Scan-ta Clause`

- Found the three keys using the guide provided

- ### **Key 1**:
```
ftp> get tbfc_qa_key1 -
remote: tbfc_qa_key1
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for tbfc_qa_key1 (13 bytes).
KEY1:3aster_
```

- ### **Key 2:**
```
thecosmic@fedora ~/tryhackme $ nc -v 10.48.183.72 25251
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.48.183.72:25251.
TBFC maintd v0.2
Type HELP for commands.
HELP
Commands: HELP, STATUS, GET KEY, QUIT
GET KEY
KEY2:15_th3_
```

- ### **Key 3:**
```
thecosmic@fedora ~/tryhackme $ dig @10.48.183.72 TXT key3.tbfc.local +short
"KEY3:n3w_xm45"
```

- ### Final Flag:
```
tbfcapp@tbfc-devqa01:~$ mysql -D tbfcqa01 -e "select * from flags;"
+----+------------------------------+
| id | flag                         |
+----+------------------------------+
|  1 | THM{4ll_s3rvice5_d1sc0vered} |
+----+------------------------------+
```
## Objectives:
### 1. 
**What evil message do you see on top of the website?**
- `Pwned by HopSec`

### 2.
**What is the first key part found on the FTP server?**
- `3aster_`

### 3. 
**What is the second key part found in the TBFC app?**
- `15_th3_`

### 4.
**What is the third key part found in the DNS records?**
- `n3w_xm45`

### 5.
**Which port was the MySQL database running on?**
- `3306`

### 6.
**Finally, what's the flag you found in the database?**
- `THM{4ll_s3rvice5_d1sc0vered}`


## Reference: https://tryhackme.com/room/networkservices-aoc2025-jnsoqbxgky

