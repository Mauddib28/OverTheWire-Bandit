# OverTheWire-Bandit
Walk-through guide with examples for the OverTheWire Bandit game

## Level 00
**Needs:** Web browser + puTTY

User/Pass: bandit0
Connect: *bandit.labs.overthewire.org*

## Level 01
**Needs:** Notepad (for storing found passwords)

## Level 02
**Needs:** Web browser (for searching for information)

## Level 03

## Level 04

## Level 05
maybehere07/.file2

## Level 06
*File properties:* owned by `bandit7` (user) & `bandit6` (group) and 33 bytes in size
```
find / -type f -size 33c -exec ls -lh {} \; 2>/dev/null
```

*Pass:* `/var/lib/dpkg/info/bandit7.password`  (Odd note: `-perm`)

## Level 07
**Needs:** Pipes & Redirects

```
grep "millionth" data.txt
```

## Level 08
Password is in `data.txt`
* only line that occurs once

```
sort data.txt | uniq -u
```

## Level 09
Password is in `data.txt`
* one of few human readable
* beginning with serveral "=" characters

```
strings data.txt | grep "="
```

## Level 10
Password is in `data.txt`
* contains base64 encoded data

```
base64 -d data.txt
```

## Level 11
Password is in `data.txt`
* all lowercase (a-z) & uppercase (A-Z) letters rotates by 13 positions
ROT13 == `[A-Z]`->`[N-ZA-M]`

```
tr [a-z] [n-za-m] < data.txt | tr [A-Z] [N-ZA-M]
```
**OR**
```
tr [a-zA-Z] [n-za-mN-ZA-M] < data.txt
```

## Level 12
Password is in `data.txt`
* hexdump of a file that has been repeatedly compressed
*Hint:* create `/tmp` directory, `cp` file and rename
* Read `hexdump` on wikipedia

itOS - 67 bytes

```
hexdump -n 8 -c
```
Use the above code to look for a magic number at the start of the file

Use '`file`' to check compression information

## Level 13
Password stored in `/etc/bandit_pass/bandit14`
* Only readable by `bandit14`

**Note:** Aim to get private SSH key
* localhost is hostname

```
ssh -i sshkey.private -l bandit14 localhost
```

## Level 14
Password retrieved by submitting level 14 password to port 30000 on localhost

```
telnet localhost 30000
```
* pass level 14 password

## Level 15
Password retrieved by submitting level 15 password to port 30001 on localhost using SSL encryption
**Note:** Use `-quiet` and read "Connected Commands"

```
openssl s_client -connect localhost:30001
```

**Nota _Bene_:** Can not start line with '`R`', '`Q`', or '`B`' because the connection will be closed
* Add space to start

## Level 16
Password retrieved by submitting level 16 password to port on localhost in range of 31000 to 32000
  1. Find ports that are listening
  2. Find which do/don't do SSL
**Note:** Only 1 server will give next password, others parrot what is sent

```
nmap -A -p31000-32000 localhost
```
Ports | Service
------|--------
31046 | echo
31518 | msdtc <- SSL <- echos
31691 | echo
31790 | msdtc <- SSL
31960 | echo
(Get RAS private key)
