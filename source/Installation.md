# Installation
## Requirements
Keyper is bundled as a docker container with alpine Linux as its base. To run keyper you need to have docker running on your server. Alternatively, you can also use podman.

## Quickstart
To run docker image using docker cli:
```console
$ docker run  -p 8080:80 -p 8443:443 -p 2389:389 -p 2636:636 --hostname <hostname> --env FLASK_CONFIG=prod -it dbsentry/keyper
```

To run the docker image using podman cli:
```console
$ podman run  -p 8080:80 -p 8443:443 -p 2389:389 -p 2636:636 --hostname <hostname> --env FLASK_CONFIG=prod -it docker.io/dbsentry/keyper
```

Either command starts a new container with keyper processes (openldap, gunicorn, nginx).

```important:: The above commands are for demo only and start a new container with LDAP server storing data within the container. That means on re-start, data would not persist. If you want data to persist, start the container with appropriate command line parameters. Refer to the documentation.
```  

```important:: By default, this image creates an LDAP server for the company **Example Inc** and the domain **keyper.example.org**. By default, all passwords are set to *superdupersecret*. All the default settings can be changed by passing parameters to docker/podman command line or using a parameter config file.
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
You must map nginx ports to your host port in order to access keyper web console.

## Environment Variables
Following environment variables can be set as part of the docker cli:
```eval_rst
+----------------------------+--------------------------+----------------------------+
| Environment Variable       | Description              | Default                    |
+============================+==========================+============================+
| LDAP_PORT                  | ldap bind port           | 389                        |
+----------------------------+--------------------------+----------------------------+
| LDAPS_PORT                 | ldaps bind port          | 636                        |
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
| LDAP_UID                   | ldap user UID            | 10100                      |
+----------------------------+--------------------------+----------------------------+
| LDAP_GID                   | ldap user GID            | 10100                      |
+----------------------------+--------------------------+----------------------------+
| NGINX_UID                  | nginx user UID           | 10080                      |
+----------------------------+--------------------------+----------------------------+
| NGINX_GID                  | nginx user GID           | 10080                      |
+----------------------------+--------------------------+----------------------------+
```

```important:: We recommend setting the HOSTNAME parameter on cli. By default, the container creates a self-signed SSL certificate per the hostname. We recommend you use a CA issued certificate in production.
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

On subsequent starts, keyper checks for the existence of files in /etc/openldap/slapd.d and /var/lib/openldap/openldap-data, and if it finds any files there, it assumes an existing database and starts the slapd service. Otherwise, it creates a new ldap database with the password specified via environment variable **LDAP_ADMIN_PASSWORD**.

## Services
Keyper is built using alipne linux as its base. When started, it launches three services:
* slapd: LDAP server
* gunicorn: REST API developed using python flask
* nginx: Frontend Web Application developed using VueJS

Keyper starts these services using runit init scheme. runit is a cross-platform Unix init scheme with service supervision. It fits perfectly within docker container framework keeping the size of container small.

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

## Logs
Keeping in line with the [12-factor](https://12factor.net/) app methodology for the container, all the service logs are sent to the stdout of the running container. In addition, audit log for openldap is stored under /var/log/openldap/auditlog.ldif

## Certificate
By default, keyper generates a self-signed certificate for the HOSTNAME specified during startup. This certificate is used by slapd (ldaps) and nginx (https). All the files are located under /container/service/nginx/assets/certs. For production use, we recommend using CA issued certificate. You can set your CA issued certificate during run time, by mounting a directory containing those files to /container/service/nginx/assets/certs and adjust their name with the following environment variables:

```console
$ docker run --hostname keyper.example.org --mount sources=certs,target=/container/service/nginx/assets/certs \
--env LDAP_TLS_CRT_FILENAME=my-cert.crt \
--env LDAP_TLS_KEY_FILENAME=my-cert.key \
--env LDAP_TLS_CA_CRT_FILENAME=the-ca.crt \
--detach dbsentry/keyper
```

## Passwords
Todo