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
  ** Permet d'attacher un container à un nouveau réseau (mais n'enlève pas l'attchement au réseau précédent)
