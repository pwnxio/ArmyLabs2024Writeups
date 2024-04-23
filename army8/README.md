# Army8

>
> ### user: PWNX{cbfd5b0c0e4ccf3e8c52f8b6aa208473}
> 

## Description

A simple HTTP and HTTP(S) Server

## Technical description

The Web server discloses the flag in the SSL Certificate of a child vhost

## Writeup

The Challenge exposes two ports:

- 80/tcp
- 443/tcp

When connecting to one of the exposed services, the client gets redirected to `https://army8.pwnx.io`. There the attacker is invited to enumerate. Once the attacker finds the correct vhost, checking the SSL certificate, the attacker is able to obtain the challenge flag.

```bash

$ ffuf -u https://10.90.1.169/ -H "Host: FUZZ-army8.pwnx.io" -w /usr/share/seclists/Discovery/Web-Content/common.txt -fs 990

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : https://10.90.1.169/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Header           : Host: FUZZ-army8.pwnx.io
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 990
________________________________________________

flag                    [Status: 200, Size: 1002, Words: 37, Lines: 26, Duration: 86ms]
:: Progress: [3012/4715] :: Job [1/1] :: 86 req/sec :: Duration: [0:00:30] :: Errors: 0 ::
```

At this point to read the flag the attacker has to add `flag-army8.pwnx.io` in its hosts file, and read the SSL certificate with the following command

```bash
$ openssl s_client -connect flag-army8.pwnx.io:443 | openssl x509 -noout -text

depth=0 C = IT, ST = Rome, L = Italy, O = PWNX, OU = Army7, CN = Army, emailAddress = PWNX{cbfd5b0c0e4ccf3e8c52f8b6aa208473}
verify error:num=18:self-signed certificate
verify return:1
depth=0 C = IT, ST = Rome, L = Italy, O = PWNX, OU = Army7, CN = Army, emailAddress = PWNX{cbfd5b0c0e4ccf3e8c52f8b6aa208473}
verify return:1
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            18:95:ba:c7:7c:ce:11:38:1c:47:aa:2a:dd:96:cc:cd:f0:27:96:cc
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = IT, ST = Rome, L = Italy, O = PWNX, OU = Army7, CN = Army, emailAddress = PWNX{cbfd5b0c0e4ccf3e8c52f8b6aa208473}
        Validity
            Not Before: Mar 26 07:55:43 2024 GMT
            Not After : Mar 26 07:55:43 2025 GMT
        Subject: C = IT, ST = Rome, L = Italy, O = PWNX, OU = Army7, CN = Army, emailAddress = PWNX{cbfd5b0c0e4ccf3e8c52f8b6aa208473}
        ...

```
