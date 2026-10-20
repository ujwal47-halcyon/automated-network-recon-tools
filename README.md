# Python security scripts

Small scripts I wrote while learning networking and security. They are learning projects, not finished tools. I only run them on my own machines and on test data.

## What's in here

**port-Scanner/port-Scanner.py**
A threaded TCP connect scanner. Give it an IP and a port range, and it prints which ports answer, names the service for 12 common ports, and tries to read a banner. `-r` saves the result to a text file.

```
python3 port-Scanner/port-Scanner.py 127.0.0.1 -s 1 -e 1024 -t 100 -r
```

**log-Analyser/log-analyser.py**
Reads an Apache/Nginx combined-format access log and flags IPs with 10 or more failed logins, 20 or more requests, or 15 or more 404s. `Sample.log` is there to try it on. A flag only means "look at this", not "attack".

**hash-Cracker/hash-Cracker.py**
Hashes every word in a wordlist and compares it to a target hash. It asks for the algorithm (md5, sha1 or sha256), the hash and the wordlist path, and defaults to `rockyou.txt`. Only for hashes I made myself.

**Wordlist-Generator/wordgen-1.py**
An interactive wordlist generator. It asks for names, dates and numbers, builds leetspeak, case and date variants from them, and saves the list to a file.

**Hackathon/**
My CTF cheat sheet for the Firewall '26 competition.

## Known problems

- The port scanner swallows every error with a bare `except`, so a failed scan looks the same as a closed port.
- The port scanner reads each banner on a second connection and only uses IPv4.
- The log analyser counts requests per IP over the whole file. It has no time window, so a slow steady client and a burst look the same.
- The hash cracker is interactive only and takes no command-line arguments.
