# Creation d'image dans Docker

L'objectif de cette question est de savoir créer et gerer ses propres images

Le Dockerfile (toujours avec une majuscule) est un fichier qui contient toutes les informations pour créer une image, comme des méta-données (Mainteneur, label etc ...), ou même les commandes à exécuter pour installer un logiciel.

Pour pouvoir construire une image, il faut connaître un minimum le logiciel que l'on souhaite conteneuriser, par exemple ici nous allons conteneuriser apache qui va necessite variables d'environnement pour fonctionner.

1- Créer un repertoire dans votre home denommé Docker

2- Se déplacer dans ce repertoire et creer un fichier nommé "Dockerfile"

Voici la liste des instructions d'un Dockerfile :
 ```
FROM # Pour choisir l'image sur laquelle on se base, toujours en premier
MAINTAINER # Le nom du créateur/mainteneur sous forme "prénom nom [ou pseudo] <email>"
RUN # Permet d'exécuter une commande
CMD # Commande exécutée au démarrage du conteneur par défaut
EXPOSE # Ouvre un port
ENV # Permet d'éditer des variables d'environnement
ARG # Un peu comme ENV, mais seulement le temps de la construction de l'image
COPY # Permet de copier un fichier ou répertoire de l'hôte vers l'image
ADD # Permet de copier un fichier de l'hôte ou depuis une URL vers l'image, permet également de décompresser une archive tar
LABEL # Des méta-données utiles pour certain logiciel de gestion de conteneur, comme rancher ou swarm, ou tout simplement pour mettre des informations sur l'image.
ENTRYPOINT # Commande exécutée au démarrage du conteneur, non modifiable, utilisée pour package une commande
VOLUME # Crée une partition spécifique

 ```
3- Editer le fichier "Dockerfile"
Instructions:
- Choisir "debian:latest" comme image sur laquelle on se base
- Mettre votre nom et email comme créateur
- Indiquer le dossier home d'apache "
- Ajouter les variables d'environnements suivants:

 ```
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/web/log/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2

 ```
 - Installer apache
  ```
 RUN export DEBIAN_FRONTEND=noninteractive && apt-get update && apt-get -y -q upgrade && apt-get -y -q install apache2

  ```
  - Exposer les ports 80 et 443 pour apache

  - Ajouter la commande a executer par défaut au démarrage du conteneur
   ```
  CMD ["apache2ctl","-D","FOREGROUND"]
   ```
4- Construire notre image

5- Verifier que notre image apparait dans la liste des images

6- Tester l'image créé

7- Trouver le port choisi par Docker.
Que remarque-t-on?
Tester le lancement d'apache au travers cette port

8- Modifier le port choisi par Docker pour qu'elle soit 80


9- Creer une archive tar de notre image qui pourra être partager.


[Question-1](https://github.com/clem9669/DockerOrNot/blob/master/Question-1/Question-1.md) & [Question-3](https://github.com/clem9669/DockerOrNot/blob/master/Question-3/Question-3.md) &
[Question-4](https://github.com/clem9669/DockerOrNot/blob/master/Question-2/question-4.md)
