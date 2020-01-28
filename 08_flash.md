
# flash

Adobe Flash technology is used to create video, animation and other rich content. Flash files can be used as standalone applications or embedded in HTML pages.

Adobe provides a scripting language called `ActionScript` based on the `ECMA-262` spec and is very similar to JavaScript.

Unlike other scripting languages Flash ActionScript needs to be compiled before execution. The compiled object is an SWF (shockwave flash) format.

Once compiled it can be difficult to obtain the exact source running in the application. Flash decompilers have been built for this very purpose.

* http://www.sothink.com/product/flashdecompiler/
* http://www.flash-decompiler.com/

## Example embedded flash application

```
<h1>my app</h1>
<object type="application/x-shockwave-flash" width="700" height="450">
    <param name="movie" value="myfirstflashapp.swf" />
    <param name="allowScriptAccess" value="never" />
    <embed src="myfirstflashapp.swf" width="700" height="450" />
</object>
```

## `allowScriptAccess` attribute

The `alowScriptAccess` attribute can have 3 values:

* Always : comunicates regardless of the domain
* sameDomain : SWF file must be on the same domain
* Never : the SWF does not communicate


## Passing arguments to Flash Files

* direct method : passing via URL query parameters
* HTML data attributes

Or via `FlashArgs`

```
<param name="flashArgs" value="name=test&demo=test2" />
```

## Flash security model

2 important concepts in flash security:

* sandbox : accessible resources
* stakeholder : access control & policies

## stakeholders

stakeholders describe different toles involved in the control of security settings. The four stakeholders are:

* administrator role (user institution settings)
* user role
* website role
* author role

Flash security checks are performed in order, each stakeholder can block rejecting the operation stopping any further checks.

## [ administrator role ]

The administrator role is represented by the system admin responsible for the flash player installation. The can configure flash security settings that affect all operating system users.

```
c:\\system32\\Macromedia\\Flash\\mm.cfg
```

This text configuration files is read when Flash player starts. It can grant or restrict access to a variety of features.

The `global Flash Player Trust` directory is where admins can register SWF files which can interact with other SWF files loading data from local or remote locations.

## [ user role ]

The user role is represented by the operating system user running a flash animation through the player

```
Settings manager & Settings UI
```

The settings manager and settings UI provide some security related options such as enabling camera and microphone, shared objects and settings relating to legacy content.

The `User Flash Player Trust` allows users to register SWF files as *trusted* which allows it to interact with other SWF locally or remotely.

## [ website role ]

The website role is represented by the web server component responsible for delivery of the Flash animation or flash resource on the client. This role can decide wehter the SWF can interact via ActionScript with data available on the web server.

A Policy file is placed at a specific location on the web server. This file is read by the Flash player; SWF files residing on third party domains will use this to derive their access rules, which they must adhere to.

## connections

Depending on the requested resource, ActionScript can initiate two different types of connection:

1. Document based server connection
  * loader - SWF files or images
  * sound
  * URL Loader (text or binary data)
  * URL Stream

By default the policy file checked during document based server connections is `crossdomain.xml` in the root directory of the server.

2. Socket
  * ActionScript socket
  * XMLSocket

## URL Policy file

The policy file is also known the master policy file. Here is an example of the URL policy file:

```
<?xml version="1.0"?>
<cross-domain-policy>
  <allow-access-from domain="*.example.com" />
</cross-domain-policy>
```

A SWF file can check the policy files in different locations by calling the `Security.loadPolicyFile()` method.

This policy file is checked by the Flash player whether or not it is a socket connection. By default the flash player looks for a socket policy file on port 843; we refer to this policy as the *Master* policy file.

# [ author role ]

The author role is represented by the developer of the Flash animation. This role can affect the interaction between SWF files available on different domains.

The API `Security.allowDomains(<allowedDomain>)` grants permissions to the following features:

