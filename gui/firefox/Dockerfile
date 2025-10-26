# docker build -t mycentos .
FROM centos:7
RUN dbus-uuidgen > /etc/machine-id
RUN useradd -m gizaoui --uid 1001 --shell /bin/bash
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
RUN yum install -y gvim firefox
