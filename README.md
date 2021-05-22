# docker-pratices

* Quelle est la difference entre "image" et "conteneur" ?
  - Une image est constituée des fichiers binaires, des librairies et du code source qui compose l'application.
  - Un container correspond à l'instance, en cours d'exécution, de l'image.

* Télécharger un Tools sur DockerHub
  - $ docker container run --publish 80:80 nginx <br>
  ** Démarrer un nouveau conteneur utilisant l'image nginx téléchargée sur DockerHub, et la partie "publish 80:80" expose le port local 80 de la machine hôte, et redige tout le trafic à l'application exécutée dans le conteneur sur son port 80.

* Lister les conteneurs en exécutions
  - $ docker container ls
  - $ docker ps
* Lister tous les conteneurs
  - $ docker container ls -a 
  - $ docker ps -l

* Stopper le container en cours d'exécution
  - $ docker container stop [container_id]
  - $ docker container stop [container_name]

* Supprimer le container en cours d'exécution
  - $ docker container rm [container_id]
  - $ docker container rm [container_name] <br>
  ** Si celui-ci est en cours d'exécution, alors
  - $ docker container rm -f [container_name/container_id] <br>
    *** Permet de supprimer définitivement un container même si celui-ci n'a pas été stoppé au préalable.<br>
  ** vous pouvez supprimer plusieurs conteneurs également
  - $ docker container rm [container_name1] [container_name2] [container_name3]...

* Lancer un container tout en restant detaché de la console
  - $ docker container run --publish 80:80 --detach nginx <br>
  ** L'option "--detach" permet d'exécuter le demarrage du container en arrière-plan, vous donnant la main pour effectuer d'autres actions.

* Donner un nom à son container
  - $ docker container run --publish 80:80 -- detach --name [container_name] [container_tool]
  - Exemple : $ docker container run --publish 80:80 -- detach --name nginx_webserver nginx <br>
  ** L'option "--name" permet de spécifier un nom et de l'associer à ce conteneur, ce qui est beaucoup plus simple par la suite pour pouvoir intéragir avec lui et lui envoyer des commandes (plutôt que d'utiliser son id)
  
* Afficher les logs en cours
  - $ docker container logs [container_name/container_id] <br>
  ** L'option "logs" permet d'afficher les logs du conteneur indiqué soit par son ID, soit par son nom.
  
* Afficher les processus en cours
  - $ docker container top [container_name/container_id] <br>
  ** L'option "top" permet d'afficher les processus qui est actuellement en cours d'exécution sur le conteneur.
  
* Que se passe-t-il réellement lors d'un "docker container run" ?
  - Docker va d'abord cherher l'image que l'on souhaite utiliser dans son cache local.
  - S'il ne la trouve pas, il va faire une recherche sur le Docker Hub par défaut (dépôt que l'on peut modifier).
  - Télécharger la dernière version de l'image (sauf si on lui précised la version à télécharger) et la stocke dans son cache.
  - Création d'un nouveau conteneur basé sur l'image spécifiée et se prépare à l'exécuter.
  - Attribution d'une adresse IP virtuelle sur un réseau privé à l'intérieur du serveur docker (docker engine).
  - Si l'on utilise l'option --publish 80:80, il va ouvrir le port 80  de votre ordinateur/serveur, puis rediriger le trafic reçu sur ce port à destination du port 80 du conteneur.
  - Démarre le conteneur en utilisant la "CMD" spécifiée danas le fichier Dockerfile.

* Obtenir les informations du container en format JSON
  - $ docker container inspect [container_name/container_id] <br>
  ** Donne toutes les informations au format json à propos du container (son adresse IP, les interfcaes configurées, etc,...)
  
* Obtenir les statistiques du container
  - $ docker container stats [container_name/container_id] <br>
  ** Affiche les statistiques concernant l'utilisation de la RAM, du CPU, etc...

* Naviguer à l'interieur de vos conteneurs
  - $ docker container run -it --name [container_name] -- publish 80:80 [container_tool] bash <br>
  ** L'option "t" permet de simuler un vrai terminal, exactement comme le ferait SSH <br>
  ** L'option "i" permet de garder la session ouverte pour recevoir l'output des commandes que l'on tape. <br>
  ** La commande "bash" donne un terminal existant à l'interieur du container afin de pouvoir exécuter des commandes interprétables.<br>
  
* Pour exécuter la commande bash en mode interactif à l'interieur du container
  - $ docker container exec -it [container_name] bash <br>
  ** Attention, par exemple sur l'image alpine, bash n'est pas disponible, et il faut plutôt utiliser "sh"
  
* Lister les associations entre les ports des conteneurs et les ports de la machine physique
  - $ docker container port [container_name/container_id]

* Formater l'affichage des informations du docker inspect
  - $ docker container inspect --format '{{.NetworkSettings.IPAddress}}' [container_name]
  - $ docker container inspect --format "'{{.NetworkSettings.IPAddress}}' && '{{.NetworkSettings.MacAddress}}'" [container_name]<br>
  ** Permet de n'afficher que la ligne correspondante à l'adresse aux propriétés du conteneurs parmi toutes les infomations de la commande inspect
  
