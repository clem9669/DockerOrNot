[Question-1](https://github.com/clem9669/DockerOrNot/tree/master/Question-1/question-1.md) &
[Question-2](https://github.com/clem9669/DockerOrNot/tree/master/Question-2/question-2.md) &
[Question-3](https://github.com/clem9669/DockerOrNot/tree/master/Question-3/question-3.md) &
[Question-4](https://github.com/clem9669/DockerOrNot/tree/master/Question-4/question-4.md)

# Corrigé de l'[exercice 2](https://github.com/clem9669/DockerOrNot/blob/master/Question-2/Question-2.md)
---
1 - Creation du repertoire dans le home

```
$ cd /home/uvsq
$ mkdir Docker

```

2- Création du fichier dockerfile dans le repertoire Docker

```
$ touch Dockerfile

```

3- Edition du fichier Dockerfile pour créer notre image apache

```
$ vi Dockerfile

```
Contenu du fichier

```
FROM debian:latest
MAINTAINER uvsq <uvsqmail@uvsqdomaine.fr>

ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/web/log/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2

RUN export DEBIAN_FRONTEND=noninteractive && apt-get update && apt-get -y -q upgrade && apt-get -y -q install apache2

EXPOSE 80

CMD ["apache2ctl","-D","FOREGROUND"]

```

4- Création de l'image

La syntaxe de la commande est la suivante:
```
docker build -t [imagename]:[tag] [repertoire du dossier de Dockerfile]
```
Dans notre cas, vu qu'on est deja dans le dossier , la commande est la suivante :
```
$ docker build -t='uvsq/apache' .

```

Execution de la commande

```
Sending build context to Docker daemon 6.656 kB Step 1 : FROM debian:latest
---> 73e72bf822ca
Step 2 : MAINTAINER uvsq
---> Running in 81418305032e
---> 7c45588e5b07
[...]
Step 10 : CMD apache2ctl -D FOREGROUND
---> Running in d435f9e2db87
---> 1f9e7590d11b
debconf: delaying package configuration, since apt-utils is not installed
…………
Successfully built cd5f19931229


```

5- Verfication de la création de l'image

```
$ docker images

```
Resultat de la commande

```
REPOSITORY	TAG	IMAGE ID	CREATED SIZE
uvsq/apache	latest	cd5f19931229	29 seconds ago	191.5 MB
ubuntu	latest	4ca3a192ff2a	3 days ago
128.2 MB

```

6- Test de l'image

```
$ docker run -d -p 80 --name=apache uvsq/apache

```
7- Port utiliser par apache

```
$ docker port apache 80
```

Resultat de la commande
```
0.0.0.0:32769
```
Le port utiliser n'est pas le port 80 mais ce dernier a été mappé sur un autre port choisi par Docker.

Faisons un test en ouvrant votre navigateur et en se connectant sur notre machine locale (Hôte) et sur le port docker:

8- Choisir le port 80

```
$ docker rm apache
$ docker run -d -p 80:80 --name=apache uvsq/apache
```

9- Création d'une image tar qui pourra être partagé

```
$ docker save -o apache.tar uvsq/apache
$ ls
Dockerfile  apache.tar

```
