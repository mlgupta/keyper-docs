# REST API
You can use Keyper REST API to create calls to integrate with Keyper.

By default, all requests must be sent to http(s)://hostname/api/

All API access is over HTTP or HTTPS (we recommend HTTPS). All data is sent and received as JSON.

Authentication creates a JWT token, which is used to maintain the session. For each call JWT token must be added as part of HTTP header with name **Authorization** and value **Bearer Token**

## Authentication
Following methods are available under authentication

### /login (method=POST)
Authenticates a user and returns JWT token.

Input:
```JSON
{
    "username": "alice",
    "password": "success."
}
```
Output:
```JSON
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1OTkxNzEyOTUsIm5iZiI6MTU5OTE3MTI5NSwianRpIjoiYjg4MThkZmQtM2I4OS00NDE2LWIxNmYtOTBhYjE1MzQ4NTcyIiwiZXhwIjoxNTk5MTcyMTk1LCJpZGVudGl0eSI6Im1hbmlzaCIsImZyZXNoIjpmYWxzZSwidHlwZSI6ImFjY2VzcyIsInVzZXJfY2xhaW1zIjoie3JvbGU6IGtleXBlcl9hZG1pbn0ifQ.W9rlhXoOrw4EWaj8NGMMSelJxsbMfF7ZOroHBBdKkDI",
  "accountLocked": false,
  "cn": "alice",
  "displayName": "Alice Parker",
  "dn": "cn=alice,ou=people,dc=dbsentry,dc=com",
  "givenName": "Alice",
  "mail": "alice@dbsentry.com",
  "memberOfs": [
    "cn=KeyperAdmins,ou=groups,dc=dbsentry,dc=com",
    "cn=AllHosts,ou=groups,dc=dbsentry,dc=com"
  ],
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1OTkxNzEyOTUsIm5iZiI6MTU5OTE3MTI5NSwianRpIjoiNWMyNTc4NGYtOTUxYy00NmNmLThiNTQtZjZmN2FmZjcyNjZmIiwiZXhwIjoxNjAxNzYzMjk1LCJpZGVudGl0eSI6Im1hbmsdkjsdCIsInR5cGUiOiJyZWZyZXNoIiwidXNlcl9jbGFpbXMiOiJ7cm9sZToga2V5cGVyX2FkbWlufSJ9.0Bcekdnn156PGvRGSQCwPUUVdAml5pcbHi8lYxvVHxk",
  "sn": "Parker",
  "sshPublicKeys": [],
  "uid": "alice"
}
```
### /logout (method=DELETE)
Terminates user's session.

Output:
```json
{
  "msg": "Successfully logged out"
}
```
### /refresh (method=POST)
If JWT expires, you need to use this method with refresh token as part of the header. It returns a fresh JWT token, which can be used in subsequent calls.

Output:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1OTkxNzE1ODQsIm5iZiI6MTU5OTE3MTU4NCwianRpIjoiOWI4NjEwNmMtMGU5Yi00N2IxLWIwYmYtMTA0YWEwZDFlNDllIiwiZXhwIjoxNTk5MTcyNDg0LCJpZGVudGl0eSI6Im1hbmlzaCIsImZyZXNoIjpmYWxzZSwidHlwZSI6ImFjY2VzcyIsInVzZXJfY2xhaW1zIjoie3JvbGU6IGtleXBlcl9hZG1pbn0ifQ.e7b5Tq1PnG64XR2sDh7HGH7z1SQ_Sk4eOib2ZUCF0Fw"
}
```

## Users
### /users (method=GET)
Gets all the users in the system.

Output:
```json
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
### /users/username (method=GET) 
Gets a single user ```username``` from the system.

Output (assuming ```username=alice```):
```json
[
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
  }
]
```
### /users (method=POST)
Creates a user per supplied input. Make sure to format all the required input attributes.

**Input Parameters:**
Input for the method is submitted in JSON format (* denotes required field)

