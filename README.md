# Week 3 Cybersecurity Lab: Password Cracking & Hash Analysis

## Overview

This repository documents the complete execution of Week 3 project modules for the Cybersecurity Internship Program by NETWORWALKS. The objective of this lab is to evaluate the security of encrypted PDF documents using both local command-line utilities in **Kali Linux** (`pdf2john` and `john`) and online web-based tools (**Networkwalks Hash Calculator & Password Cracker**).

---

## Step-by-Step Execution

### Method 1: Local Terminal Password Cracking (Kali Linux)

#### Step 1: Directory Navigation & File Verification

After getting the encrypted files traverse to the saved directory to confirm that all encrypted target files are present. In my case all the files were stored in Downloads directory:

```bash
cd Downloads
ls

```

*Expected Output:* `My-Locked-PDF1.pdf`, `My-Locked-PDF2.pdf`, `My-Locked-PDF3.pdf`
<img width="483" height="192" alt="image" src="https://github.com/user-attachments/assets/3f07cf93-f52f-4ce3-811f-5d90d8ca9594" />

---

#### Step 2: Hash Extraction via `pdf2john`

Use the `pdf2john` helper script to dump the raw cryptographic hash signatures from each PDF file into separate text files:

```bash
pdf2john My-Locked-PDF1.pdf > PDF1.txt
pdf2john My-Locked-PDF2.pdf > PDF2.txt
pdf2john My-Locked-PDF3.pdf > PDF3.txt
```
See the raw cryptographic hash signatures from each PDF file into separate text files using cat command:

```bash 
cat PDF1.txt
cat PDF2.txt
cat PDF3.txt

```
<img width="1162" height="463" alt="image" src="https://github.com/user-attachments/assets/16a1cc53-e43c-4635-b0fc-18621e89aecd" />

---

#### Step 3: Decompress the `rockyou.txt` Wordlist

Kali Linux stores the `rockyou.txt` dictionary in a compressed archive (`.gz`) by default.
On first attempt we can see that the wordlist rockyou.txt is not there since its still compressed (rockyou.txt.gz file)

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt PDF1.txt
Using default input encoding: UTF-8
Loaded 1 password hash (PDF [MD5 SHA2 RC4/AES 32/64])
Cost 1 (revision) is 4 for all loaded hashes
Will run 2 OpenMP threads
fopen: /usr/share/wordlists/rockyou.txt: No such file or directory
```
 Decompress it before running the attack:
```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz

```

---

#### Step 4: Execute Dictionary Attack with John the Ripper

Run `john` against each extracted hash file using the decompressed `rockyou.txt` wordlist:

```bash
# Crack PDF 1
john --wordlist=/usr/share/wordlists/rockyou.txt PDF1.txt

# Crack PDF 2
john --wordlist=/usr/share/wordlists/rockyou.txt PDF2.txt

# Crack PDF 3
john --wordlist=/usr/share/wordlists/rockyou.txt PDF3.txt

```

---

#### Step 5: View and Verify Cracked Passwords

Display the recovered plain-text passwords from the `john` session database:

```bash
john --show PDF1.txt
john --show PDF2.txt
john --show PDF3.txt

```

*Results:*

* `My-Locked-PDF1.pdf:password1`

* `My-Locked-PDF2.pdf:password1`

* `My-Locked-PDF3.pdf:1qaz2wsx`

<img width="306" height="355" alt="image" src="https://github.com/user-attachments/assets/860ac505-8532-4636-898e-dae75d19c6ad" />


---

### Method 2: Browser-Based Password Cracking (Networkwalks Tools)

To verify cross-platform hash extraction and recovery, **`My-Locked-PDF3.pdf`** was audited using web-based tools.

#### Step 1: Online Hash Extraction

1. Open the [Networkwalks Hash Calculator](https://networkwalks.com/hash-calculator/) in any browser.


2. Upload **`My-Locked-PDF3.pdf`**.


3. Copy the extracted `$pdf$` hash output string:


```text
$pdf$4*4*128*-1028*1*16*34eb542eff4e1b0b32d25ce15a9a7281*32*b77872bfc9a24fb2f845066283a8fc1b0021446990b9e4114071a4d9104984c1*32*e7572256e4b552cd57988f5134214b91920d94d7a6bf550ea94a2995c7f2ab02
```

<img width="924" height="724" alt="image" src="https://github.com/user-attachments/assets/762289c5-87cb-4f9e-b628-929d3f7a15a2" />



#### Step 2: Online Dictionary Attack

1. Open the [Networkwalks Password Cracker](https://networkwalks.com/password-cracker).


2. Paste the extracted `$pdf$` hash into the attack terminal field.


3. Click **Start Cracking** to initiate the dictionary attack against the built-in wordlist.


4. *Result:* The web application successfully matches the hash on trial `#35/100`, returning **`1qaz2wsx`**.


<img width="877" height="667" alt="image" src="https://github.com/user-attachments/assets/862739a0-b858-47eb-96f8-ff580729c5f2" />

---

### Step 6: Decryption & View the content of the files

1. Open Adobe Acrobat Reader (or any PDF viewer) and open **`My-Locked-PDF1.pdf`**.

2. Enter the recovered credential **`password1`**.

3. **File Content:**

My-Locked-PDF1 File
<img width="598" height="550" alt="image" src="https://github.com/user-attachments/assets/4f856c6e-b4e5-40f8-8e06-fd39d2640dee" />

My-Locked-PDF2 File
<img width="595" height="838" alt="image" src="https://github.com/user-attachments/assets/1068ad6f-f5a3-46b7-85ee-5e3d73fac14e" />

My-Locked-PDF3 File
<img width="819" height="742" alt="image" src="https://github.com/user-attachments/assets/fab40a14-8a71-44f8-99b4-4097234be37b" />



---

## Insights & Recommendations

* **Vulnerability of Simple Patterns:** Weak credentials like `password1` or keyboard walks like `1qaz2wsx` are trivial to recover using standard dictionary wordlists.

* **Encryption vs. Hashing:** Encryption is a two-way function requiring a key for decryption. Hashing is a one-way digest verification mechanism used by software to validate password input against stored metadata.

* **Best Practices:** void common patterns, dictionary words, predictable words and personal context such as names, birth dates, company names or public personal details that can be gather through OSINT.

---

LinkedIn Post Screenshot (View the post here https://www.linkedin.com/feed/update/urn:li:activity:7508348519313788929/)
<img width="1908" height="2566" alt="image" src="https://github.com/user-attachments/assets/c4858743-2cac-4319-b536-a71962eac06e" />

