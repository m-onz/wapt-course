
# html5

## cross origin resource sharing (cors)

CORS is a mechanism defined by the W3C [here](http://www.w3.org/TR/cors/). It enables client side cross origin request. The spec defines how the browser and web server must communicate in order to bypass SOP. Certain headers are required in order to achieve this.

These headers are  not part of the HTTP/1.1 standard. They are called *Control Access Headers*. The CORS standard is used to enable cross origin HTTP requests for:

* ajax requests via `XMLHTTPRequest` API
* web fonts via `@font-face`
* WebGL textures
* images drawn with the `drawImage` API

## Example Cross Origin Ajax Request

```
function crossOriginXHRGet() {
  var req = new XMLHttpRequest();
  var url = 'http:\/\/test.net'
  req.open('GET', uri, true)
  req.onreadystatechange = handler
  req.send(body)
}
```

A cross origin is deemed *simple* is uses:

* GET
* HEAD
* POST

HTTP methods, the content-type must be one of:

* application/x-www-form-urlencoded
* multipart/form-data
* test/plain

and it does not set custom HTTP headers.

## pre-flight request

A cross-origin request is a *pre-flight* request when it is not simple:

* a PUT request
* a POST request with content-type set to application/xml
* a GET request with a custom header

Unlike simple requests, a pre-flight request needs to send two HTTP request. The first ins a HTTP OPTIONS requests. This is used to determine if the actual resource exists and is safe.

With the use of the  `Access-Control-Max-Age` the browser will cache the result of the OPTIONS request. In this way the browser can avoid sending redundant messages.

## requests with credentials

Cross origin ajax requests do not send credentials by default. The `withCredentials` flag needs to be set during XHR invocation to include HTTP cookie or HTTP authentication.

## access control headers

* Access-Control-Allow-Origin
  the most important header which sets the target origin from a specific requester origin. It can be a wilcard (\*)
  - Access-Control-Allow-Origin: <allowed_origin>

* Access-Control-Allow-Credentials
* Access-Control-Allow-headers
* Access-Control-Allow-Methods
* Access-Control-Max-Age
* Access-Control-Expose-Headers
* Origin
* Access-Control-Request-Method
* Access-Control-Request-Method
* Access-Control-Request-Header

For security reasons a target origin cannot specify a resource both accessible by any other origin that accepts credentials.

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

Will never be sent.

## cross window messaging

HTML5 allows iframes, frames and popups and the current windows to communicate regardles of SOP using *cross window messaging*.

A window can send messages to another window by using the *postMessage* API call:

main window:

```
popup = open('htpp://test.local')

popup.postMessage('hello from main window ', 'http:\/\/demo.local')
```
popup:
```
window.addEventListenr('message', receiveMessage, false)
function receiveMessage () {
  if (event.origin !== 'http:\/\/allowed.com') return;
}
```

Note the popup window uses a whitelist on the `event.origin` allowing a trusted site to communicate. This is good practice and should be enforced.


## web storage

HTML5 provides data storage via some APIs namely...

* localStorage - 5MB to 10MB
* sessionStorage

Cookies are limited to 4KB

LocalStorage API

```
localStorage.setItem('key', 'value')
var thing = localStorage.getItem('key')
localStorage.removeItem('key')
localStorage.clear()
```

sessionStorage API

```
sessionStorage.setItem()
" getItem
" removeItem
" clear
```

Local storage and session storage are managed via JavaScript so they can be stolen through XSS attacks.

```
// logic..
// loop through localStorage
// store results
// send to another server
```

## Websockets

* Connection established by upgrading existing HTTP connection to a *websocket* connection
* Supported by browsers :: ws:// && wss:// secure protocol
* HTTP Standard ports 80, 443
* Full duplex communication
* minimal packet overhead
* no polling overhead
* real tcp connections allow low latency

Websocket API.

```
var ws = new WebSocket('ws://<target_url>')

ws.onopen = function (e) {
  ws.send('hello')
}

ws.onmessage = function (m) { console.log(m.data); }

```

You should check that data is sanitized correctly as websockets can provide an injection point.

## Sandboxed frames

When a website hosts third party contents through iframes. Despite SOP the `location` property of each frame is always writable.

This could be dangerous as an iframe could redirect visitors website by setting the location property of its parent document.

One technique for preventing this is installing an `onbeforeunload` event in the main document. This event will be triggered if a child iframe attempts to change the main documents location property.

If the main document and iframe are on the same origin they can access each other.

With HTML5 you can add a `sandbox` attribute to an iframe element to effectively disable JavaScript execution from within the iframe... by default this means...

* forms, scripts and plugins are disabled
* features that trigger automatically are blocked
* no links can target other browsing contexts
  - eg, a link clicked in an iframe cannot open the page in the context of the parent document.

  Some options can be specified for example

  * allow-script
  * allow-forms
  * allow-top-navigation
    - this flag allows the iframe content to navigate its top level browsing context.

# References

* http://www.w3.org/TR/cors/
* https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS
