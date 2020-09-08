# Web Console
To access keyper web console, point your browser to the hostname you specified during startup. By default, the container listens on both port 80 (http) and port 443 (https). If you are using a default self-generated certificate, you'll get a warning from the browser. We recommend the use of a CA issued certificate in the production environment.

## Login
![Login](_static/login.png)

Default administrator user is **admin**. If you specified a password using environment variable **LDAP_ADMIN_PASSWORD** use that password. If you did not specify a password using environment varible **LDAP_ADMIN_PASSWORD** use **superdupersecret** as password.

```important:: Passwords are set during the first start within openldap database. If using data persistence, which you should, the same password should be used during subsequent startup of the container.
```  

## Users
This is where you add/delete/modify users and their SSH Public Keys. Keyper users have either of two role:
* KEYPER_ADMIN or 
* KEYPER_USER
A user gets the KEYPER_ADMIN role if s/he is part of the *KeyperAdmins* group. Only users part of KEYPER_ADMIN groups can add/delete/modify users/groups/hosts.
A user with the role of KEYPER_USER can only modify his/her profile and add/delete SSH public keys.

By default, keyper comes with only one user: *admin*. This user cannot be deleted.

![User List](_static/user_list.png)

### Add User
![User Add](_static/user_add.png)
Required fields are:
* username
* email
* Last Name

```important:: Username must be same as the username on linux systems.
```  

### Edit User
![User Edit](_static/user_edit.png)

### User SSH Public Keys
![User Edit - SSH Key List](_static/user_edit_key_list.png)

![User Edit - Add SSH Key](_static/user_edit_add_key.png)

### Edit User Profile
![User Profile](_static/user_profile.png)

## Hosts
![Host List](_static/host_list.png)

### Add Host
![Host Add](_static/host_add.png)
When a host is added, a corresponding group with the same name is added under Groups. In addition, when a host is created it automatically gets added to *AllHosts* group

```important:: Hostname must be same as the hostname of the linux systems. i.e. what is returned by *hostname -s*
```  

## Groups
By default keypers comes with two groups:
* KeyperAdmins: Any user part of this group gets KEYPER_ADMIN role. This group cannot be deleted.
* AllHosts: This is a self managed group, where a host is automatically added/deleted when corresponding host is added or deleted.
![Groups List](_static/group_list.png)

### Add Group
![Group Add](_static/group_add.png)
