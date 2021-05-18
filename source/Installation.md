# Installation
## Requirements
Keyper is a docker container with alpine Linux as its base. To run Keyper you need to have docker running on your server. You can also use podman, which is bundled natively with RHEL and its variants, and it does not require any running daemon.

## Quickstart
To run docker image using docker cli:
```console
$ docker run  -p 8080:80 -p 8443:443 -p 2389:389 -p 2636:636 --hostname <hostname> --env FLASK_CONFIG=prod -it dbsentry/keyper
```

To run the docker image using podman cli:
```console
$ podman run  -p 8080:80 -p 8443:443 -p 2389:389 -p 2636:636 --hostname <hostname> --env FLASK_CONFIG=prod -it docker.io/dbsentry/keyper
```

Either command starts a new container with keyper processes (OpenLDAP, Gunicorn, Nginx).

```important:: The above commands are for demo only and start a new container with LDAP server storing data within the container. That means on re-start, data would not persist. If you want data to persist, start the container with appropriate command line parameters. Refer to the documentation.
```  

```important:: By default, this image creates an LDAP server for the company **Example Inc** and the domain **keyper.example.org**. By default, all passwords are set to *superdupersecret*. All the default settings can be changed by passing parameters to the docker/podman command line or using a parameter config file.
```
## Ports
Keyper starts services on the following ports:
```eval_rst
+-------------+-------------+
| Service     | Port(s)     |
+=============+=============+
| slapd       | 389, 636    |
+-------------+-------------+
| gunicorn    | 8000        |
+-------------+-------------+
| nginx       | 80, 443     |
+-------------+-------------+
```
You must map nginx ports to your host port to access keyper web console.

## Environment Variables
Following environment variables can be set as part of the docker CLI:
```eval_rst
+----------------------------+--------------------------+----------------------------+
| Environment Variable       | Description              | Default                    |
+============================+==========================+============================+
| LDAP_PORT                  | LDAP bind port           | 389                        |
+----------------------------+--------------------------+----------------------------+
| LDAPS_PORT                 | LDAPS bind port          | 636                        |
+----------------------------+--------------------------+----------------------------+
| LDAP_ORGANIZATION_NAME     | Name of the Organization | Example Inc.               |
+----------------------------+--------------------------+----------------------------+
| LDAP_DOMAIN                | LDAP Domain              | keyper.example.org         |
+----------------------------+--------------------------+----------------------------+
| LDAP_ADMIN_PASSWORD        | Admin password on LDAP   | superdupersecret           |
+----------------------------+--------------------------+----------------------------+
| LDAP_TLS_CA_CRT_FILENAME   | CA Cert File Name        | ca.crt                     |
+----------------------------+--------------------------+----------------------------+
| LDAP_TLS_CRT_FILENAME      | Cert File Name           | server.crt                 |
+----------------------------+--------------------------+----------------------------+
| LDAP_TLS_KEY_FILENAME      | Cert Key File Name       | server.key                 |
+----------------------------+--------------------------+----------------------------+
| LDAP_TLS_DH_PARAM_FILENAME | DH Param File Name       | dhparam.pem                |
+----------------------------+--------------------------+----------------------------+
| LDAP_TLS_CIPHER_SUITE      | Default Cipher Suite     | TLSv1.2:HIGH:!aNULL:!eNULL |
+----------------------------+--------------------------+----------------------------+
| FLASK_CONFIG               | Flask Config (dev/prod)  | prod                       |
+----------------------------+--------------------------+----------------------------+
| HOSTNAME                   | Hostname                 | <docker generated>         |
+----------------------------+--------------------------+----------------------------+
| NGINX_UID                  | nginx user UID           | 10080                      |
+----------------------------+--------------------------+----------------------------+
| NGINX_GID                  | nginx user GID           | 10080                      |
+----------------------------+--------------------------+----------------------------+
| SSH_CA_HOST_KEY            | CA KEY for Host Cert     | ca_host_key                |
+----------------------------+--------------------------+----------------------------+
| SSH_CA_USER_KEY            | CA KEY for User Cert     | ca_user_key                |
+----------------------------+--------------------------+----------------------------+
| SSH_CA_KEY_TYPE            | CA Key type (rsa/dsa/    | rsa                        |
|                            | ecdsa/ed25519)           |                            |
+----------------------------+--------------------------+----------------------------+
| SSH_CA_KRL_FILE            | CA KRL File              | ca_krl                     |
+----------------------------+--------------------------+----------------------------+
| SSH_CA_TMP_DELETE_FLAG     | CA delete temp files     | True                       |
+----------------------------+--------------------------+----------------------------+
```