* **cn***: Username. 
* **userPassword***: Password.  
* **confirmPassword***: Password. 
* **givenName***: First Name
* **sn***: Last Name
* **displayName**: Display Name
* **mail**: Email Address
* **accountLocked**: Account Locked Flag (True/False)
* **memberOfs**: Groups the user is member of
* **principal***: Principal
* **duration***: Default validity for Keys/Certificate
* **durationUnit***: Duration Unit (Hours/Days/Weeks)
* **sshPublicKeys**: SSH Public Keys for user (JSON array. See SSHPublicKeys Format)
* **sshPublicCerts**: SSH Public Certs for user (JSON array. See SSHPublicCerts Format)

**sshPublicKeys:**

* **name**: Key Name
* **Key**: 
* **fingerprint**: Fingerprint of the Key 
* **hostGroups**: Host Groups this key is applicable for.

**sshPublicCerts:**

* **name**: Cert Name
* **key**: 
* **fingerprint**: Fingerprint of the Key 
* **hostGroups**: Host Groups this cert is applicable for.

Input:
```json
{
  "cn": "judy",
  "userPassword": "keyper",
  "confirmPassword": "keyper",
  "mail": "judy@dbsentry.com",
  "givenName": "Judy",
  "sn": "Parker",
  "displayName": "Judy Parker",
  "accountLocked": "false",
  "memberOfs": [
    "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
  ],
  "sshPublicKeys": [
    {
      "name": "demo",
      "dateExpire": "20201204",
      "hostGroups": [
        "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
      ],
      "key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1KtJpPn6W9W5WgPU8+eYuuSKKyHA+Z62mVLYp50Ch/MMTUSxcFF/V1H81CStU4OrPv/pUxpHtqSDeTCMbVtTmP0Bbc5V7rCYQVgfhTB7CzKAwnfJSfJGY/JoJLCrC4kt40PMwyXTHiPUkrs4tOHiv7GIT4aZI/wmVPrg8x6oBFRgfCl1TQVgeSQl2kAnjkUHEsq2CsnZR9mKIJ31CWzeHLotYHNg82jmgylCWUsl6Pd5eigObUtk0j6Vnjn7FUKwSmffhEPInU1K+IzYMdFe1QElTSO7X+IOjedQZ2Y8nt3U9N9WPyd7FK13Sn8Ij22CIMmTuvfNXv/H4ja9vF0Ob",
      "fingerprint": "SHA256:89KzcF/hqGg6S0qtvH2Wn6FfpnuAG6BGrNOWarGBG20"
    }
  ]
}
```

Output:
```json
[
    {
        "accountLocked": false,
        "cn": "judy",
        "displayName": "Judy Parker",
        "dn": "cn=judy,ou=people,dc=keyper,dc=example,dc=org",
        "givenName": "Judy",
        "mail": "judy@dbsentry.com",
        "memberOfs": [
            "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
        ],
        "sn": "Parker",
        "sshPublicKeys": [
            {
                "dateExpire": "20201204",
                "fingerprint": "SHA256:89KzcF/hqGg6S0qtvH2Wn6FfpnuAG6BGrNOWarGBG20",
                "hostGroups": [
                    "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
                ],
                "key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1KtJpPn6W9W5WgPU8+eYuuSKKyHA+Z62mVLYp50Ch/MMTUSxcFF/V1H81CStU4OrPv/pUxpHtqSDeTCMbVtTmP0Bbc5V7rCYQVgfhTB7CzKAwnfJSfJGY/JoJLCrC4kt40PMwyXTHiPUkrs4tOHiv7GIT4aZI/wmVPrg8x6oBFRgfCl1TQVgeSQl2kAnjkUHEsq2CsnZR9mKIJ31CWzeHLotYHNg82jmgylCWUsl6Pd5eigObUtk0j6Vnjn7FUKwSmffhEPInU1K+IzYMdFe1QElTSO7X+IOjedQZ2Y8nt3U9N9WPyd7FK13Sn8Ij22CIMmTuvfNXv/H4ja9vF0Ob",
                "name": "demo"
            }
        ],
        "uid": "judy"
    }
]
```

