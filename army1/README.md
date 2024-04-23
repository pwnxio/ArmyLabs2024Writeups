# Army1 - Welcome Challenge

> 
> ### user: PWNX{D28D63D4BBB57F691B71A8DEFED0DF8F} 
> 

## Description

A simple server containing a strange audio file.

## Technical description

The challenge presents an audio file that reproduces the morse code of the flag.

## Writeup

The challenge presents the port `22/tcp (SSH)` open. On connection a user reads the lab banner and username and password needed for the SSH login. Once on the box the user can `scp` the audio file and decode the flag.
