

# information gathering

Information gathering is the very first and most critical step of every security audit.

Regardless of the scope of the test you must aim to know your targets in as much detail as possible.

What kind of information are we looking for?

* infrastructure
* application logic
* IP, Domains and subdomains
* Virtual hosts

## WHOIS

Look for domain name ownership from various databases. From cli or web based services. The WHOIS service normally runs on TCP port 43.

We may find...

* administrative contact information
* technical contact information
* IP addresses

## DNS

The domain name system (DNS) is a distributed database arranged hierarchically.

It provides an abstraction layer between internet servcies and their numeric ip addresses.

* permits the use of names instead of numbers to represent hosts
* names are easier to remember
* it permists the server to change their numberic address without requiring notification of everyone on the internet.
* one name can rever to multiple hosts


## Nslookup

Is a tool that translates hostnames to IP addresses and vice versa.

```
nslookup duckduckgo.com
nslookup -type=PTR 111.11.11.11.1 # reverse lookup
nslookup -type=NS duckduckgo.com # nameserver

```

## Finding the targets ISP

Using this DNS information try to discover the ISP and netblock that the target belongs too. An example netblock might look like:

```
104.16.0.0./12
```

## Infrastructure

Some common web infrastructure are Apache and Microsoft IIS. Discovering what kind of server is running may indicate the underlying operating system of the server.

For example IIS (Internet Information Server) will indicate the server is running Windoes Server.

## Fingerprinting the webserver

Sometimes the webserver will tell us what is via the response header...

```
HTTP/1.1 200 OK
Date: NOW
Server: apache
Content-length: 80
```

## Fingerprinting tools

* netcraft [website]
* netcat
* httprint
* whatweb
* wappalyzer

## netcat

```
nc <target> <port>
HEAD / HTTP/1.0

HTTP/1.1 200 OK
Date: NOW
Server: demo
X-Powered-By: ASP.NET
Content-length: 80
...
```

## Cookies and session information

Cookies may reveal useful informatin about the target or the session management used by the web application.

```
Server           |            Cookie

PHP                           PHPSESSID=XXXX
.NET                          ASPSESSIONIDYYYY=XXXX
JAVA                          JSESSION=XXXX
```

* https://www.owasp.org/index.php/Testing_for_Web_Application_Fingerprint_(OWASP-IG-004)
* https://www.owasp.org/index.php/Fingerprint_Web_Application_Framework_(OTG-INFO-008)

## whatweb

Whatweb can recognise website technologies used by a website.

```
whatweb -h
```

## URL rewriting

URL re-writing is sometimes handled with apache using `.htaccess` and the `mod_rewrite` module. On IIS its handled by `Ionic Isapi Rewrite` or `Helicon Isapi Rewrite`.

## Enumerating subdomains

* Netcraft :: `search sudomain matches \*.target.com`
* google :: `site:.target.com -site:www.target.com`
* Crawling / brute force
* other tools :: `dnsrecon, subbrute, fierce, nmap, dnsenum, knock, recon-ng`
* zone transfers

## Map the attach surface

Organise information using graphs and other data visualization methods.

* client side validation
* database interaction
* file uploading and downloading
* display of user supplied data
* redirections
* access controls and protected pages
* error messages
* charting

## Enumerate resources

* website spider
* DirBuster

Look for backup files:

* bak
* bac
* old
* 000
* ~
* 01
* \_bak
* 001
* inc
* xxx

## Enumerating usernames

If the login page gives detailed errors for example, saying if the wrong username was used. Enumeration of usernames may be possible by guessing or using a dictionary of usernames.

## Server misconfiguration

If we know the web application running and that app has open source components or is fully open source we can make assumptions. For example we can look for backup configuration files like: `config-backup.old.php`  and other mistakes. Logs and configuration files may yield sensitive information including data base credentials.

## Open directory listing

Some directories may be traversable and show the folder structure and other files on the server.

## Check HTTP Verbs and file upload

Misconfigured directories may be *writable* with the HTTP *PUT* verb allowing one to potentially upload an arbitrary file onto the server.

Check for the PUT verb using OPTIONS...

```
OPTIONS / HTTP/1.1
Host: target.com

<response>
Allow: OPTIONS, TRACE, HEAD, PUT...
```

> Interesting aside... using *HEAD* returns only the response header, try testing restricted routes with HEAD to see if you get different behaviour or can bypass restrictions.

If *OPTIONS* is not available you can still try using *PUT* to upload a file but you may encounter a 4xx or 5xx error code as the server response.

Uploading a file...
```

PUT /writable_dir/test.html HTTP/1.1
Content-length: 184
<RETURN>

[CONTENT OF TEST.HTML]
<RETURN>
<RETURN>
```

If the upload is successful the server will respond with a `201 Created` message.

## Also use

* google dorks
* shodan

# References

* http://technet.microsoft.com/en-us/sysinternals/bb897435.aspx
* http://whois.arin.net/rest/net/NET-108-162-192-0-1/pft
* http://www.netcraft.com/
* https://github.com/urbanadventurer/WhatWeb
* http://whois.domaintools.com/
* https://apps.db.ripe.net/search/query.html
* http://www.iis.net/
* https://wappalyzer.com/
* https://www.modsecurity.org/
* https://www.owasp.org/index.php/Fingerprint_Web_Application_Framework_(OTG-INFO-008)
* https://github.com/darkoperator/dnsrecon
* https://github.com/davidpepper/fierce-domain-scanner
* https://www.owasp.org/index.php/Testing_for_Web_Application_Fingerprint_(OWASP-IG-004)
* http://www.googleguide.com/advanced_operators_reference.html
* http://www.googleguide.com/advanced_operators_reference.html
* https://github.com/TheRook/subbrute
* http://nmap.org/book/man-host-discovery.html
* https://code.google.com/p/dnsenum/downloads/list
* https://github.com/laramies/theHarvester
* http://portswigger.net/burp/download.html
* https://github.com/guelfoweb/knock
* https://bitbucket.org/LaNMaSteR53/recon-ng/wiki/Usage%20Guide
* http://web.archive.org/web/20140207074603/http:/www.hotscripts.com/
* https://members.elearnsecurity.com/course/resources/name/wapt_v2_section_1_module_3_attachment_eLearnSecurity_Handling_Information
* https://www.exploit-db.com/google-hacking-database/
* http://www.shodanhq.com/
* http://www.googleguide.com/advanced_operators.html
* http://www.shodanhq.com/help/filters

