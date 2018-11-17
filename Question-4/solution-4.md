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
