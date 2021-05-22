# Cet exemple montre comment il est simple d'étendre l'usage des images
# officielles de Docker Hub

# On indique qu'on va utiliser la dernière version de l'image officielle de
# nginx
FROM nginx:latest


# On préfère utiliser l'option WORKDIR pour indiquer le répertoire du conteneur
# dans lequel on veut se placer (plutôt que d'utiliser le "RUN cd /usr/share/nginx/html")
WORKDIR /usr/share/nginx/html

# On copie le fichier sample.html de mon ordinateur à destination du répertoire du conteneur
# dans lequel je me situe actuellement (/usr/share/nginx/html grâce au paramètre précédent)
# et on le nomme index.html
COPY sample.html index.html