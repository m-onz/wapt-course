
# introduction

# HTTP Protocol basics

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

# Encoding
