# Exercise 6
The cookie `USER_AUTHENTICATION_EXTRA_SECURITY` is Base64 encoded. We can see that from the start of tha value which is 'rO0'.
So we can just decode the content and find the original java object that was serialized:
```java
srcom.kalavit.javulna.model.UsermP=LemailAddresstLjava/lang/String;Lmottoq~Lnameq~Lpasswordq~Lsexq~L
webPageUrlq~xr$com.kalavit.javulna.model.BaseEntity@qL	createdAttLjava/util/Date;Lidq~L
lastUpdatedAtq~xppt2ptdarth@lucasarts.comt,I see a red door and I want it paint it backtDarth Vadert
IamYourFathertmt,http://www.starwars.com/databank/darth-vader
```
We can see that the cookie actually just contains a plain java object. This means we can replace it with one that we provide; it will be deserialised when the application finds the cookie. We can make use of a payload generator to create an object that exploits the apache commons-collections 4.0 vulnerability. The object we create will execute a system command fro its constructor, allowing us to execute code on the target machine where the application runs.

The tool I used to generate the payload is [ysoserial](https://github.com/frohoff/ysoserial)

To encode the payload again I just have to execute the built-in command on a linux machine:
> base4 --wrap=0 payload

## Fix
Use another way to serialize/deserialize the java object stored in the cookie.