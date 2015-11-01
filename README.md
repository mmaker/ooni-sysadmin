# OONI sysadmin

In here shall go all code related to system administration of the OONI
infrastructure.


# OONI infrastructure overview


## Backend infrastructure

hostname | role | maintainers | notes | cost |
------------- | ------------- | ----------- |----------- |----------- |
pipeline.infra.ooni.nu | pipeline, canonical reports mirror | hellais, andaraz| | 50 USD/month |
bouncer.infra.ooni.nu         | canonical bouncer, canonical collector |hellais, andaraz| | 0 EUR/month | 
vps770.greenhost.nl  | collector bridge reachability,  |hellais, andaraz| | 0 EUR/month |
ooni-1.default.orgtech.uk0.bigv.io | collector, reports mirror |hellais, andaraz| | 0 EUR/month |
manager.infra.ooni.nu         | amabari manager, hadoop single node cluster |hellais | | 60 USD/month | 
ooni-deb         | debian repository, collector |hellais, aagbsn | | ?? | 
marcello         | development/playground |hellais | | 0 EUR/month | 
ooni-tpo-collector | backup collector |hellais, aagbsn | | 0 EUR/month |

## Probing infrastructure
hostname | role | maintainers | notes | cost | next due date
-------- | ---- | ---------- | ------ |----- |------------- |
probe ru | bridge reachability study | hellais, griffin | | 0 EUR/month | |
probe ua | bridge reachability study | hellais, griffin| | 0 EUR/month | |
probe cn | bridge reachability study | hellais | | 10 USD/month | 2015-05-10
probe ir | bridge reachability study | hellais | | 30 USD/month | 2015-03-21


## Donate to support OONI infrastructure

Send bitcoins to:
![bitcoin address](http://i.imgur.com/ILdOJ3V.png)
```
16MAyUCxfk7XiUjFQ7yawDhbGs43fyFxd
```

# Ansible roles

This is the section for using ansible roles to install and configure OONI
components.

Note: In order to use ansible you need Python 2.4 or later, but if you are
running less than Python 2.5 on the remote hosts, you will also need the
[python-simplejson]
(https://docs.ansible.com/ansible/intro_installation.html#managed-node-requirements)
package.

For Debian like systems you could use something similar to:
```
ansible host-group -i hosts-inventory-file -m raw -a \
"apt-get update && apt-get -y install python-simplejson"
```

## ooni-backend

This ansible role installs ooni-backend from git repo via pip in a python
virtual environment (virtualenv). The ooni-backend service is being started and
controlled via the [Supervisor](http://supervisord.org) service. This role
can be used for Debian releases and has been tested in Wheesy and Jessie Debian
releases.

### execute role

```
ansible-playbook -i "hosts-inventory" ansible/install-oonibackend.yml -v
```
Note: The inventory file should include hosts in custom group.

## ooniprobe

This ansible role install ooniprobe via apt (stretch repo) or via pip from this
the git repo. This role can be used for Debian releases and has been tested in
Wheesy and Jessie Debian releases. By seting the conditional variable
`set_ooniprobe_pip` to true ooniprobe will be istalled from git via pip.

### execute role

```
ansible-playbook -i "hosts-inventory" ansible/install-ooniprobe.yml -v
```

# Instructions

Unless otherwise indicated the instructions are for debian wheezy.

## Setting up docker

```
# Install kernel >3.8
echo "deb http://http.debian.net/debian wheezy-backports main" >> /etc/apt/sources.list
apt-get update
apt-get install -t wheezy-backports linux-image-amd64
apt-get install curl
# Install docker
curl -sSL https://get.docker.com/ | sh
```

# Disaster recovery procedure

This is the procedure to restore all the system of the OONI infrastructure if
everything goes bad.

## Restore OONI pipeline

```
cd ansible/server_migration/
ansible-playbook -i hosts pipeline.yml -vvvv
```
