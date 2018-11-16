# Rappel de définitions: 📚

**Image conteneur** (container image) : package de toutes les dépendances et informations nécessaires pour créer un conteneur. Une image inclut toutes les dépendances (notamment les frameworks) ainsi que la configuration de déploiement et d’exécution à utiliser par le runtime du conteneur. En règle générale, une image est dérivée de plusieurs images de base qui sont empilées en couches pour former le système de fichiers du conteneur. Une image est immuable une fois qu’elle a été créée.


**Dockerfile** : fichier texte contenant des instructions pour la création d’une image Docker. C’est comme un script de commandes, la première ligne indique l’image de base avec laquelle commencer, puis suivez les instructions pour installer les programmes requis, copier les fichiers et ainsi de suite, jusqu’à ce que vous obteniez l’environnement de travail dont vous avez besoin.


**Build** : action de créer une image conteneur sur la base des informations et du contexte fournis par le fichier Dockerfile associé, plus des fichiers supplémentaires dans le dossier où l’image est créée. Vous pouvez créer des images à l’aide de la commande docker build de Docker.


**Conteneur** (container) : instance d’une image Docker. Un conteneur représente l’exécution d’une application, d’un processus ou d’un service. Il renferme une image Docker, un environnement d’exécution et un ensemble standard d’instructions. Pour mettre un service à l’échelle, vous créez plusieurs instances d’un conteneur à partir de la même image. Cela peut également être fait par un traitement par lots, qui passe des paramètres différents à chaque instance.


**Volumes** : offre un système de fichiers accessible en écriture que le conteneur peut utiliser. Dans la mesure où les images sont en lecture seule, mais que la plupart des programmes ont besoin d’écrire dans le système de fichiers, les volumes ajoutent une couche accessible en écriture, par-dessus l’image de conteneur, afin que les programmes aient accès à un système de fichiers accessible en écriture. Le programme ne sait pas qu’il accède à un système de fichiers en couches, il s’agit simplement du système de fichiers normal. Les volumes résident dans le système hôte et sont gérés par Docker.


**Tag**: marque ou étiquette que vous pouvez appliquer aux images pour identifier les différentes images ou versions de l’image initiale (selon le numéro de version de l’environnement cible).
Build en plusieurs étapes : fonctionnalité, depuis Docker 17.05 ou une version ultérieure, qui permet de réduire la taille des images finales. En quelques phrases, avec un build en plusieurs étapes vous pouvez utiliser, par exemple, une grande image de base, qui contient le SDK pour compiler et publier l’application, puis utilisez le dossier de publication avec une petite image de base runtime uniquement, pour produire une image finale beaucoup plus petite


**Repository** : collection d’images Docker associées, identifiées par une balise qui indique la version de chaque image. Certains dépôts contiennent plusieurs variantes d’une image spécifique, par exemple une image contenant des SDK (plus lourde), une image contenant uniquement des runtimes (plus légère), etc. Ces variantes peuvent être identifiées par des balises. Un dépôt peut contenir des variantes de plateforme, comme une image Linux et une image Windows.
