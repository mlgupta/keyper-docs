# Command Line Interface
All the REST APIs of Keyper can be accessed using ```curl``` CLI.  

To access keyper REST APIs, use the hostname you specified during startup. By default, the container listens on both port 80 (http) and port 443 (https). If you are using a default self-generated certificate, you'll have to specify appropriate flags to ignore self-signed certificate. We recommend the use of a CA issued certificate in the production environment.  

By default, all requests must be sent to ```http(s)://hostname/api/```  

All API access is over http or https (https is recommended). All data is sent and received as JSON.  

Authentication creates a JWT token, which is used to maintain the session. For each call JWT token must be added as part of HTTP header with name Authorization and value Bearer Token  

## Login
To get started login to REST service and get a new JWT token.  

```
$ curl  -H "Content-Type: application/json                     \
        --request POST                                         \
        -d '{ "username": "admin", "password": "<password>" }' \
        https://sprout.dbsentry.com/api/login
```

On successful login you should get a JWT token, and output would look like the following:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE2MDIwODg0MzQsIm5iZiI6MTYwMjA4ODQzNCwianRpIjoiYzZiYjUzYTktZTQwYy00ZTEwLTg1OWUtOGE0ZDdhZDc3MTlhIiwiZXhwIjoxNjAyMDg5MzM0LCJpZGVudGl0eSI6ImFkbWluIiwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIiwidXNlcl9jbGFpbXMiOiJ7cm9sZToga2V5cGVyX2FkbWlufSJ9.hDL5GvaYlburLNhMjN9jUd1cfY1itrafHqEhMZS3FxQ",
  "accountLocked": false,
  "cn": "admin",
  "displayName": "admin",
  "dn": "cn=admin,ou=people,dc=keyper,dc=example,dc=org",
  "givenName": "admin",
  "memberOfs": [
    "cn=KeyperAdmins,ou=groups,dc=keyper,dc=example,dc=org"
  ],
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE2MDIwODg0MzQsIm5iZiI6MTYwMjA4ODQzNCwianRpIjoiYjYwOGY5M2YtYzBkMi00MjBhLWE4NTctNjE3MzgyM2YyMTViIiwiZXhwIjoxNjA0NjgwNDM0LCJpZGVudGl0eSI6ImFkbWluIiwidHlwZSI6InJlZnJlc2giLCJ1c2VyX2NsYWltcyI6Intyb2xlOiBrZXlwZXJfYWRtaW59In0.fwlENUukv1G0aI2MzmbKQpbeUcTpcG-De3XG-vilMW8",
  "sn": "admin",
  "uid": "admin"
}
```

Default administrator user is **admin**. If you specified a password using environment variable **LDAP_ADMIN_PASSWORD** use that password. If you did not specify a password using environment varible **LDAP_ADMIN_PASSWORD** use **superdupersecret** as password.

```important:: Passwords are set during the first start within openldap database. If using data persistence, which you should, the same password should be used during subsequent startup of the container.
```  

For each subsequent call JWT token must be added as part of HTTP header with name Authorization and value Bearer Token.  

## Example Operation
To get list of all users:

```
$ curl  -H "Content-Type: application/json                     \
        -H "Authorization: Bearer ${JWT_TOKEN}"                \
        https://sprout.dbsentry.com/api/users