* the interaction between different SWF files (flash cross scripting)
* [Display list access](http://web.archive.org/web/20151110232936/http:/help.adobe.com:80/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c98.html#WS5b3ccc516d4fbf351e63e3d118a9b90204-7c7e)
* [Event detection](http://web.archive.org/web/20151110232936/http:/help.adobe.com:80/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c98.html#WS5b3ccc516d4fbf351e63e3d118a9b90204-7c7e)
* [Full access to the properties and methods of the Stage object](http://web.archive.org/web/20151110232936/http:/help.adobe.com:80/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c98.html#WS5b3ccc516d4fbf351e63e3d118a9b90204-7c7e)

# Flash cross-scripting

SWF files by default cannot access the methods or properties of other SWF files running on different domains.

When flash animations are embedded in HTML pages, ActionScript code can call the parent page's javascript code using the `ExternalInterface` class.

## Example calling javascript from actionscript

here is an example JavaScript execution launched by a flash animation.

```
function sayHello () {
  return 'hello'
}

var result : String = ExternalInterface.call('sayHello');
```

## Example calling actionscript from javascript

The `getFlashMovie` function is used to obtain a reference to the embedded flash object.

```

getFlashMovie('someflash').sayHello();

ExternalInterface.addCallback('sayHello', sayHello)

```

## Method :: navigateToURL

This ActionScript method is used to open a URL in the window embedding the flash file. It is similar to the javascript function `window.open`. The second optional paramter specifies the target: a given frame, current window or new window.

```
navigateToURL(URLRequest, [target])
```

The URLRequest can contain JavaScript code such as `javascript:alert(11)`; in this case the javascript code will only execute if the following condition are true:

* the SWF file is a locally trusted file
* the target is the HTML page in which the SWF file is embedded & `allowScriptAccess` rules apply
* the target and SWF file are part of the same sandbox and on the same subdomain

[ActionScript reference](http://web.archive.org/web/20170328012534/https:/help.adobe.com/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c9b.html)


## Local shared object

Flash features an internal storage mechanism based on local shared objects. They are similar to browser cookies...

* they can be used to track user activity or store preferences
* they are read-only by subdomains that have set them

Some differences...

* they are not sent back and forth over HTTP connections
* they do not expire
* they are stored in a dedicated flash directory shared amonst all browsers on the system
* they can contain large amounts of complex data

example retrieving a local shared object with ActionScript:

```
var myCooke : SharedObject = SharedObject.getLocal('information');
```

## Flash vulnerabilities

A flash parameter injection vulnerability occurs when:

* The attacker can insert malicious code into the web application
* the web app passes the input provided by the attacker without any significant sanitization
* the flash animation, embedded in the HTML pages does not sanitize input parameters
* the SWF source code allows HTML injections via XSS

Example: vulnerable flash app with injection point

```
<div id="flashcontent">
  <object type="application/x-shockwave-flash">
    data="flash.swf?name=<?php echo $_GET['name']; ?>"&site=<?php $_GET('site'); ?>"
    ...
  </object>
</div>
.... // flash.swf actionscript code
var paramObj : Object = LoaderInfo(this.root.loaderInfo).parameters;
var link : URLRequest = new URLRequest(paramObj.site);
navigateToURL(link, " self ");
```

Exploit:

```
site=javascript:alert(document.cookie)
```

## Tools :: SWFinvestigator

A tool for investigating SWF files. Some features include:

* Actionscript 2/3 disassembling
* SWF tag viewing
* Local Shared Object (LSO) analyzing
* Dynamic function calling

## Finding sensitive information via disassembly

Some flash developers may assume its impossible to recover the source code from a compiled SWF application. However it may be possible to recover sensitive information via disassembly.

## Pentesting flash application

* check client side components (SWF files & container page)
* the communication protocol between client and server
* the server side components

## Static analysis

Static code analysis can yield...

* hard coded information, urls & credentials
* easily see if all inputs have been sanitized correctly

Do analysis of the container pages...

* check for `allowScriptAccess` paramter
* check all input arguments are sanitized

## Check `crossdomain.xml`

* check the crossdomain.xml is configured correctly
* check input sanitization

## Identifying communication protocols

A complex flash app may send requests using protocols:

* SOAP
* AMP

These can be tested for common injections and vulnerabilities.

## References

* http://web.archive.org/web/20151110232936/http:/help.adobe.com:80/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c98.html#WS5b3ccc516d4fbf351e63e3d118a9b90204-7c7e
* http://web.archive.org/web/20151110232936/http:/help.adobe.com:80/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c98.html#WS5b3ccc516d4fbf351e63e3d118a9b90204-7c7e
* https://www.owasp.org/index.php/Category:OWASP_Flash_Security_Project
* http://web.archive.org/web/20151110232936/http:/help.adobe.com:80/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c98.html#WS5b3ccc516d4fbf351e63e3d118a9b90204-7c7e
* http://web.archive.org/web/20170328012534/https:/help.adobe.com/en_US/ActionScript/3.0_ProgrammingAS3/WS5b3ccc516d4fbf351e63e3d118a9b90204-7c9b.html
*
