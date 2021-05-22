# docker-pratices

* Quelle est la difference entre "image" et "conteneur" ?
  - Une image est constituée des fichiers binaires, des librairies et du code source qui compose l'application.
  - Un container correspond à l'instance, en cours d'exécution, de l'image.

* Télécharger un Tools sur DockerHub
  - <span>$ docker container run --publish 80:80 nginx </span><br>
  ** Démarrer un nouveau conteneur utilisant l'image nginx téléchargée sur DockerHub, et la partie "publish 80:80" expose le port local 80 de la machine hôte, et redige tout le trafic à l'application exécutée dans le conteneur sur son port 80.

* Lister les conteneurs en exécutions
  - $ docker container ls
  - $ docker ps
* Lister tous les conteneurs
  - $ docker container ls -a 
  - $ docker ps -l

* Stopper le container en cours d'exécution
  - <span>$ docker container stop [container_id]</span>
  - <span>$ docker container stop [container_name]</span>

* Supprimer le container en cours d'exécution
  - <span>$ docker container rm [container_id]</span>
  - <span>$ docker container rm [container_name]</span><br>
  ** Si celui-ci est en cours d'exécution, alors
  - $ docker container rm -f [container_name/container_id] <br>
    *** Permet de supprimer définitivement un container même si celui-ci n'a pas été stoppé au préalable.<br>
  ** vous pouvez supprimer plusieurs conteneurs également
  - $ docker container rm [container_name1] [container_name2] [container_name3]...

* Lancer un container tout en restant detaché de la console
  - <span>$ docker container run --publish 80:80 --detach nginx</span> <br>
  ** L'option "--detach" permet d'exécuter le demarrage du container en arrière-plan, vous donnant la main pour effectuer d'autres actions.