### /users/username (method=PUT)
Updates a user.

**Input Parameters:**
Input for the method is submitted in JSON format (* denotes required field)

* **userPassword***: Password.  
* **confirmPassword***: Password. 
* **givenName***: First Name
* **sn***: Last Name
* **displayName**: Display Name
* **mail**: Email Address
* **accountLocked**: Account Locked Flag (True/False)
* **memberOfs**: Groups the user is member of
* **principal***: Principal
* **duration***: Default validity for Keys/Certificate
* **durationUnit***: Duration Unit (Hours/Days/Weeks)
* **sshPublicKeys**: SSH Public Keys for user (JSON array. See SSHPublicKeys Format)
* **sshPublicCerts**: SSH Public Certs for user (JSON array. See SSHPublicCerts Format)

**sshPublicKeys:**

* **keyid**: If present revoke this key
* **name**: Key Name
* **key**: 
* **fingerprint**: Fingerprint of the Key 
* **hostGroups**: Host Groups this key is applicable for.

**sshPublicCerts:**

* **keyid**: If present revoke this cert
* **name**: Cert Name
* **key**: 
* **fingerprint**: Fingerprint of the Key 
* **hostGroups**: Host Groups this cert is applicable for.

Input:
```json
  {
    "mail": "judy.parker@dbsentry.com",
    "memberOfs": [
      "cn=KeyperAdmins,ou=groups,dc=keyper,dc=example,dc=org",
      "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org"
    ],
    "sshPublicKeys": [
      {
        "name": "demo2",
        "dateExpire": "20201204",
        "hostGroups": [
          "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org"
        ],
        "key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1KtJpPn6W9W5WgPU8+eYuuSKKyHA+Z62mVLYp50Ch/MMTUSxcFF/V1H81CStU4OrPv/pUxpHtqSDeTCMbVtTmP0Bbc5V7rCYQVgfhTB7CzKAwnfJSfJGY/JoJLCrC4kt40PMwyXTHiPUkrs4tOHiv7GIT4aZI/wmVPrg8x6oBFRgfCl1TQVgeSQl2kAnjkUHEsq2CsnZR9mKIJ31CWzeHLotYHNg82jmgylCWUsl6Pd5eigObUtk0j6Vnjn7FUKwSmffhEPInU1K+IzYMdFe1QElTSO7X+IOjedQZ2Y8nt3U9N9WPyd7FK13Sn8Ij22CIMmTuvfNXv/H4ja9vF0Ob",
        "fingerprint": "SHA256:89KzcF/hqGg6S0qtvH2Wn6FfpnuAG6BGrNOWarGBG20"
      }
    ]
  }
```

Output:
```json
[
    {
        "accountLocked": false,
        "cn": "judy",
        "displayName": "Judy Parker",
        "dn": "cn=judy,ou=people,dc=keyper,dc=example,dc=org",
        "givenName": "Judy",
        "mail": "judy.parker@dbsentry.com",
        "memberOfs": [
            "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=KeyperAdmins,ou=groups,dc=keyper,dc=example,dc=org"
        ],
        "sn": "Parker",
        "sshPublicKeys": [
            {
                "dateExpire": "20201204",
                "fingerprint": "SHA256:89KzcF/hqGg6S0qtvH2Wn6FfpnuAG6BGrNOWarGBG20",
                "hostGroups": [
                    "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org"
                ],
                "key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1KtJpPn6W9W5WgPU8+eYuuSKKyHA+Z62mVLYp50Ch/MMTUSxcFF/V1H81CStU4OrPv/pUxpHtqSDeTCMbVtTmP0Bbc5V7rCYQVgfhTB7CzKAwnfJSfJGY/JoJLCrC4kt40PMwyXTHiPUkrs4tOHiv7GIT4aZI/wmVPrg8x6oBFRgfCl1TQVgeSQl2kAnjkUHEsq2CsnZR9mKIJ31CWzeHLotYHNg82jmgylCWUsl6Pd5eigObUtk0j6Vnjn7FUKwSmffhEPInU1K+IzYMdFe1QElTSO7X+IOjedQZ2Y8nt3U9N9WPyd7FK13Sn8Ij22CIMmTuvfNXv/H4ja9vF0Ob",
                "name": "demo2"
            }
        ],
        "uid": "judy"
    }
]
```

