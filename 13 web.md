
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

Similar to XML-RPC but using JSON. You send a JSON object containing method, params and id. The id is used to match the request/response.

```
POST /json_rpc/web_service.php HTTP/1.1
User-Agent: firefox
Host: wp.site
Content-Type: application/json-rpc
Content-Length: 57

{"method":"someMethod","params":["http://duckduckgo.com", "id":1]}
```

## SOAP
```
POST /InStock HTTP/1.1
Host: www.example.org
Content-Type: application/soap+xml; charset=utf-8
Content-Length: 299
SOAPAction: "http://www.w3.org/2003/05/soap-envelope"

<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:m="http://www.example.org">
  <soap:Header>
  </soap:Header>
  <soap:Body>
    <m:GetStockPrice>
      <m:StockName>T</m:StockName>
    </m:GetStockPrice>
  </soap:Body>
</soap:Envelope>
```
## REST
Representational state transfer is not a protocol, but rather a set of principles to build webservices around system resources. They typically use JSON or XML.

RESTful APIs generally use HTTP verbs and URLs to perform actions on those resources for example.

* GET `/book` :: get a book
* PUT `/book/1` :: update  
* POST `/book/1` :: create
* DELETE `/book/1` :: delete

## WSDL Web Service Services Description language

[1.1](http://www.w3.org/TR/wsdl)
[2.0](http://www.w3.org/TR/wsdl20-primer/#basics)

* abstract: describes what the service does.
* concrete: adds information where functionality is offered

```
# abstract definition
<definition>
<types>...</types>
<message>...</message>
<portType>...</portType>
# concrete description
<bindings>...</bindings>
<service></service>
```

* request WSDL file
* WSDL info
* request <SERVICE>

## binding

The binding element describes *how* to access the service. It describes the message format and protocol details for operations, messages and interfaces.

```
<wsdl:binding name="helloservice"
  type="ns:helloservice"
  <soap:binding transport="http://schemas.xml"
      soap.org/soap/http" style="document"/>
  </wsdl:binding>
```