```

Will result in an output like the following:

```
[
  {
    "accountLocked": false,
    "cn": "bob",
    "displayName": "Bob Parker",
    "dn": "cn=bob,ou=people,dc=keyper,dc=example,dc=org",
    "givenName": "Bob",
    "mail": "bob@dbsentry.com",
    "memberOfs": [
      "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sn": "Parker",
    "uid": "bob"
  },
  {
    "accountLocked": false,
    "cn": "erin",
    "displayName": "Erin Parker",
    "dn": "cn=erin,ou=people,dc=keyper,dc=example,dc=org",
    "givenName": "Erin",
    "mail": "erin@dbsentry.com",
    "memberOfs": [
      "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sn": "Parker",
    "uid": "erin"
  },
  {
    "accountLocked": false,
    "cn": "admin",
    "displayName": "admin",
    "dn": "cn=admin,ou=people,dc=keyper,dc=example,dc=org",
    "givenName": "admin",
    "memberOfs": [
      "cn=KeyperAdmins,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sn": "admin",
    "uid": "admin"
  },
  {
    "accountLocked": false,
    "cn": "alice",
    "displayName": "Alice Parker",
    "dn": "cn=alice,ou=people,dc=keyper,dc=example,dc=org",
    "givenName": "Alice",
    "mail": "alice@dbsentry.com",
    "memberOfs": [
      "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sn": "Parker",
    "sshPublicKeys": [
      {
        "dateExpire": "20201204",
        "hostGroups": [
          "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
        ],
        "key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1KtJpPn6W9W5WgPU8+eYuuSKKyHA+Z62mVLYp50Ch/MMTUSxcFF/V1H81CStU4OrPv/pUxpHtqSDeTCMbVtTmP0Bbc5V7rCYQVgfhTB7CzKAwnfJSfJGY/JoJLCrC4kt40PMwyXTHiPUkrs4tOHiv7GIT4aZI/wmVPrg8x6oBFRgfCl1TQVgeSQl2kAnjkUHEsq2CsnZR9mKIJ31CWzeHLotYHNg82jmgylCWUsl6Pd5eigObUtk0j6Vnjn7FUKwSmffhEPInU1K+IzYMdFe1QElTSO7X+IOjedQZ2Y8nt3U9N9WPyd7FK13Sn8Ij22CIMmTuvfNXv/H4ja9vF0Ob"
      }
    ],
    "uid": "alice"
  },
  {
    "accountLocked": false,
    "cn": "carol",
    "displayName": "Carol Parker",
    "dn": "cn=carol,ou=people,dc=keyper,dc=example,dc=org",
    "givenName": "Carol",
    "mail": "carol@dbsentry.com",
    "memberOfs": [
      "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sn": "Parker",
    "uid": "carol"
  },
  {
    "accountLocked": false,
    "cn": "frank",
    "displayName": "Frank Parker",
    "dn": "cn=frank,ou=people,dc=keyper,dc=example,dc=org",
    "givenName": "Frank",
    "mail": "frank@dbsentry.com",
    "memberOfs": [
      "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sn": "Parker",
    "uid": "frank"
  },
  {
    "accountLocked": false,
    "cn": "grace",
    "displayName": "Grace Parker",
    "dn": "cn=grace,ou=people,dc=keyper,dc=example,dc=org",
    "givenName": "Grace",
    "mail": "grace@dbsentry.com",
    "memberOfs": [
      "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sn": "Parker",
    "uid": "grace"
  }
]
```

## Certificate Authentication
If you are using Keyper as SSH CA and certificate based authentication, you can use following API URLs to get certificates from the system. These URLs are open and do not require authentication.

To get CA's Host Public Key: 

```
$ curl https://sprout.dbsentry.com/api/hostca
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCs11YCNSkWn9bb1rb2LM5YOvUwoLjsrgit9BlnAgAtgH5GldazodV2uHESCLoMXuw7BFLZEFoCER/Ekt0hhmYNV6+9c55mFG4qwfZzCfCBQsE2BBSqCyNnBwD5TzS8wdnvpveecZjBq3l4XGXZw12IjK4nnLF8Z5/4AliEmptnfFjTO1uPT7zozKdocnZHwLDNRKkR80b2XRfIbsRXj0qwgBsR4kSvyzojvyGgf8eGwhNMiE2GPBLouke7vl1Fh6ESz9li+ge3T5RSTSwe/iZI/Hu6Mmwmj8Q3lBSwu0210woc/vwvaBQpfE7k1eObaDrOxDDhxyZnU9lyrZ5Vves33S1r/uzPqhgGak+OSwA2YowACyo5ikn8+PUrnt/hPvsoVEOPhjYjmH65J8EY3r3Vsjg8RsSL1doIZprjLWaQhI4LouUIQGBOA/RneElKmTnb/iadjMnLnKxXEtaQhVOFduh34GAIGwmB4CIN+WNVRERrECdYOiHW1vF9gmTdgVk= root@42ff51f24a7a
```

To get CA's User Public Key: 

```
$ curl https://sprout.dbsentry.com/api/userca
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDBr/8wKPp6bnbdPxtP+ExuBNDQJ3O/DbAOta59wfWahdhGGo1Z6L4vbGgVGQV1a3sD89FbZFeGfEHfmpBxe5JThzF92IagFgxQbxzPbZP8eKMQsgldq270jeM8yl0cS13r77BCjA6ROlKQFpPcE1Mk0bKitAGhYnY9L6A9s94n9YAH//LMtB9/+3BGz2jHqS5CsXCEqTyCR2mIihdPGOxvNkFBozwB1CTl4VBM8yMBAi5iaNqacZa18Umd6aG6Z48Q7RI2cO2sDfW/sSMh/ct+glfWRoqAbTrZLEUSMs+Mhd5CpVePEpZlN8Ov52HrBWmHA0LJGWjducpffvl7jh7A4dUZl1IU2tA/qz70j09fXhSJSztTdwR1Zn5HTnlCuNAZAuDIhpC/ObBdAtqbkpNMBmAjqHnAYu2RMjrkh/wiyebUfoKTzwp/C+mwep/FAF3a+M+D4pA6BC01K3iEtdJAGc50LbyYxiCI34L+VHwYKhzCA97IjKLEAglkgPzVN5s= root@42ff51f24a7a
```

To get CA's Key Revocation List (KRL): 

```
$ curl https://sprout.dbsentry.com/api/krlca
```

To get a host's signed certificate:
```
$ curl "https://sprout.dbsentry.com/api/hostcert?hostname=getafix2&keyid=100"
ssh-rsa-cert-v01@openssh.com AAAAHHNzaC1yc2EtY2VydC12MDFAb3BlbnNzaC5jb20AAAAgkD4bjnplF1A28itjmbY7qobnUJF7f/ay81NwrLzTIfQAAAADAQABAAABgQCpuKUaryiGo8Lx/Eov51o4ojBOdu4s/s1bdTKStgv6FixqHKPWsPbNF+8J/+ODxEz09KB4cD6/OjNvuOWDkavnqKLMY8lipdVqaYxFuESSy/mf1o4gU+92+YDxsy4GzWchHcbOfxJ1mPogTHltcRt3q1R3pj5WVXotN6fUswKFEJypw9IrIr+LQ6D4qvvcY6MJBfqgSE3/SiDwp2lX+loLLd/BCoPIE/bVzx5TnnkM43upWtsiHNd5KPUrFsL7aJ5CpoQ4ZJRr/KxMov9NZGLwYjslyf4MAjwoDTp8U+4PwjecfEyoTPAlfFLBZNm2EQ4g8q+AlQ/riCRQjt/ftJG1ecSW9ypbgsj9pcksGxtPtg7pMq9Si0XMtOaMlWArJZFKLJEVF505eqe6iotal+/zP/hR7mpCCHFqW45q848aJvYLM0AwlQTEFyMEXzl2z9UTZ0Oi8pEEy9JwuDtsUhrGTbyv0qUpp/jalBorvRgcehxYJNIc3SPGNrKUQ+T5cQumqFKpFCDRqQAAAAIAAAAIZ2V0YWZpeDIAAAAlAAAACGdldGFmaXgyAAAAFWdldGFmaXgyLmRic2VudHJ5LmNvbQAAAABfkkb0AAAAAGFzerEAAAAAAAAAAAAAAAAAAAGXAAAAB3NzaC1yc2EAAAADAQABAAABgQCs11YCNSkWn9bb1rb2LM5YOvUwoLjsrgit9BlnAgAtgH5GldazodV2uHESCLoMXuw7BFLZEFoCER/Ekt0hhmYNV6+9c55mFG4qwfZzCfCBQsE2BBSqCyNnBwD5TzS8wdnvpveecZjBq3l4XGXZw12IjK4nnLF8Z5/4AliEmptnfFjTO1uPT7zozKdocnZHwLDNRKkR80b2XRfIbsRXj0qwgBsR4kSvyzojvyGgf8eGwhNMiE2GPBLouke7vl1Fh6ESz9li+ge3T5RSTSwe/iZI/Hu6Mmwmj8Q3lBSwu0210woc/vwvaBQpfE7k1eObaDrOxDDhxyZnU9lyrZ5Vves33S1r/uzPqhgGak+OSwA2YowACyo5ikn8+PUrnt/hPvsoVEOPhjYjmH65J8EY3r3Vsjg8RsSL1doIZprjLWaQhI4LouUIQGBOA/RneElKmTnb/iadjMnLnKxXEtaQhVOFduh34GAIGwmB4CIN+WNVRERrECdYOiHW1vF9gmTdgVkAAAGUAAAADHJzYS1zaGEyLTUxMgAAAYArSPYyZlgz0OWqDfiF+bSLXbZvQdRLIlBRtdsGMeDzHUUYG0lfAF3OM3uaYWcfSwatUWuxSOw0sA40y2xiDeRGEDjrRwoG2U9vejdnKZI/1upn/h9Lmyzis/umuqIYR7taL/D6ci9imJH1JZBpK5XqO0UBUj2+OeXMtuirw809e3D6h/mlmBLXQj1pYNvvIeNCETYZloJAlpF7S4JyAzCSx0QeH3GyQSDTQ1pWRkyOBhxyxDsJ4wx+IjrsP386dxsjxeNPM6C8CV11uomyfcvc3rRwzCApF0uOvi4s0od4FSg+6cdL3e5Y52Ety/9M77VjgjWbAcPcfu9VHqd/jJuu5RQvGYcEDTbmxrVhwsYfjrPu9+qsVQQ08hhcjthdkFMUMhTcGi6rgDLsjVzdHmpfQSysE/37jbF9g5DvpfARCUUKldVCoYja6+MvfvgcplaXpLTM+0yAyb50ThW42q/S71QV5DEFuUPIoczDQ65NyQem4K2wIlqc/f8VKISC+po= /etc/sshca/tmp/tmp7wpikvxc.pub
```

To get a user's signed certificate:
```
$ curl "https://sprout.dbsentry.com/api/usercert?username=alice&keyid=103"
ssh-ed25519-cert-v01@openssh.com AAAAIHNzaC1lZDI1NTE5LWNlcnQtdjAxQG9wZW5zc2guY29tAAAAIA69BxOJLW8N3HeljOzDCG/NMz8y/lsF904waHa+90e5AAAAIOneaLgpzgUK4p80a7niJMmV8PdcTxrk9VeTXA7BO60rgLRH28HCXCQAAAABAAAAB21hbmlzaDIAAAAVAAAAB21hbmlzaDIAAAAGYXBhY2hlAAAAAF+Pqg0AAAAAYAzojQAAAAAAAACCAAAAFXBlcm1pdC1YMTEtZm9yd2FyZGluZwAAAAAAAAAXcGVybWl0LWFnZW50LWZvcndhcmRpbmcAAAAAAAAAFnBlcm1pdC1wb3J0LWZvcndhcmRpbmcAAAAAAAAACnBlcm1pdC1wdHkAAAAAAAAADnBlcm1pdC11c2VyLXJjAAAAAAAAAAAAAAEXAAAAB3NzaC1yc2EAAAADAQABAAABAQDNwKkolBT3WUqy5HZaWf/w6Ptat2JCN4/jzKlNvJby/T5Q40qLQoJbNqnra/s5UtB7BAEgUmSMaP/kk7GQYS8LQa+yLvAcKvUxPpWU1vIAVYj4lagk5MnaIsCOaXxWeObULHpdmRsuiW+n5odoQ7g4rvyHr4FpEqbUIF4XtvM9fMx+i9fFwA+KEYj0IDwp1Me/HKTTb4+CaLH/lsRVT3ZICrcaRuP7/OE7qsaDMiIjRViBhol/YHJ8IIZS99mlIjYenZOXybPpi5q95ulcQt+9lODKZYp2aX5oaoQpsrUavfUNvHPY/PlAnPAzH/3osabN2LvU1gb7lqeI95B1SuplAAABDwAAAAdzc2gtcnNhAAABAMpM79a/byuk0aKH3FdZPlyXQlhhIZvEe7aLstLQgAHfgWPHE7hIWV8sXRGEYON6uf1S6NrhvgrFL/6DEFvBfJa/hbEWrQf57VHssaLQWRynaPsB9KJmBVd6XV/ifF+ZFSeCZEPh0iu03c0EBdV713RnVdHARNYq5vcmehdHCx2Cfvo1FBWpr7kyH9Ct4+e3pHYmmuc/vciF0N3OwLX3PMRskES/48H/gSE+XQ4a/T3LmHQCLe5ykE10CFjGsN7BC1bsXF2SL1Y/lHwQjgPjhB17RTU2V4i/TDYeLUtUUdssJzQhRcKEYS3310QX+YEYhGyyFM3GdgbsgWIhNmkYxpU=
```
