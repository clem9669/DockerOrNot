# Conteneurs Dockers et persistence de données 
---
Docker propose trois méthodes pour stocker les données utilisées par ou générées par les conteneurs : volumes, bind mount, tmpfs.
* **Les volumes** : font partie du système de fichiers hôte, mais gérés par menu fixe sur le chemin spécifique et ne doivent pas être modifiés par d'autres applications.
* **Les bind mounts** : peuvent être situés n'importe où sur l'hôte, mais peuvent être modifiés par d'autres applications
* **Les tmpfs** : se situent dans la mémoire de l'hôte et ne sont jamais écrits dans le système de fichiers de l’hôte.

L'objectif de ce TP est de matnipuler les [volumes](https://docs.docker.com/storage/volumes/) et les [binds mounts](https://docs.docker.com/storage/volumes/)

![](https://docs.docker.com/storage/images/types-of-mounts-volume.png)



[Question-4](https://github.com/clem9669/DockerOrNot/blob/master/Question-4/question-4.md)
