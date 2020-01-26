
# introduction

## HTTP protocol basics

Hypertext transfer protocol (HTTP) is the basic protocol used for web browsing. This is text based protocol running on TCP.

The TCP connection is established and request, response messages are sent using the following format over the connection.

```
HEADERS\r\n
\r\n

MESSAGE BODY\r\n
```
key:
* \r Carriage return moves the cursor to the beginning of the line
* \n Line feed move the cursor down to the next line
* \r\n is the same as pressing `enter` on your keyboard

## An example http request
```
GET / HTTP/1.1
Host: www.duckduckgo.com
User-Agent: Mozilla/5.0...
Accept: text/html,application/xhtml+xml
Accept-Encoding: gzip, deflate
Connection: keep-alive
```

Some HTTP verbs are...

* GET
* POST
* PUT
* DELETE
* OPTIONS
* TRACE

/ represents the path on the server. This could be something like `/some_file.txt`

The HTTP protocol version is then specified at 1.1 which means that using the `keep-alive` connection header the server maintains the duplex connection. HTTP 1 creates a connection for each request/ response.

The Host header allows a web server to host multiple websites at the same ip address.

## An example http response

```
HTTP/1.1 200 OK
Date: <date>
Cache-Control: private, max-age=0
Content-Type: text/html; charset=UTF-8
Content-Encoding: gzip
Server: gws:google web server
Content-Length: 256

<page content after carriage return>
```
HTTP/1.1 RFC : http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.1

Example status...

* 200 OK
* 301 - Moved permenently
* 302 - Found - at another URI
* 403 - Forbidden
* 404 - Not Found
* 500 - Internal server error

# HTTPS

HTTP is a plaintext protocol, that is too say that it can be read easily. It does not authenticate the communication between either end of the socket.

HTTPS of HTTP Secure over SSL/TLS is a method to run HTTP over SSL/TLS cryptographic protocol. This layering technique provides confidentiality, integrity, protection and authentication too the HTTP protocol.

When using HTTPS:

* An attacker on the path cannot sniff the application layer communication
* An attacker on the path cannot alter the application data
* The client can tell the real identity of the server and sometimes, vice-versa.

HTTPS does not protect against web application flaws it only adds an encryption layer between the client and server. XSS & SQL injection are not affected by HTTPS.

## Encoding

Information encoding is a critical component of IT. Its main function is to represent the low-level mapping of information being handled.

The *symbol* is what the user reads on the screen.

The *code point* is a numeric index used to distinguish the symbol within the character set.

Examples charsets are ASCII, Unicode, Latin-1.

## ASCII (American standard code for information interchange)

This charset contains a small set of symbols. Originally it was 128 only, but now is usually defined with its extended version for a total of 255.

ASCII cannot be used to display Chinese symbols among many others.

http://www.ascii-code.com/

## Unicode (universl character set)

This charset supports language systems from entire world.

http://unicode-table.com/en/#0032

## Charset vs. Charset Encoding

Character encodings (or simply encoding) is a representation of bytes, of a symbols of a charset: a mapping of the symbols to a series of ordered bytes.

A symbol can be represented by using one or more bytes.

Unicode has three main types of implementation of character encodings...

* UTF-8
* UTF-16
* UTF-32

Where UTF stands for unicode transformation format.

eg.
```
symbol | unicode | utf-8     | utf-16 | utf-32
!        U+0021    21          00 21    00 00 00 21
W        U+0057    57          00 57    00 00 00
#        U+2B80    E2 AE 80    2B 80    00 00 2B 80
```

## HTML encoding

