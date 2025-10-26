# Firefox

- https://www.youtube.com/watch?v=rCdd6u_FXnQ&list=PLjmuhzYhitSM7iVmmDax6GeYYL2v-CzCL

```bash
docker pull centos:7
xhost local:root # This permits the root user on the local machine to connect to X windows display.
xhost + # /!\ A EVITER : La commande "xhost +" désactive le contrôle d'accès à votre serveur X11
docker container run -it --name=sap --env="DISPLAY" --net=host -v /tmp:/tmp centos:7
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
yum install -y gvim
yum install -y xclock
dbus-uuidgen > /etc/machine-id

# Error: no DISPLAY
gvim 
```


```bash
env | grep '^DISPLAY='
xhost local:root # This permits the root user on the local machine to connect to X windows display.
xhost + # /!\ A EVITER : La commande "xhost +" désactive le contrôle d'accès à votre serveur X11
docker start sap
docker exec -ti sap bash
xclock
```

https://github.com/gizaoui/Tools/tree/main/debian

```bash
# Dockerfile
FROM centos:7
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
RUN yum install -y gvim
CMD ["/usr/bin/gvim"]
sh -c "sleep infinity"
```

```bash
docker build -t mygvim .
docker container run -it --env="DISPLAY" --net=host -v /tmp:/tmp mygvim
```

---

## bind mount


```bash
# en root
mkdir /data
mkdir /data2
touch /data/toto
findmnt # liste les pts de mountage
mount --bind /data/ /data2/
findmnt # liste les pts de mountage
ls /data2 # présebce du fichier 'toto'
umount /data2
rm -rf /data2 /data
```

```bash
docker run -d --name c1 --mount type=bind,source=/data/,destination=/usr/share/ngnix/html nginx:latest

docker exec -ti c1 bash
docker inspect --format "{{.Mount}}" c1 # Liste des mounts
```

## Volume Docker

```bash
docker volume create myvolume
docker volume ls
docker run -d --name c2 --mount type=volume,src=myvolume,destination=/usr/share/ngnix/html nginx:latest
docker exec -ti c2 bash
```

   
## TMPFS

```bash
docker run -d --name c3 --mount type=tmpfs,destination=/usr/share/ngnix/html nginx:latest
docker exec -ti c3 bash
```

---

```bash
useradd -m usertst --uid 1003 --shell /bin/bash
id usertst # usertst:x:1002:1002::/home/usertst:/bin/bash
userdel -r usertst
```

```bash
FROM centos:7
RUN dbus-uuidgen > /etc/machine-id
RUN useradd -m gizaoui --uid 1001 --shell /bin/bash
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
RUN yum install -y gvim firefox
```


```bash
xhost local:gizaoui
docker builder prune && docker build -t mycentos .
dpsaq | xargs docker rm -f # Suppression des container
docker container run -it --name c1 -u $USER --env="DISPLAY" --net=host -v '/tmp:/tmp' mycentos
docker container run -it --name c1 -u $USER --env="DISPLAY" --net=host -v '/home/gizaoui/data:/tmp' mycentos # creation de 'data' en 'root'
docker container run -it --name c1 -u $USER --env="DISPLAY" --net=host  --mount type=bind,source=/home/gizaoui,destination=/home/gizaoui mycentos
docker start c1
docker exec -ti c1 bash
```












  469  for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do  apt-get remove $pkg; done
  470  apt-get install ca-certificates curl
  471   install -m 0755 -d /etc/apt/keyrings
  472  curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
  473  chmod a+r /etc/apt/keyrings/docker.asc
  474  echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  475    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |    tee /etc/apt/sources.list.d/docker.list > /dev/null
  476  cat /etc/apt/sources.list.d/docker.list
  478   apt-get update
  479   apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  480  systemctl status docker
  481  systemctl restart docker
  482  systemctl status docker
  483  systemctl enable docker
  486  chmod 777 /var/run/docker.sock
  489  usermod -a -G docker gizaoui
  490  groupmod -g gizaoui docker


 