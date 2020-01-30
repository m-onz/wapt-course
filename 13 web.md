
# web services

Here is a list of common web server implentations...

* XML-RPC
  - remote procedure call (RPC) that uses XML over HTTP to invoke functions
* JSON-RPC
  - remote procedur call that uses JSON
* SOAP
  - messaging protocol that uses XML
* RESTful
  - a pattern for building web services

## XML-RPC

Created in 1998 and was the first web service protocol. It works by sending HTTP requests that call a single method on a remote system. The body of the request is XML which can be used to send complex and records and list structures.

```
POST /xml_rpc/web_service.php HTTP/1.1
User-Agent: Zend_XmlRpc_Client
Host: wp.site
Content-Type: text/xml
Content-Length: 179


<?xml version="1.0" ?>
<methodCall>
  <methodName>My.Method</methodName>
  <params>
    <param>
      <value>www.duckduckgo.com</value>
    </param>
  </params>
</methodCall>
```
http://xmlrpc.scripting.com/spec.html

## JSON-RPC
