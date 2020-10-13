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

for e.g. to get list of all users:

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