### /users/username (method=DELETE)
Deletes a user ```username```.

Output (assuming ```username=judy```)
```
Deleted User: judy
```

## Hosts

### /hosts (method=GET)
Gets all hosts in the system.

Output:
```json
[
    {
        "cn": "mavrix2",
        "description": "mavrix2",
        "dn": "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
        "memberOfs": [
            "cn=mavrix2,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "mavrix3",
        "description": "mavrix3",
        "dn": "cn=mavrix3,ou=Hosts,dc=keyper,dc=example,dc=org",
        "memberOfs": [
            "cn=mavrix3,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "mavrix4",
        "description": "mavrix4",
        "dn": "cn=mavrix4,ou=Hosts,dc=keyper,dc=example,dc=org",
        "memberOfs": [
            "cn=mavrix4,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "mavrix5",
        "description": "mavrix5",
        "dn": "cn=mavrix5,ou=Hosts,dc=keyper,dc=example,dc=org",
        "memberOfs": [
            "cn=mavrix5,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
        ]
    }
]
```
### /hosts/hostname (method=GET)
Gets a single host ```hostname``` from the system.

Output (assuming ```hostname=mavrix2```)
```json
[
    {
        "cn": "mavrix2",
        "description": "mavrix2",
        "dn": "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
        "memberOfs": [
            "cn=mavrix2,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org",
            "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org"
        ]
    }
]
```

### /hosts (method=POST)
Creates a host in the system.

**Input Parameters:**
Input for the method is submitted in JSON format (* denotes required field)

* **cn***: Hostname.  
* **description**: Description. 
* **principal***: Principal
* **owners**: 
* **duration***: Default validity for Certificate
* **durationUnit***: Duration Unit (Hours/Days/Weeks)
* **sshPublicCerts**: SSH Public Certs for user (JSON array. See SSHPublicCerts Format)

**sshPublicCerts:**

* **name**: Cert Name
* **Key**: 


Input:
```json
    {
        "cn": "mavrix1",
        "description": "mavrix1"
    }
```

Output:
```json
    {
        "cn": "mavrix1",
        "description": "mavrix1"
    }
```

### /hosts/hostname (method=PUT)
Updates a hosts ```hostname```

**Input Parameters:**
Input for the method is submitted in JSON format (* denotes required field)

* **description**: Description. 
* **principal***: Principal
* **owners**: 
* **duration**: Default validity for Certificate
* **durationUnit**: Duration Unit (Hours/Days/Weeks)
* **sshPublicCerts**: SSH Public Certs for user (JSON array. See SSHPublicCerts Format)

**sshPublicCerts:**

* **keyid**: If present, revoke this cert
* **name**: Cert Name
* **Key**: 

Input (Assuming ```hostname=mavrix1```):
```json
{
    "description": "updated description for mavrix1"
}
```

Output:
```json
{
    "description": "updated description for mavrix1"
}
```

### /hosts/hostname (method=DELETE)
Deletes a hosts ```hostname```

Output (Assuming ```hostname=mavrix1```)
```json
"Deleted host: mavrix1"
```

## Groups

### /groups (method=GET)
Gets a list of all groups in the system.

