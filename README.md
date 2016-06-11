vcc-base
===

[![Build Status](https://travis-ci.org/announce/vcc-base.svg?branch=master)](https://travis-ci.org/announce/vcc-base)


## Introduction

#### TL;DR
* This Ansible playbook constructs VCC research DB server into single server node.


#### Related Papers
* *Henning Perl, Sergej Dechand, Matthew Smith, Daniel Arp, Fabian Yamaguchi, Konrad Rieck, Sascha Fahl, and Yasemin Acar. 2015. VCCFinder: Finding Potential Vulnerabilities in Open-Source Projects to Assist Code Audits. In Proceedings of the 22nd ACM SIGSAC Conference on Computer and Communications Security (CCS '15). ACM, New York, NY, USA, 426-437. DOI=http://dx.doi.org/10.1145/2810103.2813604*


## Requirements

#### Environment
* Host: Ubuntu 14.04 LTS (Trusty Tahr)
* Client: Mac OS X 10.11

#### Runtime and Tools
- Python v2.7
- Vagrant v1.8 (optional)
- ssh-copy-id (optional)
- Postico (optional)

```bash
# Example on OS X with Homebrew
xcode-select --install \
&& brew update \
&& brew install \
  python \
  ssh-copy-id \
  Caskroom/cask/vagrant \
  Caskroom/cask/postico
```


## Setups

#### Setup Client

* Install Ansible and its 3rd party packages to your local client

```bash
$ pip install --requirement requirements.txt
$ ansible-galaxy install --role-file=role_packages.yml
```

* Edit ``~/.ssh/config`` corresponding to hosts (No need for Vagrant)

```bash
$ cat <<EOF >> ~/.ssh/config
Host vcc-base-init001
    User root
    HostName example.com
    IdentitiesOnly yes

Host vcc-base-general001
    User admin
    HostName example.com
EOF
```

#### Setup Vagrant Server for local development environment

* Run Vagrant commands after setting up client

```bash
$ vagrant up  # Run this for the first time
$ vagrant provision  # Run this when `vagrant up` had problem due to network error, etc
$ vagrant ssh  # Login to the Vagrant Machine
$ vagrant suspend  # Stop the Vagrant Machine
$ vagrant resume  # Start the Vagrant Machine
$ vagrant destroy --force && vagrant up  # Run this to reset everything
```

#### Setup Host Server for production environment

* Run commands below, and wait for a few hours or so (up to the server spec)
    * Use `--check` option if you prefer to run as dry-run mode

```bash
$ ssh-copy-id -i ~/.ssh/id_rsa.pub root@example.com
$ ansible-playbook site.yml --inventory-file hosts --private-key="~/.ssh/id_rsa" --limit="init_server"
$ ansible-playbook site.yml --inventory-file hosts --private-key="~/.ssh/id_rsa" --limit="general_server"
```

#### How to know what are these commands above doing?

```bash
$ ansible-playbook site.yml --list-tasks
$ ansible-playbook site.yml --list-hosts
```

## Import Data

* On server (e.g. vagrant), download dump data and restore it to PostgreSQL like by commands below

```
$ cd /tmp
$ wget https://dl.dropboxusercontent.com/u/6998388/vcc_data/vcc-database.dump.bz2
$ bzip2 -dc vcc-database.dump.bz2 > /vagrant/var/vcc-database.dump
$ psql -U postgres --set ON_ERROR_STOP=on -f /vagrant/var/vcc-database.dump
```


## Lookup data on Postico

* Postico is a PostgreSQL Client for Mac
    * Screenshots below are the example how to setup to connect the database on Vagrant

![Postico Settings](https://dl.dropboxusercontent.com/u/6998388/vcc_data/postico_settings.png)

![Postico Commits Table](https://dl.dropboxusercontent.com/u/6998388/vcc_data/postico_commits.png)
