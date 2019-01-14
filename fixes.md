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