Output:
```json
[
    {
        "cn": "mavrix2",
        "description": "mavrix2 Autocreated Group",
        "dn": "cn=mavrix2,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "mavrix3",
        "description": "mavrix3 Autocreated Group",
        "dn": "cn=mavrix3,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=mavrix3,ou=Hosts,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "mavrix4",
        "description": "mavrix4 Autocreated Group",
        "dn": "cn=mavrix4,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=mavrix4,ou=Hosts,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "mavrix5",
        "description": "mavrix5 Autocreated Group",
        "dn": "cn=mavrix5,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=mavrix5,ou=Hosts,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "AllHosts",
        "description": "All Hosts Group",
        "dn": "cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=admin,ou=people,dc=keyper,dc=example,dc=org",
            "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix3,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix4,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix5,ou=Hosts,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "demo_servers",
        "description": "demo_servers",
        "dn": "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix3,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix4,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix5,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=alice,ou=people,dc=keyper,dc=example,dc=org",
            "cn=bob,ou=people,dc=keyper,dc=example,dc=org",
            "cn=carol,ou=people,dc=keyper,dc=example,dc=org",
            "cn=erin,ou=people,dc=keyper,dc=example,dc=org",
            "cn=frank,ou=people,dc=keyper,dc=example,dc=org",
            "cn=grace,ou=people,dc=keyper,dc=example,dc=org"
        ]
    },
    {
        "cn": "KeyperAdmins",
        "description": "Keyper Administrators",
        "dn": "cn=KeyperAdmins,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=Manager,dc=keyper,dc=example,dc=org",
            "cn=admin,ou=people,dc=keyper,dc=example,dc=org"
        ]
    }
]
```
### /groups/groupname (method=GET)
Get a single group ```groupname``` from the system.

Output (assuming ```groupname=demo_servers```):
```json
[
    {
        "cn": "demo_servers",
        "description": "demo_servers",
        "dn": "cn=demo_servers,ou=groups,dc=keyper,dc=example,dc=org",
        "members": [
            "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix3,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix4,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix5,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=alice,ou=people,dc=keyper,dc=example,dc=org",
            "cn=bob,ou=people,dc=keyper,dc=example,dc=org",
            "cn=carol,ou=people,dc=keyper,dc=example,dc=org",
            "cn=erin,ou=people,dc=keyper,dc=example,dc=org",
            "cn=frank,ou=people,dc=keyper,dc=example,dc=org",
            "cn=grace,ou=people,dc=keyper,dc=example,dc=org"
        ]
    }
]
```

### /groups (method=POST)
Creates a new group.

Input:
```json
    {
        "cn": "dev_servers",
        "description": "dev_servers",
        "members": [
            "cn=mavrix1,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
            "cn=alice,ou=people,dc=keyper,dc=example,dc=org",
            "cn=grace,ou=people,dc=keyper,dc=example,dc=org"
        ]
    }
```

Output:
```json
{
    "cn": "dev_servers",
    "description": "dev_servers",
    "members": [
        "cn=mavrix1,ou=Hosts,dc=keyper,dc=example,dc=org",
        "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
        "cn=alice,ou=people,dc=keyper,dc=example,dc=org",
        "cn=grace,ou=people,dc=keyper,dc=example,dc=org"
    ]
}
```

### /groups/groupname (method=PUT)
Updates a group ```groupname```

Input (assuming ```groupname=dev_servers```):
```json
{
    "description": "updated description dev_servers",
    "members": [
        "cn=mavrix1,ou=Hosts,dc=keyper,dc=example,dc=org",
        "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
        "cn=alice,ou=people,dc=keyper,dc=example,dc=org",
        "cn=bob,ou=people,dc=keyper,dc=example,dc=org",
        "cn=grace,ou=people,dc=keyper,dc=example,dc=org"
    ]
}
```

Output:
```json
{
    "description": "updated description dev_servers",
    "members": [
        "cn=mavrix1,ou=Hosts,dc=keyper,dc=example,dc=org",
        "cn=mavrix2,ou=Hosts,dc=keyper,dc=example,dc=org",
        "cn=alice,ou=people,dc=keyper,dc=example,dc=org",
        "cn=bob,ou=people,dc=keyper,dc=example,dc=org",
        "cn=grace,ou=people,dc=keyper,dc=example,dc=org"
    ]
}
```

