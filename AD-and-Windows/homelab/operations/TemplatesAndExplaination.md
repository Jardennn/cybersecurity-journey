# Directory explanation

This directory will contain writeups, logs of attacks and explanation of attacks I did and specific techniques/explanation about what are the techniques.

## Layout

```
Writeups and attacks/
|
|-----_log.md - Running log: date, what I tried, what worked and didn't
|
|-----kerberoasting.md - technique files, what it is, my commands and output
|-----asrep-roasting.md
|-----bloodhound-enums.md
|-----dcsync.md
```

# Templates

## _log.md file

### Raw template

```
## <date>

**What I worked on:** <technique I worked on and where>
**What happened:** <What did I do on the attack and got to the goal>
**What worked:** <What worked without problems>
**Points to strengthen and shortcuts I took:** 
- **Shortcut:** <shortcut I took>
  - **Point:** <point to strengthen>
**What I didn't understand:** <Stuff that didn't work or I didn't understand and needs better sharpening>
```

### Example template

```
## 2025-07-20

**What I worked on:** Kerberoasting on DC01
**What happened:** Got the hash for svc_backup, cracked it with hashcat in ~2 min
**What worked:** Getting the hash
**Points to strengthen and shortcuts I took:**
- **Shortcut:** Added the password for the service user manually to the wordlist
  - **Point:** Need to learn how to get my way around when the desired password isn't in the wordlist, your knowledge while hacking is only what you have already found/cracked.
**What I didn't understand:** Why svc_sql hash didn't appear - need to check SPN config
```

## Technique files

### Raw template

```
# <Technique name>

## What it is
One sentence explanation in my words

## When to use it
What conditions need to be true on the target.

## Commands 
Exact commands I ran, with my output pasted in

## What to do with the output
Next step after getting a hash / result

## Gotchas
Things that went wrong or were confusing at first and I got them.
```
