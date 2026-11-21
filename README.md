# PASSWORD CRACKING REPORT

### John the Ripper (JTR) & Networkwalks Password Cracking Tools

**W3-PM-FINAL | CYBERSECURITY | NETWORKWALKS**

| Field                                           | Detail                                                                                                                      |
| ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Pentester Name (Cybersecurity Professional)** | **Wathuta Wamugi**                                                                                                          |
| **Program/Batch**                               | B084A - Networkwalks                                                                                                        |
| **Date**                                        | 21 November 2026                                                                                                            |
| **Modules completed**                           | W3-PM1 (Password Cracking with JTR)<br>W3-PM2 (Password Cracking with Networkwalks Tools)                                  |
| **Client/Target**                               | Locked PDF files supplied by Networkwalks for the password-cracking labs                                                   |
| **Permission secured from client?**             | Yes. Files were provided by Networkwalks specifically for this exercise.                                                   |
| **Phases covered**                              | **Phase 6:** Password Attacks (offline, dictionary attack against PDF encryption)                                          |

# 1. Liability Disclaimer

I have performed these activities only on the sample PDF files provided for this lab. All these materials are for education and research purposes only. Do not use anything from here to break the law. The instructor, the authors and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job and a permanent record. In most countries unauthorised access is a crime even when nothing is damaged.

# 2. Introduction

This report covers Week 3 of the Networkwalks Cybersecurity & Ethical Hacking program:

- **W3-PM1:** cracking a password-protected PDF using John the Ripper (JTR).
- **W3-PM2:** cracking a password-protected PDF (`My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf`) using the Networkwalks Hash Calculator and Password Cracker web tools.

Both modules demonstrate the same underlying idea: a locked PDF stores its password as a hash, and a cracking tool recovers the real password by trying candidate words against that hash until one matches.

All work was done on **Ubuntu**, entirely from the **command line**. The lab guide's instructions describe installing the **Johnny** GUI front-end for JTR, and I followed that installation process, but performed the actual cracking using JTR's CLI (`john-the-ripper`) rather than the Johnny GUI. The only file required for the exercise was `My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf` (hash extracted to `hash1.txt`). On top of that, I cracked three further sample PDFs for additional practice with JTR's wordlist attack mode.

# 3. Tools Used

| Tool                      | Purpose                                                                                      |
| -------------------------- | ---------------------------------------------------------------------------------------------- |
| Ubuntu (terminal)          | Operating system used for all password-cracking activities                                   |
| `pdf2john.pl`              | Extracts a crackable hash from a password-protected PDF (part of the John the Ripper suite)  |
| John the Ripper (`john-the-ripper`, installed via snap) | Runs a dictionary/wordlist attack against the extracted hash to recover the password |
| Johnny (GUI for JTR)       | Installed from source as per the lab guide, as an alternative graphical front-end for JTR (not used for the actual cracking — see note below) |
| Networkwalks Hash Calculator | Online tool that extracts a `$pdf$...` hash from a locked PDF, entirely in the browser       |
| Networkwalks Password Cracker | Online tool that runs a dictionary attack against a pasted `$pdf$...` hash                  |

# 4. Activities Performed

## 4.1 Installing JTR and Johnny on Ubuntu (W3-PM1)

The lab guide covers installing JTR and its GUI, Johnny, on Windows. Since I was working on Ubuntu, I installed the equivalent tools with the following terminal commands:

```bash
sudo apt update && sudo apt install -y git build-essential qtbase5-dev qt5-qmake
sudo snap install john-the-ripper
cd ~ && git clone https://github.com/openwall/johnny.git
cd johnny
qmake && make -j$(nproc)
sudo cp johnny /usr/local/bin/
johnny &
```

- `sudo snap install john-the-ripper` installs John the Ripper's CLI, including the `pdf2john.pl` helper script (found at `/snap/john-the-ripper/current/bin/pdf2john.pl`).
- The `git clone` / `qmake` / `make` steps build Johnny (the GUI) from source, since it isn't packaged for Ubuntu the way it's distributed as a Windows installer in the guide.
- `johnny &` launches the GUI in the background.

