Lancer un build de la base : 

Dans un terminal etre dans le dossier TP1/Base lancer  la commande suivante:

docker build -t carottealpha/base .

Creation d'un network  pour les containers :

```` 
docker  network create bd-network
````

Creation d'un volume :

````
 docker volumer create 
Puis lancer  le container avec cette image en utilisant la commande dans un reseau  docker (voir plus bas) :
docker run -d -p  5433:5432 --name=pg carottealpha/base 
docker run -d -p  5433:5432 --name=pg --network app-network carottealpha/base 

persistence : 
option : -v /my/own/datadir:/var/lib/postgresql/data

docker run -d -p  5433:5432 -v /my/own/datadir:/var/lib/postgresql/data --name=pg --network app-network carottealpha/base


Creation d'un reseau :
docker network create app-network
fda64c4362d937405e7e8bc94feeef9263558cc4be2341ef69537381e4431eb1

Creation Adminer : 
docker run -p "8090:8080"  --net=app-network  --name=adminer -d adminer

lancer API: 
docker run -d -p  80:80  --name=api --network app-network carottealpha/api
copy conf htp
docker exec proxy cat /usr/local/apache2/conf/httpd.conf 
## Questions

1-1 Documentez les éléments essentiels de votre conteneur de base de données: commandes et Dockerfile.

Pourquoi avons-nous besoin d'un volume à attacher à notre conteneur postgres ? 
nous avons besoin d'un volume  pour persister les données, c'est a dire conserver les données même si l'on arrête et redémarre le conteneur. Cela permet également de partager des Pour persister les données, c'est-à-dire pour que ces dernières soient Pour persister les données, c’est-à-dire pour que ces dernières ne so


1-2 Why do we need a multistage build? And explain each step of this dockerfile ?
-2 Pourquoi avons-nous besoin d'une construction à plusieurs étages? Et expliquez chaque étape de ce dockerfile. 

1-3 Document d'accueil-composez les commandes les plus importantes. 1-4 Documentez votre fichier de composition de docker.
1-3 Document docker-compose most important commands. 1-4 Document your docker-compose file?
    Docker Compose is a powerful tool for creating and managing Docker containers. Here are some of the most important commands for working with Docker Compose:
    Start/Stop Containers:
    docker-compose up: Start all containers defined in the docker-compose.yml file.
    docker-compose down: Stop and remove the containers.
1-5 Document your publication commands and published images in dockerhub ?

