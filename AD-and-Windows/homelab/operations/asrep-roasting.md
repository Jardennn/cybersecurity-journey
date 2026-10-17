# AS-REP roasting

## What it is

AS-REP roasting is an active credential harvesting attack that exploits misconfigurations and configuration weaknesses in Active Directory.

The misconfigured setting is disabling kerberos pre-auth for the specific user, it works by asking the KDC for an AS-REP response without needing a password, then a part of the AS-REP response can be formatted into a crackable hash to then crack offline the password with hashcat.

The tool that mainly does the work here is `GetNPUsers` in the impacket suite, it queries the server for users that have kerberos pre-auth disabled using a wordlist or singular user to request a TGT (ticket granting ticket) (via an AS-REQ), takes the resulting AS-REP message and formats the relevant encrypted portion to a hash, cracking with hashcat is done with passing the hash in a text file and the `-m 18200` to mark it as a AS-REP response.

## When to use it

- When there is a user with pre-auth disabled

## Commands

Querying the server with a wordlist for vulnerable users, requesting a TGT for the users in the list.
```bash
impacket-getnpusers lab.local/ -dc-ip 192.168.100.10 -no-pass -usersfile /usr/share/seclists/usernames/names/names.txt
```

Requesting a TGT as a specific user
```bash
impacket-GetNPUsers lab.local/ajones  -dc-ip 192.168.100.10 -no-pass
```

Cracking the found hash (hash passed into `hash.txt`)
```bash
hashcat -m 18200 hash.txt /usr/share/wordlists/seclists/Passwords/seasons.txt
```

## What to do with the output

With the TGT requesting commands, if everything went correctly and you got a hash you would want to copy that hash onto a text file to store that hash.

With the hashcat output, if everything went correctly and it shows "cracked", then you could find the password separated beside the hash with a `:`. Example: `hash:password`.

