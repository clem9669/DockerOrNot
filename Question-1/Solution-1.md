## Installation:

```
sudo apt update
sudo apt search docker
sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common
```

Add Docker’s official GPG key:

```
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

```
Verify that you now have the key with the fingerprint

```
9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88

sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
 ```

**puis**


 ```
sudo apt install docker-ce docker docker.io -y
 ```

 Vérifier que vous avez correctement installer docker en saisissant:

 ```
docker version

```

## Première image:

```
sudo docker run hello-world
```

#### Docker ne peut pas trouver cette image en local donc il va la chercher en ligne.
#### Et la 'pull' d'[ici](https://hub.docker.com/_/hello-world/)

> hub.docker.com est le lieux d'échange de toutes les images Docker.

## Résultat:

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
d1725b59e92d: Pull complete
Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## Aide docker:

Comme pour toutes les commandes, vous pouvez saisir :

Vous y trouverez également quelques exemples.

```
man docker
man docker-run

docker --help
```

## Afficher ses images Docker:

Pour afficher ses images docker, d'après la documentation, il faut executer:

```
docker images
```
Vous pourrez en fonction de vos besoin ajouter des options à cette commande.


> Résultat:
```
docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              4ab4c602aa5e        2 months ago        1.84kB

```
Notons que nous retrouvons nombres d'informations utile grâce à cette commande comme le tag, son id, sa date de création et sa taille.


## Historique d'une image Docker:

Les images Docker sont identifié par plusieurs critères comme vu dans la commande 'docker image' par un **image ID** ou **repository-name**.

```
docker history <image id/repository name>
```

> Résultat

```
docker history 4ab4c602aa5e
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
4ab4c602aa5e        2 months ago        /bin/sh -c #(nop)  CMD ["/hello"]               0B                  
<missing>           2 months ago        /bin/sh -c #(nop) COPY file:9824c33ef192ac94…   1.84kB     
```


## Informations Bas niveau:

Ce référé à **docker -h**

```
docker inspect <image id/repository name>
```

Vous aurez cependant remarquer d'autres intéréssante informations comme:

```
bridge               gracious_kowalevski  hello-world:latest   host                 none
```


## Liste des conteneurs:

```
docker ps -h

Usage:	docker ps [OPTIONS]

List containers

Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display numeric IDs
  -s, --size            Display total file sizes
```

Vous devriez avoir :

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                         PORTS               NAMES                 SIZE
be07ec5a6454        hello-world         "/hello"            About an hour ago   Exited (0) About an hour ago                       gracious_kowalevski   0B (virtual 1.84kB)

```

###Informations générales:

```
docker info
Containers: 1
 Running: 0
 Paused: 0
 Stopped: 1
Images: 1
Server Version: 18.06.1-ce
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: b41633746ed4833f52c3c071e8edcfa2713e5677 (expected: 468a545b9edcd5932818eb9de8e72413e616e86e)
runc version: N/A (expected: 69663f0bd4b60df09991c08812a60108003fa340)
init version: v0.18.0 (expected: fec3683b971d9c3ef73f284f176672c44b448662)
Security Options:
 apparmor
 seccomp
  Profile: default
Kernel Version: 4.18.0-kali2-amd64
Operating System: Kali GNU/Linux Rolling
OSType: linux
Architecture: x86_64
CPUs: 2
Total Memory: 1.903GiB
Name: kali
ID: QI7C:F4ME:HOYO:DHJF:EXJX:5DWL:2NDA:F75R:CLGT:6YEQ:QGR2:6R2R
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
```

### Stopper et supprimer un conteneur/image:

```
docker -h

[...]
  images      List images
  kill        Kill one or more running containers
  pause       Pause all processes within one or more containers
  ps          List containers
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  start       Start one or more stopped containers
  stop        Stop one or more running containers

Run 'docker COMMAND --help' for more information on a command.

```

Supprimons la seul image que nous ayons pour le moment: 'hello-world'

```
docker rmi -h
Flag shorthand -h has been deprecated, please use --help

Usage:	docker rmi [OPTIONS] IMAGE [IMAGE...]

Remove one or more images

Options:
  -f, --force      Force removal of the image
      --no-prune   Do not delete untagged parents


docker rmi hello-world:latest --force
Untagged: hello-world:latest
Untagged: hello-world@sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Deleted: sha256:4ab4c602aa5eed5528a6620ff18a1dc4faef0e1ab3a5eddeddb410714478c67f

```
Vérifions:

```
docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

Conteneurs et Images sont stockés sur votre machine. Cependant, en fonction de votre configuration, ils peuvent être à différents endroits. Cas commun: /var/lib/docker

[https://stackoverflow.com/questions/19234831/where-are-docker-images-stored-on-the-host-machine](https://stackoverflow.com/questions/19234831/where-are-docker-images-stored-on-the-host-machine)


✅
