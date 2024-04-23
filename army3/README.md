# Army3

> 
> ### user: PWNX{f0b959378b199e0272149cb008e9fa5d}
> 

## Descritpion

A simple SSH server with an extra message in the banner.

## Technical description

A simple challenge where the attacker needs to bypass the rbash shell restrictions.

## Writeup

The challenge presents the port `22/tcp (SSH)` open. On connection, a user reads the lab banner and username and password needed for the SSH login. In the banner the user who tries to connect also sees a message informing him of where the flag is located on the system, this is because on a successful SSH connection, the user lands in a `rbash` environment and most of the commands useful to read files have been restricted, so the attacker has to find a way to read the file.

To achieve this goal there are multiple ways, one of them is the following:

```bash
$ echo $(</flag.txt)
PWNX{f0b959378b199e0272149cb008e9fa5d}
```
