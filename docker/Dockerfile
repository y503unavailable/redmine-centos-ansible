FROM centos:7

MAINTAINER Tatsuya Saito <twopackas@gmail.com>

RUN yum install -y epel-release && \
    yum-config-manager --enable epel

RUN rm -f /etc/rpm/macros.image-language-conf && \
    sed -i '/^override_install_langs=/d' /etc/yum.conf && \
    yum -y reinstall glibc-common && \
    yum clean all

ENV LANG="ja_JP.UTF-8" \
    LANGUAGE="ja_JP:ja" \
    LC_ALL="ja_JP.UTF-8"

RUN yum install -y which && \
    yum install -y selinux-policy && \
    yum install -y firewalld && \
    yum install -y ansible git

WORKDIR /tmp
RUN git clone -b 3.4-unofficialcooking https://github.com/y503unavailable/redmine-centos-ansible.git

WORKDIR /tmp/redmine-centos-ansible
