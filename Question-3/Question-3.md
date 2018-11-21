# Exercice 3 : Conteneurs Dockers et persistence de données
---
Docker propose trois méthodes pour stocker les données utilisées par ou générées par les conteneurs : volumes, bind mount, tmpfs.
* **Les volumes** : font partie du système de fichiers hôte, mais gérés par menu fixe sur le chemin spécifique et ne doivent pas être modifiés par d'autres applications.
* **Les bind mounts** : peuvent être situés n'importe où sur l'hôte, mais peuvent être modifiés par d'autres applications
* **Les tmpfs** : se situent dans la mémoire de l'hôte et ne sont jamais écrits dans le système de fichiers de l’hôte.

L'objectif de ce TP est de matnipuler les [volumes](https://docs.docker.com/storage/volumes/)


1)- Créer un volume Docker.

2)- Vérifier que le volume est bien créé.

3)- Afficher les détails sur le volume.

4)- Récupérer une image Jenkins sur le Docker Hub (https://hub.docker.com).

5)- Lire l’aide relative à l’image Jenkins et fournie sur le docker hub et trouver la commande pour lancer un conteneur à l’aide de l’image jenkins.

6)- Lancer un conteneur “MonJenkins1” basé sur l’image Jenkins précédemment récupérée, tout en assurant le montage de répertoire /var/jenkins_home dans le volume “monvolume1”. Copier le mot de passe généré et affiché dans le terminal ,et poursuivre l'installation (minimale sans plugin additionnels) de Jenkins en accédant à http://localhost:port depuis la machine hôte.

7)- Créer une tâche Jenkins dans le tableau de bord (tâche freestyle, mettre ls pour la commande de build).

8)- Lancer un conteneur “MonJenkins2” basé sur l’image Jenkins précédemment récupérée, tout en assurant le montage de répertoire /var/jenkins_home dans le volume “monvolume1”. (Note : à utiliser des ports différents de ceux utilisés par le conteneur “MonJenkins1”). Remarquez que ce Jenkins utilise les mme données que le précédent : l'utilisateur Admin est déjà créé et la tâche créé au niveau de MonJenkins1 est présente sur l'interface de MonJenkins2.

9)- Arrêter puis supprimer les conteneurs “MonJenkins1” et “MonJenkins2”.

10)- Vérifier (à l’aide de la commande appropriée) que le volume précédemment créé est toujours disponible.




[Question-1](https://github.com/clem9669/DockerOrNot/blob/master/Question-1/Question-1.md) &
[Question-2](https://github.com/clem9669/DockerOrNot/blob/master/Question-2/Question-2.md) & [Question-4](https://github.com/clem9669/DockerOrNot/blob/master/Question-4/question-4.md)
