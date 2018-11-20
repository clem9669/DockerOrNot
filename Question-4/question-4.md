## Exploitation d'une infrastructure réseau dans Docker ##

L'objectif de cet exercice est de léver le voile sur les communications intra-conteneurs dans Docker sans toutefois rentrer trop dans les subtilités de la gestion réseau de Docker.
À l'issue de ce TP, vous aurez compris les bases du réseau Docker, ainsi que les différentes techniques pour tirer tous les bénéfices d'une infrastructure "dockerisée".

Il faut savoir que Docker repose sur la librairie ```libnetwork``` pour la gestion des communications et de base, il fournit quatres type de réseau (driver libnetwork) :


* **bridge** : crée un réseau interne pour vos conteneurs ;
* **host** : ce type de réseau permet au conteneur d'avoir la même interface que l'hôte ;
* **none** : comme le nom l'indique, aucun réseau pour les conteneurs ;
* **overlay** : réseau interne entre plusieurs hôtes. (Consacré à Docker Swarm que nous ne verrons pas dans ce tp)


Docker utilise le réseau _bridge_ par defaut. À titre d'information, il existe des plugins pour étendre ces possibilités de gestion de réseau.


1. a) Vérifiez la liste des réseaux disponibles pour vos conteneurs au niveau de votre démon Docker sur votre hôte. Que remarquez-vous ?

   b) Inspectez le sous-réseau par defaut de Docker et donner son addresse réseau.

   c) Où se trouve l'interface de passerelle par défaut du réseau de la question précédente.
2. La communication des conteneurs entre eux et avec le monde extérieure est contrôlée par le fait que les paquets IP soient transmises (_forwarded_)  par la machine hôte et qu'elles soient pas bloquées par un pare-feu. Tous ces mécanismes sont configurables au niveau du noyau du système hôte de Docker.

   a) Recupérez l'image officielle du serveur HTTP d'Apache (__httpd__) depuis le registre public de Docker. Lancez un serveur httpd dans un conteneur nommé __mon-serveur__ de sorte qu'on arrive à l'interroger depuis un navigateur de la machine hôte avec une addresse autre que l'adresse réseau fourni par le bridge.

   b) Recupérez l'image officielle d'ubuntu depuis le registre public de Docker. Assurez-vous que notre serveur de la question précédente tourne toujours. Exécutez la commande suivante :
      ```youtrack
         docker run --rm -it --name container2 --net container:mon-serveur ubuntu
      ```
      Installez le paquet **net-tools** et lancez la commande ```netstat -al``` .

      Que remarquiez-vous? Qu'est -ce que vous pouvez dire entre les deux containeurs container2 et mon-serveur?

   c) Arrêtez et supprimez tous les containers lancés précédemment. Lancez un conteneur nommé _mon-serveur_ en tache de fond contenant le serveur httpd.

     + Trouvez l'adresse ip du conteneur _mon-serveur_
     + Lancez un container nommé _container1_ basé sur ubuntu et verifiez que depuis ce dernier, on peut interroger le serveur httpd en utilisant un navigateur leger comme **curl**.
     + Lancez un autre container nommé _container2_ basé sur ubuntu en ajoutant l'option **- link mon-serveur:serveur** et verifiez que depuis ce conteneur, on peut interroger le serveur httpd en utilisant un navigateur leger comme **curl** sans connaître l'adresse ip du serveur.

3. Dans cette partie, nous allons voir comment créer un nouvel réseau de type bridge dans Docker.
    
   a) Familiarisez-vous avec la commande ```docker network``` et ses options en lisant le man ```man docker network``` ou l'aide ```docker network -h```,```docker network create --help```, ext...
   
   b) Créez un nouveau réseau nommé __chat__ avec addresse _192.168.1.0/24_ sous les spécifications suivantes :
        
      - fournisez un passerelle par defaut pour ce réseau
      - nommez __chat0__ le nouveu pont virtuel Ethernet qui sert de passerelle
      - attribuez une plage d'addresse de 14 clients pour ce réseau 
        
        Vérifiez que votre réseau est bien crée avec les configurations valides.
        
   c) C'est bien beau d'avoir créer un réseau bridge dans Docker mais il faut maintenant l'utuliser dans des conteneurs. Pour ce faire, nous allons simmuler un mini-système client-serveur de messagerie (**un chat**).
      
      + Récuperéz le dossier [chat](./chat) dans votre hôte.
      + Créez une image Docker nommé __mypython2__ basé sur l'image ubuntu dont on installera python2.7 à l'intérieure et dont on ajoutera le dossier [chat](./chat) dans le répertoire _/home/_.(Piste: créez un Dockerfile ou faire un commit sur une image avec python installé à l'intérieure)
      + Lancez un conteneur nommé __chat_serveur__ basé sur l'image _mypython2_ en le connectant dans le réseau **chat** et récupérez son addresse ip. Executez le fichier serveur.py qui se trouve dans _/home/_.
      + Lancez trois conteneurs basés sur l'image _mypython2_ qui joueront le rôle des clients au près du serveur __chat_serveur__. Executez le fichier client.py pour chaque client.
      + Vérifiez que les clients peuvent se chater entre eux.
   








  [Question-3](https://github.com/clem9669/DockerOrNot/blob/master/Question-3/Question-3.md)
