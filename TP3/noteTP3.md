![alt text](image.png)

ansible all -m ping --private-key=/home/carotee/cpe/id_rsa -u centos

$ ansible all -m yum -a "name=httpd state=present" --private-key=/home/carotee/cpe/id_rsa -u centos

$ ansible all -m shell -a 'echo "<html><h1>Hello World</h1></html>" >> /var/www/html/index.html' --private-key=/home/carotee/cpe/id_rsa -u centos --become

---
BECOME  !! 
$ ansible all -m shell -a 'echo "<html><h1>Hello World</h1></html>" >> /var/www/html/index.html' --private-key=/home/carotee/cpe/id_rsa -u centos --become

$ ansible all -m service -a "name=httpd state=started" --private-key=/home/carotee/cpe/id_rsa -u centos --become


----

ansible all -i inventories/setup.yml -m ping
/code 

 ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
sebastien.richard.takima.cloud | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.9",
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}

ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become


![alt text](image-1.png)

ansible all -i inventories/setup.yml -m yum -a "name=httpd state=present" --become


3$ ansible-playbook -i inventories/setup.yml playbook.yml

PLAY [all] ************************************************************************************************

TASK [Test connection] ************************************************************************************
ok: [sebastien.richard.takima.cloud]

PLAY RECAP ************************************************************************************************
sebastien.richard.takima.cloud : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0



ansible-galaxy init roles/docker
- Role roles/docker was created successfully


ansible-playbook playbooks/network-test.yaml -e "network=asa"


ansible-galaxy collection install community.docker

![alt text](image-2.png)

change l'ordre dans playbook important


docker network ls

sudo docker network inspect my-network

![alt text](image-3.png)

ansible-playbook -i inventories/setup.yml playbook.yml -vvv

API
![alt text](image-4.png)

database
![alt text](image-5.png)

docker exec <container_id> env


sudo docker exec database env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=d8a2f56b059a
POSTGRES_DB=db
POSTGRES_USER=usr
POSTGRES_PASSWORD=pwd
LANG=en_US.utf8
PG_MAJOR=14
PG_VERSION=14.1
PG_SHA256=4d3c101ea7ae38982f06bdc73758b53727fb6402ecd9382006fa5ecc7c2ca41f
PGDATA=/var/lib/postgresql/data
HOME=/root

docker images -a 


REPOSITORY                                   TAG       IMAGE ID       CREATED             SIZE
carottealpha/tp-devops-simple-api            latest    57c5bb3a16d7   About an hour ago   504MB
carottealpha/tp-devops-simple-api-httpd      latest    02809a79f842   25 hours ago        167MB
carottealpha/tp-devops-simple-api-database   latest    64b12c6bc6c4   25 hours ago        209MB
[root@ip-10-0-1-52 centos]# docker rmi 57c5bb3a16d7
Error response from daemon: conflict: unable to delete 57c5bb3a16d7 (must be forced) - image is being used by stopped container 9568591d4ccc
[root@ip-10-0-1-52 centos]# docker stop  9568591d4ccc
9568591d4ccc
[root@ip-10-0-1-52 centos]# docker rmi 57c5bb3a16d7
Error response from daemon: conflict: unable to delete 57c5bb3a16d7 (must be forced) - image is being used by stopped container 9568591d4ccc
[root@ip-10-0-1-52 centos]# docker rmi -f 57c5bb3a16d7
Untagged: carottealpha/tp-devops-simple-api:latest
Untagged: carottealpha/tp-devops-simple-api@sha256:de27840f8cb7ab6e6577cd3e1fc1bf59ee7da66d035b27efd4a10db867992650
Deleted: sha256:57c5bb3a16d74dffa4464de037e0817ec69f840aced8541efcbcdffe3ba2b84b
[root@ip-10-0-1-52 centos]# docker rmi -f 02809a79f842
Untagged: carottealpha/tp-devops-simple-api-httpd:latest
Untagged: carottealpha/tp-devops-simple-api-httpd@sha256:1354afc67a466df0ce351770e4f34978b85dbc896ac600b8d65a0bf89d7526bf
Deleted: sha256:02809a79f8426f9f55dc44f67417c603fe82e8e54f8ec709bb01864cd6adcf0f
[root@ip-10-0-1-52 centos]# docker rmi -f 64b12c6bc6c4
Untagged: carottealpha/tp-devops-simple-api-database:latest
Untagged: carottealpha/tp-devops-simple-api-database@sha256:db281988c5a5312867e2b521cdf65c02db3cb8217541c1c292a0b1ddaa4fd084
Deleted: sha256:64b12c6bc6c4965717431921508606b1a9966748aa4def874364975d35cc1656
[root@ip-10-0-1-52 centos]# docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

