## Exploitation d'une infrastructure réseau dans Docker ##

L'objectif de cet exercice est de léver le voile sur les communications intra-conteneurs dans Docker sans toutefois rentrer trop dans les subtilités de la gestion réseau de Docker.
À l'issue de ce TP, vous aurez compris les bases du réseau Docker, ainsi que les différentes techniques pour tirer tous les bénéfices d'une infrastructure "dockerisée".

Il faut savoir que Docker repose sur la librairie ```libnetwork``` pour la gestion des communications et de base, il fournit quatres type de réseau :


* **bridge** : crée un réseau interne pour vos conteneurs ;
* **host** : ce type de réseau permet au conteneur d'avoir la même interface que l'hôte ;
* **none** : comme le nom l'indique, aucun réseau pour les conteneurs ;
* **overlay** : réseau interne entre plusieurs hôtes. (Consacré à Docker Swarm que nous ne verrons pas dans ce tp)


Docker utilise le réseau _bridge_ par defaut. À titre d'information, il existe des plugins pour étendre ces possibilités de gestion de réseau. 
 