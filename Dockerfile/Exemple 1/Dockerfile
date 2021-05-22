# Le FROM est un élément obligatoire à faire figurer dans le Dockerfile
# On utilise en général une distribution Linux minimaliste (comme debian ou alpine)
# Il est possible de partir d'un conteneur complètement vide en utilisant "FROM scratch"
FROM ubuntu:latest

# Vous pouvez définir de manière optionnelle une variable d'environnement
# L'avantage d'utiliser cette option, consiste au fait que peu importe la distribution
# Linux que vous utilisez, la commande reste la même pour injecter ces variables à l'intérieur
# de votre conteneur
ENV MA_VARIABLE "je suis une variable"

# Grâce au "RUN" vous pouvez exécuter de véritables commandes Shell à l'intérieur du conteneur
# au moment où il est buildé.
# La commande ci-dessous installe le paquet nginx et le paquet curl
RUN apt-get update && apt-get install nginx curl -y

# Par défaut, aucun port TCP ou UDP n'est ouvert
# La commande "EXPOSE" permet d'ouvrir les ports indiqués sur le conteneur
# Attention, cela n'empêche pas d'utiliser l'option --publish pour rediriger ces ports
# vers ceux de votre machine
EXPOSE 80 443

# Le dernier paramètre obligatoire CMD correspond à la commande à exécuter lorsque le conteneur
# démarre.
# Attention seule une commande CMD est autorisée et si vous en indiquez plusieurs, seule la dernière
# sera utilisée
CMD ["nginx", "-g", "daemon off;"]
