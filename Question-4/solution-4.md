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
3. a) 
   ```youtrack
      debian@debian:~$ docker network -h
      Flag shorthand -h has been deprecated, please use --help
      
      Usage:  docker network COMMAND
      
      Manage networks
      
      Commands:
        connect     Connect a container to a network
        create      Create a network
        disconnect  Disconnect a container from a network
        inspect     Display detailed information on one or more networks
        ls          List networks
        prune       Remove all unused networks
        rm          Remove one or more networks
      
      Run 'docker network COMMAND --help' for more information on a command.
      debian@debian:~$ 
   ```
   ```youtrack
      debian@debian:~$ docker network create -h
      Flag shorthand -h has been deprecated, please use --help
      
      Usage:  docker network create [OPTIONS] NETWORK
      
      Create a network
      
      Options:
            --attachable           Enable manual container attachment
            --aux-address map      Auxiliary IPv4 or IPv6 addresses used by Network driver (default map[])
            --config-from string   The network from which copying the configuration
            --config-only          Create a configuration only network
        -d, --driver string        Driver to manage the Network (default "bridge")
            --gateway strings      IPv4 or IPv6 Gateway for the master subnet
            --ingress              Create swarm routing-mesh network
            --internal             Restrict external access to the network
            --ip-range strings     Allocate container ip from a sub-range
            --ipam-driver string   IP Address Management Driver (default "default")
            --ipam-opt map         Set IPAM driver specific options (default map[])
            --ipv6                 Enable IPv6 networking
            --label list           Set metadata on a network
        -o, --opt map              Set driver specific options (default map[])
            --scope string         Control the network's scope
            --subnet strings       Subnet in CIDR format that represents a network segment
      debian@debian:~$ 
   ```
   b) Prenons l'addresse __192.168.1.254__ comme addresse de passerelle. Pour avoir 14 clients sans gaspillé l'addresse de notre réseau, il faut prendre le masque sous-réseau __192.168.1.0/28__.
      On a donc la configuration suivante :
   ```youtrack
      debian@debian:~$ docker network create --subnet 192.168.1.0/24 --gateway 192.168.1.254 --ip-range 192.168.1.0/28 -o "com.docker.network.bridge.name=chat0" chat
      b3e235ea3cc1e3d6168d6cc01bb32e9a69f6a334a9a9411ef2d29b21e935f254
      debian@debian:~$ docker network inspect chat 
      [
          {
              "Name": "chat",
              "Id": "b3e235ea3cc1e3d6168d6cc01bb32e9a69f6a334a9a9411ef2d29b21e935f254",
              "Created": "2018-11-20T19:06:53.634083266+01:00",
              "Scope": "local",
              "Driver": "bridge",
              "EnableIPv6": false,
              "IPAM": {
                  "Driver": "default",
                  "Options": {},
                  "Config": [
                      {
                          "Subnet": "192.168.1.0/24",
                          "IPRange": "192.168.1.0/28",
                          "Gateway": "192.168.1.254"
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
                  "com.docker.network.bridge.name": "chat0"
              },
              "Labels": {}
          }
      ]
      debian@debian:~$  
   ```
   Au niveau de la machine hôte, on a bien notre pont virtuel __chat0__ :
   ```youtrack
      root@debian:/home/debian# ifconfig
      chat0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
              inet 192.168.1.254  netmask 255.255.255.0  broadcast 192.168.1.255
              ether 02:42:e6:e0:8d:e1  txqueuelen 0  (Ethernet)
              RX packets 0  bytes 0 (0.0 B)
              RX errors 0  dropped 0  overruns 0  frame 0
              TX packets 0  bytes 0 (0.0 B)
              TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
      
      docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
              inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
              inet6 fe80::42:f3ff:feae:73c8  prefixlen 64  scopeid 0x20<link>
              ether 02:42:f3:ae:73:c8  txqueuelen 0  (Ethernet)
              RX packets 0  bytes 0 (0.0 B)
              RX errors 0  dropped 0  overruns 0  frame 0
              TX packets 25  bytes 3434 (3.3 KiB)
              TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
      
      enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
              inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
              inet6 fe80::a00:27ff:fe9f:ac76  prefixlen 64  scopeid 0x20<link>
              ether 08:00:27:9f:ac:76  txqueuelen 1000  (Ethernet)
              RX packets 2919  bytes 1050725 (1.0 MiB)
              RX errors 0  dropped 0  overruns 0  frame 0
              TX packets 1827  bytes 179310 (175.1 KiB)
              TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
      
      lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
              inet 127.0.0.1  netmask 255.0.0.0
              inet6 ::1  prefixlen 128  scopeid 0x10<host>
              loop  txqueuelen 1  (Boucle locale)
              RX packets 10  bytes 558 (558.0 B)
              RX errors 0  dropped 0  overruns 0  frame 0
              TX packets 10  bytes 558 (558.0 B)
              TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
      
      root@debian:/home/debian# 
   ```
   c) Création de l'image __mypython2__:
   + Contenu du Dockerfile placé dans le dossier _/home/Dockerfile_ de l'hôte ainsi que le dossier [chat](./chat):
        ```dockerfile
           FROM ubuntu
           ADD chat/ /home/
           RUN apt-get update && apt-get install -y python
           LABEL description="mypython2"
        ```
        Création de l'image avec la commande  ```docker build -t mypython2 /home/ ```
   + Lancement du conteneur __chat_serveur__:
       ```youtrack
          debian@debian:~/Téléchargements$ docker run -it --rm --name chat_serveur --network chat mypython2
          root@7f47a29dcf76:/# cd /home/
          root@7f47a29dcf76:/home# python server.py 
                                          SERVER WORKING 
          
        ```
        Son addresse ip est :
        ```youtrack
           debian@debian:/home$ docker inspect chat_serveur --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $INSTANCE_ID
           192.168.1.1
           debian@debian:/home$ 
        ```
    + Lancement des clients :
        ```youtrack
           debian@debian:~$ docker run --rm -it --network chat mypython2
           root@f236377f02ee:/# cd /home/
           root@f236377f02ee:/home# python client.py 192.168.1.1
            CREATING NEW ID:
            Enter username: Alice
            Welcome to chat room. Enter 'exit' anytime to exit
            You: Je suis Alice
            Bob: Je suis bob
            You: exit
           DISCONNECTED!!
            
           root@f236377f02ee:/home# 
        ```
        Vérification au niveau serveur :
        ```youtrack
           root@7f47a29dcf76:/home# python server.py 
                                           SERVER WORKING 
           Client (192.168.1.2, 35764) connected  [ Bob ]
           Client (192.168.1.3, 54236) connected  [ Alice ]
           Client (192.168.1.3, 54236) is offline  [ Alice ]
        ```

