## redmine-centos-ansible Dockerfile


This repository contains **Dockerfile** of running for [Redmine
AnsiblePlaybook Unofficial Cooking
Edition](https://github.com/y503unavailable/redmine-centos-ansible) for
[Docker](https://www.docker.com/)'s automated
build published to the
public [Docker Hub Registry](https://registry.hub.docker.com/).


### Base Docker Image

* [centos](https://hub.docker.com/_/centos/)


### Installation

1. Install [Docker](https://www.docker.com/).

2. Download [automated
build](https://hub.docker.com/r/twopackas/redmine-centos-ansible/) from
public [Docker Hub](https://hub.docker.com/):  
   `docker pull twopackas/redmine-centos-ansible`

### Build

Alternatively, you can build an image from Dockerfile:

```
$ git clone https://github.com/y503unavailable/redmine-centos-ansible.git
$ cd redmine-centos-ansible
$ docker build -t redmine-centos-ansible docker/Dockerfile
```

### Usage

```
$ docker run --privileged --name redmine-centos-ansible -d -p 8080:80
redmine-centos-ansible /sbin/init
$ docker exec -ti redmine-centos-ansible /bin/bash
# ansible-playbook -i hosts site.yml
```

After that, access to http://your-server:8080/redmine