According to [HTTP 1.1 RFC](https://tools.ietf.org/html/rfc2616), documents transmitted via HTTP can send a charset parameter in the header to specify the character encoding of the document sent. This is the HTTP `Content-Type`.

```
<meta charset="UTF-8">
```

If not defined, the RFC defines the default charset as `ISO-8859-1` "8-bit single-byte coded graphic character sets" aka latin 1.

Setting an incorrect or omitting the charset may cause the browser to display symbols incorrectly.

## HTML entities

```
<h1></h1>
<img />
```

If you want to show symbols in your web document and avoid the symbols being interpreted *as code*... an html entity is simply a string staring

* &
* &#

and ending with ;

When the browser encounters an entity within a HTML page will show the symbol to the user and not interpret the symbol as a HTML language element.

## URL Encoding (Percent encoding)

As stated in [RFC 3986](http://tools.ietf.org/html/rfc3986#section-2.1), URLs sent over the internet must contain characters in the range of US-ASCII code character set. If unsafe characters are present in the URL, encoding them is required.

* unreserved chars: `[a-zA-z][0-9][-._~]`
* reserved chars: `:/?#[]@!$&"()*+,;=%"`

```
character | purpose in URI          | encoding
#           separate anchors          %23
?           separate query String     %3F
&           separate query elements   %26
+           indicate a space          %2B
```

When you visit a site, URL-encoding is performed  automatically by your browser.

[URL-encoding reference](http://tools.ietf.org/html/rfc3986#section-2.1)

## Base64

Base64 is a binary-to-text encoding schema used to convert binary files to send them over the internet. For example email protocols make use of the encoding to attach files to messages.

The HTML language permits the inclusion of some resources by using this encoding. For example an image can be converted to bas64 and rendered via html... eg `<img src="data:image/png,base64,zzzzzzzz"`

The alphabet of the Base64 encoding scheme is composed of digits [0-9] and Latin Letters, both upper and lower case [a-zA-Z], for a total of 62 values. To complete the character set to 64 there are `+` and `/` characters.

Different implementations however may use other values for the latest two characters and the one used for padding `=`.

## Same Origin Policy

CSS stylesheets, images and scripts are loaded by the browser without consulting the policy.

Same Origin Policy (SOP) is consulted when cross-site HTTP requests are initiated from within a client side scripts.

The origin is defined by the following triplet:

Protocol        Host        Port

For example:

```
http://www.elswapt.site:80
```

* site is the top level domain (TLD)
* elswapt is the second level domain (SLD)
  - the subdomain of site
* www is the third level domain
  - subdomain of elswapt

## Example origins

```
URL                                      |  SOP  |       Reason
http://els.wapt.site/admin/index.php        x           works, same host and port
https://els.wapt.site/index.PHP             o           fails, different port
http://els.wapt.site/index.php:8080         o           different port
http://www.els.wapt.site/index.php          o           diffrent host

```

Content from `about:blank`, `javascript:` and  `data:` inherits from the origin.

On internet explorer it works a bit differently.

* port is not considered a component of SOP
* trust zone: SOP is not applied in a highly trusted zone

# Why SOP?

The same origin policy (SOP) prevents javascript running on a given origin from interacting with a document from a different origin. The primory purpose of SOP is to isolate the requests coming from different origins.

# Exceptions to SOP

* window.location
* cross window messaging
* document.domain
* cross origin resource sharing (CORS)

A document can always write the location propery of another document.

# Cookies

in 1994 Netscape invented cookies to make HTTP a statefull protocol. At the time HTTP was stateless and had no way to manage user sessions.
```
cookie jar
name=value

domain ,
expires
path
content
http only flag
secure flag
```
http://tools.ietf.org/html/rfc6265

Set-Cookie header
Cookie header


# Sessions

Store a session token client side and reference it on the server to keep track of users. eg.
```
SESSION=xxxx
PHPSESSID=xxxx
JSESSIONID=xxxx
```

# References

* http://www.w3.org/TR/uri-clarification/
* http://httpd.apache.org/docs/2.2/ssl/ssl_intro.html
* http://tools.ietf.org/html/rfc6265
* http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html
* http://www.tcpipguide.com/free/t_HTTPOverviewHistoryVersionsandStandards.htm
