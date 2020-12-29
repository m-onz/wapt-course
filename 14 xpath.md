
# xpath

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

# XPath

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