Supprimer tout !

docker rm -f $(docker ps -a -q) 

safe main.yml network

---
# tasks file for roles/network
- name: Connect multiple containers to the network
  community.docker.docker_network:
    name: my-network
    state: present
    containers:
      - database
      - httpd
      - api

docker exec -it pg cat /var/lib/postgresql/data/pg_hba.conf
# PostgreSQL Client Authentication Configuration File
# ===================================================
#
# Refer to the "Client Authentication" section in the PostgreSQL
# documentation for a complete description of this file.  A short
# synopsis follows.
#
# This file controls: which hosts are allowed to connect, how clients
# are authenticated, which PostgreSQL user names they can use, which
# databases they can access.  Records take one of these forms:
#
# local         DATABASE  USER  METHOD  [OPTIONS]
# host          DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostssl       DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostnossl     DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostgssenc    DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostnogssenc  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
#
# (The uppercase items must be replaced by actual values.)
#
# The first field is the connection type:
# - "local" is a Unix-domain socket
# - "host" is a TCP/IP socket (encrypted or not)
# - "hostssl" is a TCP/IP socket that is SSL-encrypted
# - "hostnossl" is a TCP/IP socket that is not SSL-encrypted
# - "hostgssenc" is a TCP/IP socket that is GSSAPI-encrypted
# - "hostnogssenc" is a TCP/IP socket that is not GSSAPI-encrypted
#
# DATABASE can be "all", "sameuser", "samerole", "replication", a
# database name, or a comma-separated list thereof. The "all"
# keyword does not match "replication". Access to replication
# must be enabled in a separate record (see example below).
#
# USER can be "all", a user name, a group name prefixed with "+", or a
# comma-separated list thereof.  In both the DATABASE and USER fields
# you can also write a file name prefixed with "@" to include names
# from a separate file.
#
# ADDRESS specifies the set of hosts the record matches.  It can be a
# host name, or it is made up of an IP address and a CIDR mask that is
# an integer (between 0 and 32 (IPv4) or 128 (IPv6) inclusive) that
# specifies the number of significant bits in the mask.  A host name
# that starts with a dot (.) matches a suffix of the actual host name.
# Alternatively, you can write an IP address and netmask in separate
# columns to specify the set of hosts.  Instead of a CIDR-address, you
# can write "samehost" to match any of the server's own IP addresses,
# or "samenet" to match any address in any subnet that the server is
# directly connected to.
#
# METHOD can be "trust", "reject", "md5", "password", "scram-sha-256",
# "gss", "sspi", "ident", "peer", "pam", "ldap", "radius" or "cert".
# Note that "password" sends passwords in clear text; "md5" or
# "scram-sha-256" are preferred since they send encrypted passwords.
#
# OPTIONS are a set of options for the authentication in the format
# NAME=VALUE.  The available options depend on the different
# authentication methods -- refer to the "Client Authentication"
# section in the documentation for a list of which options are
# available for which authentication methods.
#
# Database and user names containing spaces, commas, quotes and other
# special characters must be quoted.  Quoting one of the keywords
# "all", "sameuser", "samerole" or "replication" makes the name lose
# its special character, and just match a database or username with
# that name.
#
# This file is read on server startup and when the server receives a
# SIGHUP signal.  If you edit the file on a running system, you have to
# SIGHUP the server for the changes to take effect, run "pg_ctl reload",
# or execute "SELECT pg_reload_conf()".
#
# Put your actual configuration here
# ----------------------------------
#
# If you want to allow non-local connections, you need to add more
# "host" records.  In that case you will also need to make PostgreSQL
# listen on a non-local interface via the listen_addresses
# configuration parameter, or via the -i or -h command line switches.

# CAUTION: Configuring the system for local "trust" authentication
# allows any local user to connect as any PostgreSQL user, including
# the database superuser.  If you do not trust all your local users,
# use another authentication method.


# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
# IPv6 local connections:
host    all             all             ::1/128                 trust
# Allow replication connections from localhost, by a user with the
# replication privilege.
richa\Documents\IRC\4IRC\DevOps\git\DevOps\TP1>
