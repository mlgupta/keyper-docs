# REST API
You can use Keyper REST API to create calls to integrate with Keyper.

By default, all requests must be sent to http(s)://hostname/api/

All API access is over http or https (https is recommended). All data is sent and received as JSON.

Authentication creates a JWT token, which is used to maintain the session. For each call JWT token must be added as part of HTTP header with name **Authorization** and value **Bearer Token**

## Authentication
Following methods are available under authentication

### /login (method=POST)
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
Output:
```json
{
  "msg": "Successfully logged out"
}
```
### /refresh (method=POST)
Output:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1OTkxNzE1ODQsIm5iZiI6MTU5OTE3MTU4NCwianRpIjoiOWI4NjEwNmMtMGU5Yi00N2IxLWIwYmYtMTA0YWEwZDFlNDllIiwiZXhwIjoxNTk5MTcyNDg0LCJpZGVudGl0eSI6Im1hbmlzaCIsImZyZXNoIjpmYWxzZSwidHlwZSI6ImFjY2VzcyIsInVzZXJfY2xhaW1zIjoie3JvbGU6IGtleXBlcl9hZG1pbn0ifQ.e7b5Tq1PnG64XR2sDh7HGH7z1SQ_Sk4eOib2ZUCF0Fw"
}
```

## Users

### /users (method=GET)
### /users/username (method=GET)
### /users (method=POST)
### /users/username (method=PUT)
### /users/username (method=DELETE)

## Hosts

### /hosts (method=GET)
### /hosts/hostname (method=GET)
### /hosts (method=POST)
### /hosts/hostname (method=PUT)
### /hosts/hostname (method=DELETE)

## Groups

### /groups (method=GET)
### /groups/groupname (method=GET)
### /groups (method=POST)
### /groups/groupname (method=PUT)
### /groups/groupname (method=DELETE)



