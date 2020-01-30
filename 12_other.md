
# other

## Clickjacking

Clickjacking is a technique, also known as UI redressing, where and attacker tricks a victim into clicking on a resource that is different from what they actually intended to click on.

This attack relies on legitimate browser features to allow, layers, overlapping elements and changing opacity.

Technically an attacker crafts a malicious HTML page. This page can contain two overlapping layers, one malicious and the other the actual target of the click.

The malicious layer is shown to the user and has the innocent looking thing to click on. The target layer is invisible and is what will be clicked on.

## Attack steps

* feasibility study
* build a malicious web page
* spread the malicious link
* wait for a victim to click

## Feasibility study

Create a HTML page..

```
<html>
  <body>
    <iframe id="theFrame" src="[TARGET]"></iframe>
  </body>
</html>
```

Add some CSS to make it look correct:

```
#theFrame {
  width: 100%;
  height: 600px;
  border: none;
  position: absolute;
  top: 0px;
  left: 0px;
}
```

If the target website is visible within the frame then the attack *can* be performed. If its not visible that means any number of defence mechanisms has been used.

## Building a Malicious Web Page

* Malicious layer is placed under target layer using z-index
* Opacity can be used to hide the target layer

## Remediation

From within an iframe js method to stop the document being shown in an iframe.

```js
if (top != window) {
  top.location = window.location
}
```

This is not foolproof! The top level window can intercept this change using `onbeforeunload`.

Other modern defences.

* X-Frame-Options header
* Content Security Policy
* Browser Frame Breaker

## X-Frame Options

Modern browsers support the *X-Frame-Options* header. This header is sent over HTTP to prevent a document from being shown inside an iframe.

* same origin ::
  - the attacker must use a page on the same origin as the target site
* deny ::
  - this options blocks being loaded via an iframe

Note that the browser will ignore the *X-Frame-Options* header if the loaded document containts the following meta tag:

```
<meta http-equiv="X-Frame-Options" content="deny">
```

Also see *cursor jacking*

http://javascript.info/tutorial/clickjacking#defences-and-the-ways-to-break-through
http://web.archive.org/web/20170119181502/http:/w2spconf.com/2010/papers/p27.pdf

## HTTP Response splitting

https://www.owasp.org/index.php/HTTP_Response_Splitting

This type of *Header Injection* attack occurs when an attacker sends arbitrary data to the server, and this data is returned as part of the HTTP response.

The attacker is able to split the HTTP response into parts using

* CR (Carriage Return) - [ \r ]
* LF (Line Feed) - [ \n ]

The difference between this attack and a *Header Injection* attack is that we are not only injection data into the headers but we are also creating a new HTTP Response block with new headers!

A Header Injection attack would add new Headers to the current HTTP Response block.

## Typical Vulnerable Scenario

Using the encoded forms of CR and LF %0d and %0a:

```
GET
http://target.site/t.php?url=http://els.com%0d%0aHTTP/1.1%20200%20OK%0d%0aContent%2dTypeL%20Text/html%0d%0aContent%2dLength:%2028%0d<html><h1>defaced</h1></html>
```

An XSS payload could be used instead....

```
<script>alert(document.cookie)</script>
```

## Bypassing Same Origin Policy

HTML5 has introduced new origin headers to extend the origin concept. An attacker can inject these headers into the response and access the response body via javascript.

```
Set-Cookie: vulnParam=http://x.com
Access-Control-Allow-Origin: <attackerSite>
Access-Control-Allow-Credentials: true
```

## Remediation

Filter out...

* `CR` (Carriage Return) - [ \r ]
* `LF` (Line Feed) - [ \n ]

* their encoded forms:

* `%0d`
* `%0a`

From user input.

Before PHP v5.1.5 you must explicity filter special characters from all user input.

## Denial of Service

A denial of service attack...

* too many requests
* use up all available resources
* malicious requests
* clogging of networks and packets

## Remediations

* IP blacklisting
* Blocking malicous requests

# References

* https://www.owasp.org/index.php/Clickjacking
* http://news.softpedia.com/news/LinkedIn-Fixes-Clickjacking-Vulnerability-in-Remove-Connections-Section-Video-322122.shtml
* http://developer.joomla.org/security/news/544-20121102-core-clickjacking
* http://javascript.info/tutorial/clickjacking#defences-and-the-ways-to-break-through
* http://www.cnet.com/news/cheerleaders-gone-wild-clickjacking-tempts-facebook-users/
* http://news.softpedia.com/news/Google-Fixes-CSRF-Vulnerability-in-Translator-and-Clickjacking-Flaw-in-Gmail-Video-351036.shtml
* http://web.archive.org/web/20170119181502/http:/w2spconf.com/2010/papers/p27.pdf
* https://www.owasp.org/index.php/HTTP_Response_Splitting
* https://www.owasp.org/index.php/Testing_for_business_logic
