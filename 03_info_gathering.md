
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
```