* Pour afficher les réseaux Docker existants
  - $ docker network ls <br>
  ** Affiche les réseaux Dockers qui existent, par défaut, ils sont au nombre de 3 : Bridge (ou Docker0), Host et None <br>
  ** Le Réseau "Host" correspond au réseau qui attache les conteneurs directement à l'hôte (SANS NAT) ce ui est franchement délétère au niveau de la sécurité, mais augmente les performances. Donc l'addresse IP du container correspondra à l'addresse IP de ma carte réseau. <br>
  ** Le Réseau "None" correspond au réseau qui enlève l'interface eth0 du conteneur et ne laisse que l'interface loclahost d'existante.<br>

* Création des nouveaux Réseaux
  - $ docker network create [network_name] <br>
  ** Permet de créer un nouveau réseau auquel pourront s'attacher les containers.<br>
  ** Le Driver par défaut auquel sera attché le nouveau réseau est le bridge qui permet simplement de pouvoir attacher un sous-réseau au nouveau réseau créé. <br> 

* Création d'un container en spécifiant le réseau
  - $ docker container run --name [container_name] --network [network_name] --detach [container_tool] <br>
  ** L'option "--network" permet d'attcaher le conteneur à un réseau spécifique.

* Pour connecter un container existant à un nouveau réseau créé
  - $ docker network connect [network_name] [conatiner_name] <br>
  ** Permet d'attacher un container à un nouveau réseau (mais n'enlève pas l'attchement au réseau précédent) <br>
  
* Analyser le cache d'une image docker
  - $ docker image history [container_name] <br>
  ** Montre les differentes couches de modifications qui ont été appliquées à l'image docker.<br>
  Toutes les images démarrent au tout début avec une couche vide appelée "scratch", et tous les changements qui arrivent ensuite sur le système de fichiers de cette image est une autre couche.<br>
  Tous les changements n'influent pas sur la taille de l'image, comme pour les métadonnées (comme les commandes).
  
* Obtenir les informations sur l'image
  - $ docker image inspect [image_tool]
  
* Taguer les images et les pousser dans docker hub
  - $ docker image tag [image_tool]:[tag] [Pseudo]/[image_target]:[tag]
  - $ docker image tag mysql:latest akkyn/mysql_test:latest

* Pour pousser l'image sur docker hub
  - $ docker image push [Pseudo]/[image_name]
  - $ docker image push akkyn/mysql_test <br>
  ** Upload les modifications des couches de l'image vers Docker Hub par défaut (on peut changer le dépôt destination)
  
* Qu'est-ce que Dockerfile ?
  - Les dockerfiles sont des fichiers textes décrivant les différentes étapes de création d'un conteneur totalement personnalisé.

* Pour construire une image
  - $ docker image build -t [image_name]:[tag] . <br>
  ** Vous pouvez vous trouver dans le repertoire contenant le fichier Dockerfile pour exécuter la commande.<br>
  L'option -t indique que l'image va être taguée <br>
  Attention à ne pas oublier le point à la fin de la commande. <br>
  
* La durée de vie d'un conteneur
  - Les conteneurs sont conçus pour être la plupart du temps immuable et éphémère (non changeant, et temporaire)<br>
  - L'infrastucture immuable : on ne fait que déployer les conteneurs, ceux-ci ne changent jamais. Si des modifications sont nécessaires, on redéploye de nouveaux conteneurs basés sur des images différentes.<br>
  - Bénéfices : fiabilité et consistance, tout en permettant la reproduction de tous les changements.<br>
  - On parle de persistance des données lorsqu'on peut conserver celles-ci même si le conteneur a été supprimé ou recréé. très utilie surtout dans les conteneurs avec l'utilisation de bases de données.
  - On peut utiliser les volumes lorsqu'on veut partager les données entre plusieurs conteneurs qui sont en cours d'exécution ou stocker les données du conteneur sur un hôte distant ou sur le cloud. Et Lorsqu'on souhaite restaurer, sauvergarder ou migrer les données depuis un hôte Docker vers un autre hôte.
  - On utilisera les bind mounts lorsque le fichier des configuations de l'hôte ou du serveur sont à partager.

* Lister les volumes actuels dans la machine
  - $ docker volume ls
  - 
* Information du volume
  - $ docker volume inspect [volume_name/volume_id]
  - 
* Démarrer un container dans un volume docker en lui précisant le nom
  - $ docker container run -d --name [container_name] -e MYSQL_ALLOW_EMPTY_PASSWORD-True -v [volume_name]:[repertoire] [image_tool]:[tag]
  - $ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD-True -v mysql-db:/var/lib/mysql mysql:latest <br>
  ** L'option "-v" permet de spécifier le volume à crées. On commence par le nom du volume ici mysql-db, puis après les ":", on spécifie le chemin du volume à l'intérieur du conteneur, ici "/var/lib/mysql"

* Créer un volume
  - $ docker volume create
  
* Supprimer tous les volumes qui sont rélier à aucun conteneur
  - $ docker volume prune
 
* Pour ouvrir la commande bash sur un conteneur
  - $ docker exec -it [container_name] bash
