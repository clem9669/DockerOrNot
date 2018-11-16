##Installation:

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

##Première image:

```
sudo docker hello-world
```

####Docker ne peut pas trouver cette image en local donc il va la chercher en ligne.
####Et la 'pull'.

##Résultat:

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
