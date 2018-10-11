# PowerDNS - Ansible
# Author: TonyChG

Vagrant infra provision with ansible

PowerDNS is use to create a domain.local DNS zone.


## Requirements

- ```ansible```
- ```vagrant```
- ```nfs-utils``` (optionnel)

## Preréquis

```
> systemctl start nfs-server
# On error nfs version
> journalctl -u nfs-server -f
> vim /etc/nfs.conf
# Uncomment [nfsd]
# And udp=y
```

## Usage

```
> vagrant up
# Connect to client
> vagrant ssh client
[vagrant@client ~]$ cat /data/group_vars/all

# Globals
default_user: "vagrant"
default_zone: "domain.local"

# Docker
docker_yum_repo_url: "https://download.docker.com/linux/centos/docker-ce.repo"
docker_compose_url: "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-Linux-x86_64"
docker_yum_repo_enable_edge: false
docker_yum_repo_enable_test: false
docker_edition: "ce"
docker_ip: "172.16.2.4"
docker_hostname: "docker"

# PowerDNS database
pdns_mysql_user: "pdns"
pdns_mysql_password: "password1"
pdns_mysql_database: "pdns"

pdns_auth_ip: "172.16.2.3"
pdns_hostname: "ns1"

# Client
client_ip: "172.16.2.50"

[vagrant@client ~]$ dig +short domain.local
172.16.2.3
[vagrant@client ~]$ dig +short docker.domain.local
172.16.2.4
> vagrant ssh ns1
[vagrant@ns1 ~]$
[vagrant@ns1 ~]$ mysql -updns -ppassword1 pdns --execute "select name, type, content from records;"
+------------------------+------+-----------------------------------------------------+
| name                   | type | content                                             |
+------------------------+------+-----------------------------------------------------+
| domain.local           | SOA  | localhost ns1.domain.local 1 10380 3600 604800 3600 |
| domain.local           | NS   | dns-us1.powerdns.net                                |
| domain.local           | NS   | dns-eu1.powerdns.net                                |
| www.domain.local       | A    | 172.16.2.3                                          |
| domain.local           | A    | 172.16.2.3                                          |
| ns1.domain.local       | A    | 172.16.2.3                                          |
| docker.domain.local    | A    | 172.16.2.4                                          |
| localhost.domain.local | A    | 127.0.0.1                                           |
+------------------------+------+-----------------------------------------------------+

[vagrant@ns1 ~]$ sudo egrep 'recursor' /etc/pdns/pdns.conf
# recursor      If recursion is desired, IP address of a recursing nameserver
recursor=8.8.8.8
[vagrant@ns1 ~]$
[vagrant@ns1 ~]$ sudo egrep 'allow-recursion' /etc/pdns/pdns.conf
# allow-recursion       List of subnets that are allowed to recurse
allow-recursion=127.0.0.1,172.16.2.0/24
```