**Note:** although I built and could launch Johnny, I performed all of the actual cracking through JTR's command line instead of the GUI, as shown in the terminal screenshots.

## 4.2 Cracking locked PDFs with JTR CLI

For each PDF, I followed the same two-step process:

**Step 1: Extract the hash with `pdf2john.pl`**

```bash
perl /snap/john-the-ripper/current/bin/pdf2john.pl "<filename>.pdf" > hashN.txt
```

**Step 2: Run JTR against the hash file**

```bash
/snap/bin/john-the-ripper hashN.txt
/snap/bin/john-the-ripper --show hashN.txt
```

JTR first tries "single crack" mode (based on the filename/metadata), and when that doesn't find a match, falls back to its built-in wordlist (`password.lst`). All four files below were cracked within a few seconds using the wordlist.

| # | Hash file   | PDF file                                               | Cracked password | Notes                                          |
| - | ----------- | ------------------------------------------------------- | ------------------ | ------------------------------------------------- |
| 1 | `hash1.txt` | `My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf`         | `password1`        | **The only required file for this exercise.** Also cracked using the Networkwalks web tools — see section 4.3. |
| 2 | `hash2.txt` | `My Locked PDF1.pdf`                                     | `good-luck`        | Extra practice file                              |
| 3 | `hash3.txt` | `My Locked PDF2.pdf`                                     | `password1`        | Extra practice file                              |
| 4 | `hash4.txt` | `My Locked PDF3.pdf`                                     | `1qaz2wsx`         | Extra practice file                              |

I then opened each of the four PDFs and entered its recovered password to confirm the crack was correct (see the "entering the password" screenshots in section 8).

## 4.3 W3-PM2: Networkwalks Hash Calculator & Password Cracker

For the required file, `My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf`, I additionally completed the task as instructed using the two Networkwalks browser-based tools:

1. Uploaded the PDF to the **Hash Calculator** (`networkwalks.com/hash-calculator/`), which extracted the same `$pdf$...` hash locally in the browser.
2. Pasted that hash into the **Password Cracker** (`networkwalks.com/password-cracker/`), which ran a dictionary attack against it and returned the password.

This recovered the same password (`password1`) as the JTR CLI method in section 4.2, confirming both approaches — local command-line and in-browser — arrive at the same result.

# 5. Risk Analysis / Impact

| **#** | **Finding**                                  | **Evidence / Observation**                                        | **Potential Impact**                                                                                 | **Risk Level** |
| ----- | --------------------------------------------- | -------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | -------------- |
| 1     | All four sample PDFs used weak, common passwords | `password1`, `good-luck`, `1qaz2wsx` are all present in JTR's default 3,000-word list | Dictionary-based passwords are cracked almost instantly, regardless of the encryption algorithm protecting the file | **● Critical** |
| 2     | PDF password protection does not prevent offline attacks | `pdf2john.pl` can extract a crackable hash from any password-protected PDF without needing the password first | An attacker who obtains a locked PDF can attack the password offline, with no limit on attempts and no lockout | **● Medium**   |
| 3     | Default wordlists are enough to crack many real-world passwords | A ~3,000-word built-in list cracked every sample file tried          | Organizations relying on "the file is password protected" as their only control are not meaningfully protected | **● Medium**   |

**Risk level key:** ● Critical ● Medium ● Low

These findings reflect the strength of the four deliberately weak sample passwords used in this lab, not a vulnerability in PDF encryption itself. A long, random, non-dictionary password would not be recovered this quickly, if at all, using the same wordlist.

# 6. Recommendations

1. **Use long, random, non-dictionary passwords** for any encrypted file, especially one that may be shared or stored outside a controlled environment.
2. **Avoid common patterns.** Keyboard-walk passwords (`1qaz2wsx`), phrases (`good-luck`), and word-plus-number combinations (`password1`) are the first candidates any dictionary attack tries.
3. **Prefer a passphrase over a single word+number.** A random passphrase of several unrelated words is both easier to remember and dramatically harder to crack than `word+digit`.
4. **Don't rely on file-level password protection alone** for sensitive documents; combine it with secure storage and access controls, since the password can be attacked offline once the file is obtained.
5. **Test your own organization's protected files** periodically with the same tools (JTR, or the Networkwalks Hash Calculator/Password Cracker) to confirm real-world password strength, and extend the wordlist with sector-specific terms when doing so.

