FROM ubuntu:16.04

RUN apt-get update && apt-get install nginx curl -y

EXPOSE 80 

WORKDIR /var/www/html

COPY sample.html index.html

CMD ["nginx", "-g", "daemon off;"]