# **Army7**

> ## **Difficulty: easy**
> **user:** **PWNX{5d269e091ce9d15d872771ac7d3a7114}**
> 

## Description

A simple Web Application that allows users to read maybe too many things.

## **Technical Description**
Local file inclusion (also known as LFI) is the process of including files, that are already locally present on the server, through the exploiting of vulnerable inclusion procedures implemented in the application. The File Inclusion vulnerability allows an attacker to include a file.. The vulnerability, depending on the severity, may also allow an attacker, in addition to reading internal system files, to execute code on the vulnerable application and / or cause Denial Of Service.
More (https://en.wikipedia.org/wiki/File_inclusion_vulnerability)

## **Writeup**
A Local File Inclusion vulnerability occurs due to the use of user-supplied input without proper validation and a system of dynamic file inclusion implemented by the vulnerable application. An attacker who exploits this vulnerability can obtain sensitive data about the vulnerable application, such as credentials, configuration files, and other sensitive data. Worst scenario is that where the attacker via a Local File Inclusion Vulnerability gains Remote Code Execution allowing him to can take over the entire vulnerable application.

This Local File Inclusion lab isn't the worst case but doesn't seems to have any filter on the user input and the user can include any file in the system without restrictions. The attacker knows that the flag is under the /opt/ folder in a file named `flag.txt`. 

To exploit this lab and obtain the flag an attacker just needs to type the following payload and the content of the file `flag.txt` will be included in the HTML code.

```ascii
/opt/flag.txt
``` 
