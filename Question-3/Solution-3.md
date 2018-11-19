# SOLUTION de l'exercice 2 : Conteneurs Dockers et persistence de données
---

**1- Les volumes**

a)- Créer un volume Docker
```
docker volume create monvolume1
```

b)- Vérifier que le volume est bien créé
```
docker volume ls
```

c)- Afficher les détails sur le volume
```
docker volume inspect monvolume1
```

d)- Récupérer une image Jenkins sur le Docker Hub (https://hub.docker.com)
```
docker pull jenkins
```

e)- Lire l’aide relative à l’image Jenkins et fournie sur le docker hub et trouver la commande pour lancer un conteneur à l’aide de l’image jenkins.
```
docker run --name MonJenkins1 -v monvolume1:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins
```

f)- Lancer un conteneur “MonJenkins1” basé sur l’image Jenkins précédemment récupérée, tout en assurant le montage de répertoire /var/jenkins_home dans le volume “monvolume1”. Copier le mot de passe généré et affiché dans le terminal ,et poursuivre l'installation (minimale sans plugin additionnels) de Jenkins en accédant à http://localhost:port depuis la machine hôte.
```
docker run --name MonJenkins1 -v monvolume1:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins
```

g)- Créer une tâche Jenkins dans le tableau de bord (tâche freestyle, mettre ls pour la commande de build)
Accéder à http://localhost:8080

h)- Conteneur “MonJenkins2”
```
docker run --name MonJenkins1 -v monvolume1:/var/jenkins_home -p 8181:8080 -p 55000:50000 jenkins
```
Accéder à http://localhost:8181
Remarquez que ce Jenkins utilise les mme données que le précédent : l'utilisateur Admin est déjà créé et la tâche créé au niveau de MonJenkins1 est présente sur l'interface de MonJenkins2.

i)- Arrêter puis supprimer les conteneurs MonJenkins 1 et MonJenkins2
```
docker stop MonJenkins1
docker stop MonJenkins2
```
Avant de continuer, Vérifier que les conteneur sont bien arrêtés (```docker ps```)
```
docker rm MonJenkins1
docker rm MonJenkins2
```

j)- Vérifier (à l’aide de la commande appropriée) que le volume précédemment créé est toujours disponible
```
docker volume ls
```


**2- Les bind mounts**

