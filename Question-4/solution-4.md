## Exploitation d'une infrastructure réseau dans Docker ##

1. Utilisation de la commande ``` docker network ls ```:
    ```
    debian@debian:~$ docker network ls 
    NETWORK ID          NAME                DRIVER              SCOPE
    74ca54c94e20        bridge              bridge              local
    d445f212f1f3        host                host                local
    af0536b47bba        none                null                local
    
    ```
    
    On remarque que Docker est installé par défaut avec trois sous-réseaux : _bridge_,_host_ et _none_.
    
    On sait que Docker utilise le sous-réseau _bridge_ par défaut. On l'inspècte avec la commande ```docker network inspect bridge``` :
    ```$xslt
       debian@debian:~$ docker network inspect bridge 
       [
           {
               "Name": "bridge",
               "Id": "74ca54c94e20e62b997f3df15d09d5715fbb92aa20b2027252ae48daf5fb2d1a",
               "Created": "2018-11-17T10:44:15.911018271+01:00",
               "Scope": "local",
               "Driver": "bridge",
               "EnableIPv6": false,
               "IPAM": {
                   "Driver": "default",
                   "Options": null,
                   "Config": [
                       {
                           "Subnet": "172.17.0.0/16",
                           "Gateway": "172.17.0.1"
                       }
                   ]
               },
               "Internal": false,
               "Attachable": false,
               "Ingress": false,
               "ConfigFrom": {
                   "Network": ""
               },
               "ConfigOnly": false,
               "Containers": {},
               "Options": {
                   "com.docker.network.bridge.default_bridge": "true",
                   "com.docker.network.bridge.enable_icc": "true",
                   "com.docker.network.bridge.enable_ip_masquerade": "true",
                   "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
                   "com.docker.network.bridge.name": "docker0",
                   "com.docker.network.driver.mtu": "1500"
               },
               "Labels": {}
           }
       ]
    ```
    
    Nous voyons que ce réseau est de type **bridge** et qu'il dispose des adresses IP 172.17.0.0/16 avec comme passerelle par défaut 172.17.0.1. Cette passerelle est un pont virtuel Ethernet, nommé _docker0_ sur notre machine hôte.
    ```youtrack
       root@debian:/home/debian# ifconfig docker0
       docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
               inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
               ether 02:42:cc:fa:e2:bc  txqueuelen 0  (Ethernet)
               RX packets 0  bytes 0 (0.0 B)
               RX errors 0  dropped 0  overruns 0  frame 0
               TX packets 0  bytes 0 (0.0 B)
               TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    ```

2. a) La commande ```docker pull httpd``` récupère l'image du serveur HTTP d'Apache. Pour lancer notre serveur dans un conteneur accessible depuis l'extérieure, il faut exposer le port d'écoute du serveur vers un port de la machine hôte.
    ```
        docker run -dit --name mon-serveur -p 8080:80 httpd
    ``` 
    [http://localhost:8080](http://localhost:8080)
    
   b) 
   ```
   debian@debian:~$ docker run --rm -it --name container2 --net container:mon-serveur ubuntu
   root@6f4a4065ea1b:/# apt update & apt install net-tools
   root@6f4a4065ea1b:/# netstat -al
   Active Internet connections (servers and established)
   Proto Recv-Q Send-Q Local Address           Foreign Address         State      
   tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
   Active UNIX domain sockets (servers and established)
   Proto RefCnt Flags       Type       State         I-Node   Path
   root@6f4a4065ea1b:/# 
   ```
   On remarque une activité réseau dans le conteneur2 : le port 80 est en écoute;  or qu'on a rien exécuté dans ce conteneur. On constate donc que le conteneur _mon-serveur_ et le conteneur _conteneur2_ partage la même pile réseau grace à l'option ``` -net container:mon-serveur``` lors de la création du container2.
   
   c) 
   ```youtrack
    debian@debian:~$ docker run --rm -d --name mon-serveur httpd
    bc45985fe12f91e2a7fd88d1eb9bd66871a797c8353e9c97e77a2aad8a46780e
    debian@debian:~$ docker inspect mon-serveur --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $INSTANCE_ID
    172.17.0.2
    debian@debian:~$ docker run --rm -it --name container1 ubuntu 
    root@72aa6665eb16:/# apt update & apt install curl
    root@72aa6665eb16:/# curl 172.17.0.2
    <html><body><h1>It works!</h1></body></html>
    root@72aa6665eb16:/# exit
    debian@debian:~$ docker run --rm -it --link mon-serveur:server ubuntu 
    root@16fe84b74e0e:/# apt update & apt install -y curl
    root@16fe84b74e0e:/# curl server
    <html><body><h1>It works!</h1></body></html>
    root@16fe84b74e0e:/# 
   ```
   L'option **- link mon-serveur:serveur**  crée un lien du _container1_ vers le container _mon-serveur_, plus précisement, Docker récupère dynamiquement l'adresse IP du conteneur _mon-serveur_ dans son interface docker0, puis injecte le lien entre le nom fourni en option qui sert d'alias dans le fichier hosts du conteneur _container1_.
   ```youtrack
      root@16fe84b74e0e:/# cat /etc/hosts
      127.0.0.1       localhost
      ::1     localhost ip6-localhost ip6-loopback
      fe00::0 ip6-localnet
      ff00::0 ip6-mcastprefix
      ff02::1 ip6-allnodes
      ff02::2 ip6-allrouters
      172.17.0.2      server bc45985fe12f mon-serveur
      172.17.0.3      16fe84b74e0e
      root@16fe84b74e0e:/#
   ```
3. 

4.

5.
