# **Army6**

> 
> ## Difficulty: easy
> **user:** **PWNX{ff32556d870a49617c8ae1f6ad5e8735}**
> 

## Description

Insecure application showing user's username and password hashes.

## **Technical Description**
SQL injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. It generally allows an attacker to view data that they are not normally able to retrieve. This might include data belonging to other users, or any other data that the application itself is able to access. In many cases, an attacker can modify or delete this data, causing persistent changes to the application's content or behavior. A successful SQL injection attack can result in unauthorized access to sensitive data, such as passwords, credit card details, or personal user information. Many high-profile data breaches in recent years have been the result of SQL injection attacks. In some cases, an attacker can obtain a persistent backdoor into an organization's systems, leading to a long-term compromise that can go unnoticed for an extended period. SQL Injection has become a common issue with database-driven web sites. The flaw is easily detected, and easily exploited, and as such, any site or software package with even a minimal user base is likely to be subject to an attempted attack of this kind. Essentially, the attack is accomplished by placing a meta character into data input to then place SQL commands in the control plane, which did not exist there before.


## **Writeup**
A SQL injection vulnerability mainly exists because the vulnerable application doesn't uses prepared statements, for this reason if an user makes a malicious request to the application that request is forwarded to the database as it is and in many cases the user could receive a response or an error message relative to its request, confirming or revealing the issue.
More (https://en.wikipedia.org/wiki/Prepared_statement)

This SQL Injection lab is an example of this cases. The user here receives a direct response from the database with the data from it. The application presents a table with two columns, username and password, and a input field that should behave like a search box. This search box becomes instead our vector to the SQL Injection payloads. Making a requests with a `single quote mark` symbol, no error response come back, but the page seems to be bugged or something is wrong with the page, because the HTML code after the table disappears. Trying the most classical SQL Injection payloads we receive some data back including the lab flag.

The steps to reproduce the exploit are the following:
1. Make an invalid request to see the behaviour of the Web Application
2. Try different type of payloads until the flag is gave back from the SQL query.

Exist a couple of valid payloads to complete this lab.

```sql
' OR 1 = 1 -- -
```

```sql
' OR 1 -- -
```

This payloads in an example query changes it significantly. For example:

```sql
SELECT username,password FROM vulnusers WHERE username = '' OR 1 = 1 -- - ' AND password = '';
```

```sql
SELECT username,password FROM vulnusers WHERE username = '' OR 1 -- - ' AND password = '';
```

In this case the sql injection causes also a Login Bypass issue. As you can see the user input included in the SQL query makes the query return a valid response (`true`) in any case. Translating this query in simple words is: "Give me username AND password if the username is equal to &lt;NOTHING&gt; OR TRUE (1 = 1 is always true)", then the two consecutive dash symbols (`--`) stands for comments in the SQL query language, so anything after that is considered a comment and not evaluated by SQL.