### /groups/groupname (method=DELETE)
Deletes a group ```groupname```

Output (assuming ```groupname=dev_servers```):
```json
"Deleted group: dev_servers"
```

## Utilities
Following utilities are available for authentication

### /authkeys (method=GET or POST)
Returns valid public keys for a user in text format. This method is called by ```AuthorizedKeysCommand``` script ```auth.sh```. Before returning the public key for the user, this method performs the following checks:
* Check Key in Key Revocation List using supplied fingerprint (KRL)
* Check Key expiry
* Check if Key is authorized for the host

**Input:**
Input for the method can be submitted by either including in URL or posted as HTTP Form variables (* denotes required field)

* **username***: Username. %u in sshd_config
* **host***: Hostname as defined in Keyper. 
* **fingerprint**: Key Fingerprint being used for the authentication. %f in sshd_config

KRL check is performed using ```fingerprint```.

**Output:**
List of SSH Public Keys in text format.

### /authprinc (method=GET or POST)
Returns Principals authorized to acces a host given username and Certificate fingerprint. This method is called by ```AuthorizedPrincipalsCommand``` script ```authprinc.sh```. Before returning the list of principals, this method performs the following checks:
* Check Cert in Key Revocation List (KRL) using the certificate serial number 
* Check Cert expiry
* Check if Principal in Cert is authorized to access the host

**Input:**
Input for the method can be submitted by either including in URL or posted as HTTP Form variables (* denotes required field)

* **username***: Username. %u in sshd_config
* **host***: Hostname as defined in Keyper. 
* **fingerprint***: Certificate Fingerprint being used for the authentication. %f in sshd_config
* **serial***: Serial number on the SSH Certificate. "%s" in sshd_config

KRL check is performed only when ```serial``` is part of the input.

**Output:**
List of Principals in text format.

### /hostca (method=GET or POST)
Returns Public Key for Key used by SSH CA to sign host certificate. This Key must be included on each SSH client ```known_hosts``` file for the client to trust Hosts' SSH certificates.

**Input:**
None

**Output:**
SSH CA Public Key user for host certificate signing.

### /userca (method=GET or POST)
Returns Public Key for Key used by SSH CA to sign user certificate. This Key must be included on each SSH server ```sshd_config``` file for against ```TrustedUserCAKeys``` to trust User's SSH certificates.

**Input:**
None

**Output:**
SSH CA Public Key user for user certificate signing.

### /krlca (method=GET or POST)
Returns Key Revocation List File. This file can be included in each SSH Server's ```sshd_config``` file as ```RevokedKeys``` parameter. This configuration is optional and should only be used if the performance of ```AuthorizedKeysCommand``` and/or ```AuthorizedPrincipalsCommand``` with Key/Cert is not acceptable.

**Input:**
None

**Output:**
SSH Key Revocation List File.

### /usercert (method=GET)
Returns signed certificate for a user. 

**Input:**
Input for the method can be submitted by including them in URL as part of HTTP GET request (* denotes required field)

* **username***: Username. 
* **keyid**: KeyID of the certificate as shown on the webconsole. 
* **fingerprint**: Certificate Fingerprint.

If the keyid or fingerprint is not part of the input then this method would return all the certificates for the user.

**Output:**
Signed Certificate for the user.

### /hostcert (method=GET)
Returns signed certificate for a host. 

**Input:**
Input for the method can be submitted by including them in URL as part of HTTP GET request (* denotes required field)

* **hostname***: Hostname. 
* **keyid**: KeyID of the certificate as shown on the webconsole. 
* **fingerprint**: Certificate Fingerprint.

If keyid or fingerprint is not part of the input then this method would return all the certificates for the host.

**Output:**
Signed Certificate for the host.