* Donner un nom à son container
  - <span>$ docker container run --publish 80:80 -- detach --name [container_name] [container_tool]</span>
  - Exemple : $ docker container run --publish 80:80 -- detach --name nginx_webserver nginx <br>
  ** L'option "--name" permet de spécifier un nom et de l'associer à ce conteneur, ce qui est beaucoup plus simple par la suite pour pouvoir intéragir avec lui et lui envoyer des commandes (plutôt que d'utiliser son id)
  
* Afficher les logs en cours
  - <span>$ docker container logs [container_name/container_id]</span><br>
  ** L'option "logs" permet d'afficher les logs du conteneur indiqué soit par son ID, soit par son nom.
  
* Afficher les processus en cours
  - <span>$ docker container top [container_name/container_id]</span> <br>
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
  - <span>$ docker container inspect [container_name/container_id]</span> <br>
  ** Donne toutes les informations au format json à propos du container (son adresse IP, les interfcaes configurées, etc,...)
  
* Obtenir les statistiques du container
  - <span>$ docker container stats [container_name/container_id]</span> <br>
  ** Affiche les statistiques concernant l'utilisation de la RAM, du CPU, etc...

* Naviguer à l'interieur de vos conteneurs
  - <span>$ docker container run -it --name [container_name] -- publish 80:80 [container_tool] bash</span> <br>
  ** L'option "t" permet de simuler un vrai terminal, exactement comme le ferait SSH <br>
  ** L'option "i" permet de garder la session ouverte pour recevoir l'output des commandes que l'on tape. <br>
  ** La commande "bash" donne un terminal existant à l'interieur du container afin de pouvoir exécuter des commandes interprétables.<br>
  
* Pour exécuter la commande bash en mode interactif à l'interieur du container
  - <span>$ docker container exec -it [container_name] bash</span> <br>
  ** Attention, par exemple sur l'image alpine, bash n'est pas disponible, et il faut plutôt utiliser "sh"
  
* Lister les associations entre les ports des conteneurs et les ports de la machine physique
  - <span>$ docker container port [container_name/container_id]</span>

* Formater l'affichage des informations du docker inspect
  - <span>$ docker container inspect --format '{{.NetworkSettings.IPAddress}}' [container_name]</span>
  - <span>$ docker container inspect --format "'{{.NetworkSettings.IPAddress}}' && '{{.NetworkSettings.MacAddress}}'" [container_name]</span><br>
  ** Permet de n'afficher que la ligne correspondante à l'adresse aux propriétés du conteneurs parmi toutes les infomations de la commande inspect
  
* Pour afficher les réseaux Docker existants
  - $ docker network ls <br>
  ** Affiche les réseaux Dockers qui existent, par défaut, ils sont au nombre de 3 : Bridge (ou Docker0), Host et None <br>
  ** Le Réseau "Host" correspond au réseau qui attache les conteneurs directement à l'hôte (SANS NAT) ce ui est franchement délétère au niveau de la sécurité, mais augmente les performances. Donc l'addresse IP du container correspondra à l'addresse IP de ma carte réseau. <br>
  ** Le Réseau "None" correspond au réseau qui enlève l'interface eth0 du conteneur et ne laisse que l'interface loclahost d'existante.<br>

* Création des nouveaux Réseaux
  - <span>$ docker network create [network_name]</span> <br>
  ** Permet de créer un nouveau réseau auquel pourront s'attacher les containers.<br>
  ** Le Driver par défaut auquel sera attché le nouveau réseau est le bridge qui permet simplement de pouvoir attacher un sous-réseau au nouveau réseau créé. <br> 

* Création d'un container en spécifiant le réseau
  - <span>$ docker container run --name [container_name] --network [network_name] --detach [container_tool]</span> <br>
  ** L'option "--network" permet d'attcaher le conteneur à un réseau spécifique.

* Pour connecter un container existant à un nouveau réseau créé
  - <span>$ docker network connect [network_name] [conatiner_name]</span> <br>
  ** Permet d'attacher un container à un nouveau réseau (mais n'enlève pas l'attchement au réseau précédent) <br>
  
* Analyser le cache d'une image docker
  - <span>$ docker image history [container_name]</span> <br>
  ** Montre les differentes couches de modifications qui ont été appliquées à l'image docker.<br>
  Toutes les images démarrent au tout début avec une couche vide appelée "scratch", et tous les changements qui arrivent ensuite sur le système de fichiers de cette image est une autre couche.<br>
  Tous les changements n'influent pas sur la taille de l'image, comme pour les métadonnées (comme les commandes).
  
* Obtenir les informations sur l'image
  - $ docker image inspect [image_tool]
  
* Taguer les images et les pousser dans docker hub
  - <span>$ docker image tag [image_tool]:[tag] [Pseudo]/[image_target]:[tag]</span>
  - $ docker image tag mysql:latest akkyn/mysql_test:latest

* Pour pousser l'image sur docker hub
  - <span>$ docker image push [Pseudo]/[image_name]</span>
  - $ docker image push akkyn/mysql_test <br>
  ** Upload les modifications des couches de l'image vers Docker Hub par défaut (on peut changer le dépôt destination)
  
* Qu'est-ce que Dockerfile ?
  - Les dockerfiles sont des fichiers textes décrivant les différentes étapes de création d'un conteneur totalement personnalisé.

* Pour construire une image
  - <span>$ docker image build -t [image_name]:[tag] . </span><br>
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
  
* Information du volume
  - $ docker volume inspect [volume_name/volume_id]
  
* Démarrer un container dans un volume docker en lui précisant le nom
  - <span>$ docker container run -d --name [container_name] -e MYSQL_ALLOW_EMPTY_PASSWORD-True -v [volume_name]:[repertoire] [image_tool]:[tag]</span>
  - $ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD-True -v mysql-db:/var/lib/mysql mysql:latest <br>
  ** L'option "-v" permet de spécifier le volume à crées. On commence par le nom du volume ici mysql-db, puis après les ":", on spécifie le chemin du volume à l'intérieur du conteneur, ici "/var/lib/mysql"

* Créer un volume
  - $ docker volume create
  
* Supprimer tous les volumes qui sont rélier à aucun conteneur
  - $ docker volume prune
 
* Pour ouvrir la commande bash sur un conteneur
  - <span>$ docker exec -it [container_name] bash</span>

* Volumes
  - <span>$ docker container run -d --name [container_name] -p 8080:80 -v [volume_name]:[repertoire] [image_tool]:[tag] </span><br>
  ** L'avantage est que même en supprimant le présent container et en créant un nouveau container, tant qu'on utilise le même volume, on garde toutes les données antérieures (monter des versions par exemple)

* Bind Mounting
  - <span>$ docker container run -d --name [container_name] -p 8080:80 -v [PATH_repertoire_courant]:[repertoire] [image_tool]:[tag]</span>
  - $ docker container run --name my_apache3 -p 8050:80 -v "C:\Users\josue\Documents\GitHub\udemy-docker\Dockerfile\Exemple 2":/usr/local/apache2/htdocs/ httpd:2.4 <br>
  ** Less données se trouvant dans ce repertoire change au moindre changement du côté serveur.
 
 * Quelle commande permet de construire une image Docker ayant pour nom webportal et pour tag 1.0 à partir du Dockerfile disponible dans le répertoire courant ?
  - <span>$ docker build -t webportail:1.0 .</span>
 
 * Quelle commande peut être utilisée pour voir le point de montage d'un volume nommé nginx-vol ?
  - <span>docker volume inspect nginx-vol</span>

* Pour investiguer un problème, vous avez besoin de lancer un shell interactif (bash) dans un container en cours d'exécution nommé mycontainer. Votre but est de pouvoir exécuter des commandes depuis l'intérieur du container. Quelle commande utiliseriez-vous pour lancer ce shell ?
  - <span>$ docker exec -it mycontainer /bin/bash</span>
 
* Quel est le rôle de l'instruction EXPOSE dans un Dockerfile ?
  - Elle indique au démon Docker qu'il devra router l'ensemble du trafic du container vers l'hôte par l'utilisation de NAT

* Vous avez besoin de l'image nginx avec tag latest dans votre docker storage local. Vous décidez de récupérer cette image depuis un dépôt public. Quelle commande utiliseriez-vous ?
  - <span>$ docker pull nginx:latest</span>

* Une application appelée "dashboard" basée sur l'image nginx:latest a besoin du répertoire /var/www/html/ de la machine hôte monté dans le container en tant que volume nommé "web_dir". Quelle commande utiliseriez-vous ?
- <span>$ docker run --name dashboard -v web_dir:/var/www/html nginx:latest</span>

* Quelle commande utiliser pour supprimer toutes les images non-utilisées de votre hôte docker et ainsi récupérer l'espace disque correspondant ?
  - <span>$ docker image prune</span>
 
* Quelle instruction du Dockerfile pouvez-vous utiliser pour affecter une variable d'environnement dans le container s'exécutant à partir de l'image correspondante ?
  - $ ENV

* Quelle commande vous permet de retourner uniquement ipaddress du container mycontainer ?
  - <span>$ docker inspect -f '{{.NetworkSettings.IPAddress}}' mycontainer</span>
 
* Vous avez besoin que le démon docker supprime automatiquement le système de fichiers quand le container se termine. Quelle option de la ligne de commande devez-vous passer à la création du container ?
  - "--rm"

Fait par Josue Lubaki
