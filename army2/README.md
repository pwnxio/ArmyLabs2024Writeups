# Army2

>
> ### user: PWNX{0974dea602e6534ba6898697f321a84e}
> 

## Description

A simple SSH server. The pwnx user home directory contains a file with unreadable text.

## Technical description

The challenge presents a `flag.txt` file containing the flag in brainf*ck format rotated of 13 characters.

## Writeup

The challenge presents the port `22/tcp (SSH)` open. On connection a user reads the lab banner and username and password needed for the SSH login. Once the user connects, in the pwnx user home directory can find the `flag.txt` file containing the following brainf*uck code:

```text
++++[++++>---<]>.+++++++.---------.++++++++++.+[->+++++<]>-.-[-->+++++<]>-.+++++++++.--.---.+[----->+<]>.+.----.[-->+<]>-.------.++.--[----->+<]>++.[-->+<]>---.-.--.+.+++[->++<]>+.-.[-->+<]>-.++.+.-.--.+++.--.++[->++<]>+.>-[----->+<]>.-.-.-[----->+<]>--.[-->+<]>+.----.+++++[->++<]>.+++++++++++.
```

Using any [BrainF*ck decoder online](https://www.dcode.fr/brainfuck-language) the user obtains the flag rotated of 13 characters:

```text
CJAK{0974qrn602r6534on6898697s321n84r}
```

To rotate the flag without too much effort the user can use the [ROT13](https://rot13.com) utility online, which will give to the user the final flag


```text
PWNX{0974dea602e6534ba6898697f321a84e}
```
