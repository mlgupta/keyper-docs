# OpenLDAP
Keyper by default creates an LDAP database for the company **Example Inc.** and the domain **keyper.example.org**. You can change the defaults by specifying company, domain, and password as environment variables.

By default LDAP database is created with the following structure:

    . dc=keyper,dc=example,dc=org
    |
    |-- cn=Manager,dc=keyper,dc=example,dc=org
    |
    |-- ou=people,dc=keyper,dc=example,dc=org
    |   |-- cn=admin,ou=people,dc=keyper,dc=example,dc=org
    |
    |-- ou=hosts,dc=keyper,dc=example,dc=org
    |
    |-- ou=groups,dc=keyper,dc=example,dc=org
    |   |-- cn=KeyperAdmins,ou=groups,dc=keyper,dc=example,dc=org
    |   |-- cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org

* **cn=Manager,dc=keyper,dc=example,dc=org** is the superuser of LDAP directory.
* All the users are created under **ou=people,dc=keyper,dc=example,dc=org** 
* User **cn=admin,ou=people,dc=keyper,dc=example,dc=org** gets created by default on start of the system.
* All hosts gets created under **ou=hosts,dc=keyper,dc=example,dc=org**
* All groups gets created under **ou=groups,dc=keyper,dc=example,dc=org**
* **cn=KeyperAdmins,ou=groups,dc=keyper,dc=example,dc=org** and **cn=AllHosts,ou=groups,dc=keyper,dc=example,dc=org** gets created by default.