```important:: We recommend setting the HOSTNAME parameter on CLI. By default, the container creates a self-signed SSL certificate per the hostname. We recommend you use a CA issued certificate in production.
```  

```important:: Since version 0.2.1 all services (i.e. OpenLDAP, Gunicorn, and Nginx run as user nginx. 
```  

## Data Persistence
By default, keyper creates openldap database within container under /var/lib/openldap/openldap-data and /etc/openldap/slapd.d. For data to persist after a restart, we need to present local docker volumes as a parameter. Something like this:
```console
$ docker volume create slapd.d
$ docker volume create openldap-data
$ docker run -p 80:80 -p 443:443 -p 389:389 -p 636:636 --hostname <hostname> --mount source=slapd.d,target=/etc/openldap/slapd.d --mount source=openldap-data,target=/var/lib/openldap/openldap-data -it dbsentry/keyper
```  
For more information about docker data volume, please refer to:
> [https://docs.docker.com/engine/tutorials/dockervolumes/](https://docs.docker.com/engine/tutorials/dockervolumes/)

On subsequent starts, keyper checks for the existence of files in /etc/openldap/slapd.d and /var/lib/openldap/openldap-data, and if it finds any files there, it assumes an existing database and starts the slapd service. Otherwise, it creates a new LDAP database with the password specified via environment variable **LDAP_ADMIN_PASSWORD**.

## Services
Keyper is built using alipne Linux as its base. When started, it launches three services:
* slapd: LDAP server
* gunicorn: REST API developed using python flask
* nginx: Frontend Web Application developed using VueJS

Keyper starts these services using runit init scheme. runit is a cross-platform Unix init scheme with service supervision. It fits perfectly within a docker container framework keeping the size of the container small.

To manage these services, connect to your keyper docker container and issue following commands:

```console
$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                                                       NAMES
f439774327a5        dbsentry/keyper       "/container/tools/run"   16 hours ago        Up 16 hours         0.0.0.0:8080->80/tcp, 0.0.0.0:2389->389/tcp, 0.0.0.0:8443->443/tcp, 0.0.0.0:2636->636/tcp   priceless_clarke
66d33bbdd32c        jenkinsci/blueocean   "/sbin/tini -- /usr/…"   2 weeks ago         Up 2 weeks          0.0.0.0:50000->50000/tcp, 0.0.0.0:8000->8080/tcp                                            jenkins-blueocean
$ docker exec -it f439774327a5 /bin/sh
/ # 
/ # export SVDIR=/container/run/process
/ # cd /container/run/process
/container/run/process # sv status *
run: gunicorn: (pid 61) 56789s
run: nginx: (pid 62) 56789s
run: slapd: (pid 63) 56789s
/container/run/process # sv status slapd
run: slapd: (pid 63) 56803s
/container/run/process # sv status gunicorn
run: gunicorn: (pid 61) 56807s
/container/run/process # sv status nginx
run: nginx: (pid 62) 56811s
```

To stop a service:
```console
/container/run/process # sv stop nginx
ok: down: nginx: 1s, normally up
```

To start a service:
```console
/container/run/process # sv start nginx
ok: run: nginx: (pid 122) 0s
```

To restart a service:
```console
/container/run/process # sv restart nginx
ok: run: nginx: (pid 116) 0s
/container/run/process # 
```

If any of the above service crashes or terminates. runit being a supervisor starts it again:
```console
/container/run/process # ps -ef
PID   USER     TIME  COMMAND
    1 root      0:00 {run} /usr/bin/python3 -u /container/tools/run
   57 root      0:00 /sbin/runsvdir -P /container/run/process
   58 root      0:00 runsv gunicorn
   59 root      0:00 runsv nginx
   60 root      0:00 runsv slapd
   61 root      0:06 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   63 ldap      0:00 /usr/sbin/slapd -h ldap:/// ldaps:/// -u ldap -g ldap -d 256
   71 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   72 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   73 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   74 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
  106 root      0:00 /bin/sh
  128 root      0:00 nginx: master process /usr/sbin/nginx -g daemon off;
  131 nginx     0:00 nginx: worker process
  137 root      0:00 ps -ef
/container/run/process # pkill nginx
/container/run/process # ps -ef
PID   USER     TIME  COMMAND
    1 root      0:00 {run} /usr/bin/python3 -u /container/tools/run
   57 root      0:00 /sbin/runsvdir -P /container/run/process
   58 root      0:00 runsv gunicorn
   59 root      0:00 runsv nginx
   60 root      0:00 runsv slapd
   61 root      0:06 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   63 ldap      0:00 /usr/sbin/slapd -h ldap:/// ldaps:/// -u ldap -g ldap -d 256
   71 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   72 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   73 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
   74 nginx     0:00 {gunicorn} /var/www/keyper/env/bin/python3 /var/www/keyper/env/bin/gunicorn -w 4 ap
  106 root      0:00 /bin/sh
  139 root      0:00 nginx: master process /usr/sbin/nginx -g daemon off;
  142 nginx     0:00 nginx: worker process
  143 root      0:00 ps -ef
/container/run/process # 
```

```important:: Since version 0.2.1 all services (i.e. OpenLDAP, Gunicorn, and Nginx run as user nginx. 
```  

## Logs
Keeping in line with the [12-factor](https://12factor.net/) app methodology for the container, all the service logs are sent to the stdout of the running container. In addition, audit log for OpenLDAP is stored under /var/log/openldap/auditlog.ldif

## SSL Certificate
By default, keyper generates a self-signed certificate for the HOSTNAME specified during startup. This certificate is used by slapd (ldaps) and nginx (https). All the files are located under /container/service/nginx/assets/certs. For production use, we recommend using CA issued certificate. You can set your CA issued certificate during run time, by mounting a directory containing those files to /container/service/nginx/assets/certs. This directory must contain the following files:
* my-server.key
* my-server.crt
* the-ca.crt
* dhparam.pem 

```console
$ docker run --hostname keyper.example.org --mount sources=certs,target=/container/service/nginx/assets/certs \
  --env LDAP_TLS_CRT_FILENAME=my-cert.crt \
  --env LDAP_TLS_KEY_FILENAME=my-cert.key \
  --env LDAP_TLS_CA_CRT_FILENAME=the-ca.crt \
  --detach dbsentry/keyper
```

When you renew your certificate, you can place the renewed certificate in the same folder, and restart keyper. Please note that the above names for keys and certificates were changed from default to show that custom filenames can be used. However, we recommened that you use the default names of ```server.key```, ```server.crt```, and ```ca.crt```.

## SSH Certificate Authority (CA)
By default, SSH CA gets created under ```/etc/sshca``` on the running container. Keyper creates two separate signing keys: 
* ```ca_user_key``` is used to sign users' certificates.
* ```ca_host_kety``` is used to sign host certificates. 

Keyper also creates a Key Revocation List (KRL) file in OpenSSH format (```ca_krl```).

For content under ```/etc/sshca``` to persist you should present a local docker volume.  

```console
/etc/sshca # ls -la
total 20
drwxr-xr-x    1 nginx    nginx          115 Oct 29 17:30 .
drwxr-xr-x    1 root     root           118 Oct 29 17:30 ..
-rw-------    1 nginx    nginx         2602 Oct 29 17:30 ca_host_key
-rw-r--r--    1 nginx    nginx          571 Oct 29 17:30 ca_host_key.pub
-rw-r--r--    1 nginx    nginx           44 Oct 29 17:30 ca_krl
-rw-------    1 nginx    nginx         2602 Oct 29 17:30 ca_user_key
-rw-r--r--    1 nginx    nginx          571 Oct 29 17:30 ca_user_key.pub
drwxr-xr-x    2 nginx    nginx            6 Oct 29 17:30 tmp
```

You can also download CA public keys and KRL using API calls ```/hostca```, ```/userca```, and ```/crlca``` respectively.

## Passwords
Todo