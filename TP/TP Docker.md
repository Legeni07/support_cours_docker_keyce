#### TP 1 : Installation de Docker et docker compose 

# Installing docker on ubuntu

1- Get the installation script
curl -fsSL https://get.docker.com -o get-docker.sh

2- Run script
sudo sh get-docker.sh --version 20.10.13

--------------

# Post-installation tasks

1- Put the centos user in the docker group so that he can use docker and avoid using the root user.

sudo usermod -aG docker centos

2- Starting the docker service

sudo systemctl start docker
sudo systemctl status docker

3- Disconnect and reconnect

4- test

docker ps

# Deploy odoo software using docker compose

1- Installing docker compose

   sudo curl -SL https://github.com/docker/compose/releases/download/v2.23.3/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   docker-compose -v

2- Deploy application

   - In the 05_lab-5 folder in this directory, check that the access port is 80 and that the odoo and db containers are in the odoo_network network
   - Deploy: docker-compose up -d
   - Verify: docker ps
   - In the browser: 192.168.99.10:80 

#### TP2 : Dockerfile , images , conteneur
# ---------Dockerfile-------
FROM ubuntu:22.04
LABEL maintainer="Legeni"
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y nginx git
RUN rm -Rf /var/www/html/*
RUN git clone https://github.com/Legeni0/static-website-example.git /var/www/html/
EXPOSE 80
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]


----------------------------



Création d'un compte DockerHub et GitHub
1- Création d'un compte DockerHub

Tapez dockerhub dans le navigateur
Allez à la connexion
Créez un nouveau compte
Suivez la procédure de création de compte

2- Création d'un compte GitHub

Tapez github dans le navigateur
Allez à la connexion
Créez un nouveau compte
Suivez la procédure de création de compte


Création et test de l'image
1- Créer l'image :

mkdir webapp
cd webapp
nano Dockerfile
copiez le contenu du Dockerfile dans le répertoire webapp
--------------------
# ---------Dockerfile-------
FROM ubuntu:22.04
LABEL maintainer="Legeni"
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y nginx git
RUN rm -Rf /var/www/html/*
RUN git clone https://github.com/Legeni0/static-website-example.git /var/www/html/
EXPOSE 80
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
--------------------
récupérez le code : git clone https://github.com/Legeni07/static-website-example.git

2- Exécuter et tester l'image

docker build -t webapp_legeni:v1 .
vérifiez que l'image a été créée : docker images
démarrez un conteneur : docker run --name webapp -d -p 80:80 webapp:v1
Accédez à l'application dans le navigateur avec l'adresse de la machine suivie du port 80 : 192.168.99:80


Gestion de l'image sur DockerHub
1- Connectez-vous à votre compte DockerHub

nom d'utilisateur : training
mot de passe : training_dockerhub_password
pour se connecter à DockerHub : docker login

2- Pousser l'image

Tagger l'image : docker tag id_image eazytraining/webapp:v1
Pousser l'image : docker push training/webapp:v1

3- Vérification

Allez sur votre compte DockerHub, puis sur les dépôts
Vérifiez le nom de l'image que vous venez de pousser


### TP 3 : Volume, reseau, 
TAF : creer 2 conteneur B2A et B2B , 
1) Creer un réseau bridge entre les 2 conteneurs
2) créer les volumes temporaires et partager les données entre les deux via ces volumes 
3)  créer les volumes persistant et partager les données entre les deux via ces volumes

Étape 1 : Créer les conteneurs B2A et B2B

1. Ouvrez un terminal et exécutez les commandes suivantes pour créer les conteneurs :

docker run -it --name B2A ubuntu
docker run -it --name B2B ubuntu

taper 
exit 
puis 
docker start B2A
docker start B2B

1. Vérifiez que les conteneurs sont créés et en cours d'exécution :

docker ps


Étape 2 : Créer un réseau bridge entre les 2 conteneurs

1. Créez un réseau bridge :

docker network create -d bridge mon_reseau

1. Connectez les conteneurs au réseau bridge :

docker network connect mon_reseau B2A
docker network connect mon_reseau B2B

1. Vérifiez que les conteneurs sont connectés au réseau bridge :

docker network inspect mon_reseau


Étape 3 : Créer des volumes temporaires et partager les données entre les deux conteneurs

1. Créez un volume temporaire :

docker volume create mon_volume_temporaire

1. Montez le volume temporaire dans les conteneurs :

docker run -d --name B2A -v mon_volume_temporaire:/tmp ubuntu
docker run -d --name B2B -v mon_volume_temporaire:/tmp ubuntu

1. Créez un fichier dans le volume temporaire à partir de l'un des conteneurs :

docker exec -it B2A bash
echo "Bonjour" > /tmp/fichier.txt

1. Vérifiez que le fichier est accessible à partir de l'autre conteneur :

docker exec -it B2B bash
cat /tmp/fichier.txt


Étape 4 : Créer des volumes persistants et partager les données entre les deux conteneurs

1. Créez un volume persistant :

docker volume create mon_volume_persistant

1. Montez le volume persistant dans les conteneurs :

docker run -d --name B2A -v mon_volume_persistant:/mnt ubuntu
docker run -d --name B2B -v mon_volume_persistant:/mnt ubuntu

1. Créez un fichier dans le volume persistant à partir de l'un des conteneurs :

docker exec -it B2A bash
echo "Bonjour" > /mnt/fichier.txt

1. Vérifiez que le fichier est accessible à partir de l'autre conteneur :


cat /mnt/fichier.txt



1. Vérifiez que les volumes persistants sont toujours présents :

docker volume ls