# 7. Conclusion

In Week 3 of the Cybersecurity & Ethical Hacking program, I learned how a locked PDF's password is stored as a crackable hash, and how a tool like John the Ripper extracts that hash with `pdf2john.pl` and then tries candidate passwords against it until one matches. I installed JTR and its Johnny GUI on Ubuntu, but performed all cracking through JTR's command line, since it let me work through each file quickly and keep a clear command-by-command record for this report. I also completed the required file through the Networkwalks Hash Calculator and Password Cracker web tools, which arrived at the same password using the same underlying hash-then-dictionary-attack approach.

All sample passwords — `password1`, `good-luck`, and `1qaz2wsx` — were recovered in a few seconds each using JTR's default built-in wordlist, which shows how quickly a weak, dictionary-based password falls to an offline attack regardless of the file format protecting it. This reinforced for me why password strength, not just "is it password protected", is what actually determines how safe a file is.

# 8. Evidences Collected

*Screenshots collected as evidence during the activities (stored in the `screenshots/` folder). Raw hash output is in the `outputs/` folder.*

### Required file: My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf

![JTR CLI cracking the required file](screenshots/JTR%20Password%20Cracking%20(CLI).png)

![Entering the recovered password to open the required PDF](screenshots/Cracked%20Password.png)

![Hash extracted with the Networkwalks Hash Calculator](screenshots/Hash%20Calculator.png)

![Password recovered with the Networkwalks Password Cracker](screenshots/Password%20Cracker.png)

![Flag captured — the required PDF opened](screenshots/Unlocked%20PDF.png)

### Extra practice: My Locked PDF1.pdf

![JTR CLI cracking My Locked PDF1.pdf](screenshots/JTR%20CLI%20Crack%20My%20Locked%20PDF1.png)

![Entering the recovered password to open My Locked PDF1.pdf](screenshots/Cracked%20Pass%20My%20Locked%20PDF1.png)

![Flag captured — My Locked PDF1.pdf opened](screenshots/Unlocked%20My%20Locked%20PDF1.png)

### Extra practice: My Locked PDF2.pdf

![JTR CLI cracking My Locked PDF2.pdf](screenshots/JTR%20CLI%20Crack%20My%20Locked%20PDF2.png)

![Entering the recovered password to open My Locked PDF2.pdf](screenshots/Cracked%20Pass%20My%20Locked%20PDF2.png)

![Flag captured — My Locked PDF2.pdf opened](screenshots/Unlocked%20My%20Locked%20PDF2.png)

### Extra practice: My Locked PDF3.pdf

![JTR CLI cracking My Locked PDF3.pdf](screenshots/JTR%20CLI%20Crack%20My%20Locked%20PDF3.png)

![Entering the recovered password to open My Locked PDF3.pdf](screenshots/Cracked%20Pass%20My%20Locked%20PDF3.png)

![Flag captured — My Locked PDF3.pdf opened](screenshots/Unlocked%20My%20Locked%20PDF3.png)

# 9. Repository Structure

```
.
├── README.md
├── banner.png
├── outputs/
│   ├── hash1.txt
│   ├── hash2.txt
│   ├── hash3.txt
│   └── hash4.txt
└── screenshots/
    ├── JTR Password Cracking (CLI).png
    ├── Cracked Password.png
    ├── Hash Calculator.png
    ├── Password Cracker.png
    ├── Unlocked PDF.png
    ├── JTR CLI Crack My Locked PDF1.png
    ├── Cracked Pass My Locked PDF1.png
    ├── Unlocked My Locked PDF1.png
    ├── JTR CLI Crack My Locked PDF2.png
    ├── Cracked Pass My Locked PDF2.png
    ├── Unlocked My Locked PDF2.png
    ├── JTR CLI Crack My Locked PDF3.png
    ├── Cracked Pass My Locked PDF3.png
    └── Unlocked My Locked PDF3.png
```

-End-
