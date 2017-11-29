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

## Level 17
Use ssh from level 16 using RSA key

**Note:** `chmod 600 id_rsa`
* `ssh -i id_rsa -l bandit17 localhost`

```
sdiff -s passwords.new passwords.old
```

## Level 18
**Note:** `.bashrc` modified to log me out when logging in with SSH
* Due to level 19

```
ssh -l bandit18 localhost cat readme
```
* The command run after connecting is `cat readme`
* Could use '`-t`' to then open `/bin/sh` to get terminal

## Level 19
Use `setuid` binary in home directoy
Password can be found in `/etc/bandit_pass` **after** having used the `setuid` binary

```
./bandit20-do cat /etc/bandit_pass/bandit20
```

## Level 20
`setuid` binary in the home directory
* makes connection to localhost on port specified as commandline argument
* then reads line to text from connection & compares it to password in previous level (`bandit20`)
* if password correct, transmits pass for `bandit21`

**Note:** Need to login twice
  1. Run `setuid` command
  2. Start network daemon to which `setuid` will connect

**Note:** Try connecting to own network daemon to see how it orks

**Port:** 4312		**Service:** rwhois (interesting)

```
./suconnect < $(nc -l 4828)
```

**Send:** `cat /etc/bandit_pass/bandit20 | nc localhost 4828`

Echo with `ncat`
* `ncat -b -l -p 4828 -c 'while true; do echo $(cat /etc/bandit_pass/bandit20); done'`
1. `nc -l 4828` + `$ bandit pass lvl20`
2. `./suconnect 4828`

## Level 21
`cron job`; examine `/etc/cron.d`

**Note:** Examine who has owner/group privilege for what files

`test` - check file types and compare values
* `-x FILE` => FILE exists & execute (or search) permission is granted

```
test -x /usr/sbin/cron-apt && /usr/sbin/cron-apt /etc/cron-apt/config2
```
* Does *not* go anywhere

```
/usr/bin/cronjob_bandit22.sh &> /dev/null
```
* Shows cat of pass to temp file

## Level 22
`cron job`; examine `/etc/cron.d/`
Examine `/etc/cron.d/cronjob_bandit23`

```
/usr/bin/cronjob_bandit23.sh &> /dev/null
```

`$(echo  I am user bandit23 | md5sum | cut -d' ' -f 1)`
* file name for tmp file that holds `bandit23` password

## Level 23
`cron job`; examine `/etc/cron.d/`

**Note:** Will need to write own shell-script
* **Nota Bene:** Shell script removed once executed, therefore keep copy around

Examine `/etc/cron.d/cronjob_bandit24`

```
/usr/bind/cronjob_bandit24.sh &> /dev/null
```
* Executes & deletes al scripts in `/var/spool/$(whoami)`

`for file in *`
* Bash performs filename expansion on epxressions that globbing recognizes
If the `[list]` in a for loop contains wild card (\* and ?) used in filename expression, then globbing takes place
**Note:** Strings containing \* will not match filenames that start with a dot
* The "\*" in the previous for loop expands into a list of all files in the current directory

**Note:** There is no `/var/spool/bandit23` **BUT** there is a `/var/spool/bandit24`
* How to run as `bandit24`???

```
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /home/bandit23/pass.txt
```
(If not make file with password as name

`&>` redirects both `stdout` & `stderr`
**Nota Bene:** Cronjob is able to write to `/tmp/` not able for `bandit23` owned directories

## Level 24
Port listening on 30002 and will give you password for `bandit25`
* Need to give `bandit24pass` + 4 digit code

**Note:** No way to retrieve passcode, needs to brute-force

Most likely needs script to attempt all 10,000 combinations (separate passbandit24 & passcode by space)

Use modifiers? - "`%#04d`"
Use `seq` command
* `-w` flag - equalize width by padding with leading zeros

Make the following in `/tmp/`
```
#!/bin/bash

B24PASS=$(echo "bandit24pass")

for i in $(seq -w 9999); do
	echo "$B24PASS $i" | nc localhost 30002
done
```

`scriptRun | nc localhost 30002 > localfile.txt`
* Then look through for bandit25 pass???

## Level 25
Need to determine shell used by `bandit26`
* Not `/bin/bash`

**Note:** Figure out how to log onto shell via ssh

**Idea:** Look through `/etc/passwd` (password) file to determine shell attached to user `bandit26`
* bandit26 -> `/usr/bind/showtext`

`/usr/bin/showtext` calls `more` on file `~/text.txt`
* uses `#!/bin/sh` <= different shell**!!!**

1. Get more in a restricted window (e.g. get the `--More--`)
2. Press the '`h`' key to enter the help menu
3. Use `!<cmd>` or `:!<cmd>` to execute `<cmd>` in subshell
	* Try `cat /etc/bandit_pass/bandit26`

[ ] Attempt to write to `/tmp/` file??
* *Failed*

1. Press '`v`' to open an editor
2. Use `:r /etc/bandit_pass/bandit26`
3. Password should now appear on the current line in the editor
	* Essentially replacing the current line with the contents of `/etc/bandit_pass/bandit26`

## Level 26
At this moment level 27 does not exist
