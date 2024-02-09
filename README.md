
--------
# Devops
## DataBase : Postgres 

Explication du Dockerfile de PostgresSQL

**Import de l'image officielle de PostgresSQL**

FROM postgres:14.1-alpine

> Ce n'est pas la derniere version  mais c'est une image alpine qui est plus legère et donc plus rapide à démarrer 
L'image officielle est basée sur Alpine, une distribution Linux très légère. Pour plus d'informations, vous pouvez consulter la documentation [ici](https://hub.docker.com/_/postgres/)

**Variables d'environnement pour le démarrage du serveur**

ENV POSTGRES_DB=db \
POSTGRES_USER=usr \
POSTGRES_PASSWORD=pwd

> Cela permet  de définir les informations de connexion à la base de données lorsqu'elle sera build.



**Copier les scripts d'initialisation dans le conteneur**

COPY ./Script/* /docker-entrypoint-initdb.d/

> Les fichiers `.sql` présents dans ce répertoire seront exécutés au démarrage du container, ils permettent par exemple de créer des tables ou d'inserer des lignes ect ...

#### Build  et exécution de BDD

````
docker build -t carottealpha/base .
````
> Attention ne pas oublier le point

Creation d'un network  pour les containers :

```` 
docker  network create bd-network
````

Creation d'un volume :

````
 docker volumer create db-data
````

Puis lancer  le container avec cette image en utilisant la commande dans un reseau  docker (voir plus bas) :
````
docker run -d -p  5433:5432 --name=pg carottealpha/base 
````
````
docker run -d -p  5433:5432 --name=pg --network bd-network carottealpha/base 
````

**Resoudre le problème persistence** 

utilisation de l'option -v
````
 -v /my/own/datadir:/var/lib/postgresql/data
````
> la persistence permet  à PostgresSQL de conserver ses données même si le container est supprimé.
````
docker run -d -p  5433:5432 -v /my/own/datadir:/var/lib/postgresql/data --name=pg --network db-network carottealpha/base
````
Pour lancer votre instance de BDD, il vous suffit de faire un simple :

````
docker run -d --net=db-network -v db-data:/var/lib/postgresql/data --name=database carottealpha/base 
````
> l'indication du réseau est importe pour accéder plus tard avec adminer


Lancer Adminer:

````
docker run -d -p 8090:8080 --net=bd-network --name=adminer adminer
````
Accès à l'interface d'Adminer via son port publié : [http://localhost:8090](http://localhost:8090)

Vous pouvez maintenant vous connecter à votre base PostgreSQL.
Connectez-vous à la base de données à l'aide d'Adminer  et vérifiez que vous pouvez vous connecter correctement à partir de là.

````
        environment:
          POSTGRES_DB: db
          POSTGRES_USER: usr
          POSTGRES_PASSWORD: pwd
````

Documentation des éléments essentiels des conteneurs de base de données

Commandes :

- docker-compose up -d : Lance le fichier docker-compose.yml dans le répertoire courant en mode détaché.
- docker-compose down : Arrête tous les conteneurs Docker correspondant au répertoire courant.
- docker-compose logs -f : Affiche les journaux (logs) et suit les logs à venir en continu.
- docker-compose ps : Affiche des informations relatives aux conteneurs enfants du Docker Compose dans le répertoire courant.
- docker-compose --build : Reconstruit les images Docker.

Instructions Dockerfile :

FROM : Utilisé pour spécifier l'image Docker à utiliser en tant que base.

ENV : Utilisé pour définir des variables d'environnement.

COPY : Utilisé pour copier des fichiers dans le conteneur Docker après l'initialisation.

## Back-End : Java
Premier application Java voici l'explication du DockerFile.
Environnement d'exécution Java (JRE)

````
# Choose a Java JRE base image
FROM openjdk:17-alpine

# Copy the compiled Java class into the container
COPY Main.class /app/Main.class

# Set the working directory
WORKDIR /app

# Run the Java class
CMD ["java", "Main"]

````
Dockerfile: 



- FROM openjdk:17-alpine : Choisi l'image OpenJDK 17 JRE Alpine comme image de base pour le conteneur Docker. Cela signifie que le conteneur sera basé sur une image contenant OpenJDK 17 JRE (Java Runtime Environment) sur une distribution Alpine Linux, une version légère d'Alpine Linux.

- COPY Main.class /app/Main.class : Copie le fichier compilé Main.class depuis le système hôte vers le répertoire /app à l'intérieur du conteneur Docker. Cela permet d'intégrer la classe Java compilée dans le conteneur.

- WORKDIR /app : Définit le répertoire de travail à l'intérieur du conteneur Docker. Toutes les instructions suivantes dans le Dockerfile seront exécutées dans ce répertoire. Ici, il est défini sur /app, ce qui signifie que toutes les commandes exécutées dans le conteneur seront effectuées dans ce répertoire.

- CMD ["java", "Main"] : Spécifie la commande à exécuter lorsque le conteneur démarre. Dans ce cas, il exécute la commande java Main, ce qui signifie qu'il exécute la classe Java Main à l'intérieur du conteneur. Cela suppose que le fichier Main.class contient une méthode main() qui sert de point d'entrée pour l'exécution de l'application Java.


Build Docker Image: 
````
docker build -t firstapp .
````

Run Docker Container: 
````
docker run firstapp
````


> Why do we need a multistage build? And explain each step of this Dockerfile.

Réduction de la taille de l'image : Les constructions en plusieurs étapes vous permettent de construire des images intermédiaires avec des dépendances de construction, puis de ne copier que les artefacts nécessaires dans l'image finale. Cela permet de réduire la taille de l'image finale, ce qui la rend plus petite et plus efficace à distribuer et à déployer.

Amélioration de la vitesse de construction : en utilisant des constructions en plusieurs étapes, vous pouvez exploiter des étapes de construction distinctes pour mettre en cache les dépendances et les artefacts intermédiaires. Cela permet d'accélérer le processus de construction, en particulier pour les applications complexes avec de multiples dépendances, car Docker peut réutiliser les couches mises en cache lors des étapes de construction précédentes.

Séparation des dépendances de construction et d'exécution : Les constructions en plusieurs étapes vous permettent de séparer les dépendances de construction (par exemple, les compilateurs, les outils de construction) des dépendances d'exécution (par exemple, les bibliothèques, les environnements d'exécution). Cette séparation permet de s'assurer que l'image finale ne contient que les composants nécessaires à l'exécution, ce qui réduit la surface d'attaque et les vulnérabilités potentielles.

Amélioration de la sécurité : En minimisant le nombre de paquets et de dépendances inclus dans l'image finale, les constructions en plusieurs étapes permettent de réduire les risques de sécurité potentiels associés aux composants inutilisés ou superflus. Cela facilite la maintenance et la mise à jour de l'image au fil du temps, car il suffit de se concentrer sur les dépendances essentielles.

Simplification de la configuration de la compilation : Les constructions en plusieurs étapes offrent une configuration de construction plus propre et plus facile à maintenir, car vous pouvez organiser le processus de construction en étapes distinctes avec une séparation claire des préoccupations. Cela facilite la compréhension et la gestion du pipeline de construction, en particulier pour les projets plus importants et plus complexes.

Explanation of Each Step in the Dockerfile:


````
# Build stage
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
````

- Cette ligne définit la première étape de la construction en plusieurs étapes.
- Elle utilise l'image Maven avec une version spécifique (3.8.6-amazoncorretto-17) comme image de base pour l'étape de construction.
- Il définit le nom de cette étape comme myapp-build pour référence dans les étapes suivantes.
- Dans le cas de ce projet, le paquetage utilisé était maven:3-eclipse-temurin-17.

````
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
````
- Définit une variable d'environnement MYAPP_HOME à /opt/myapp.
- Définit le répertoire de travail à /opt/myapp.
````
COPY pom.xml .
COPY src ./src
````
- Copie le fichier pom.xml et le répertoire src (contenant le code source) du système de fichiers local dans le répertoire de travail du conteneur Docker.

````
RUN mvn package -DskipTests
````
- Exécute la commande Maven pour empaqueter l'application.
- L'option -DskipTests est utilisée pour ne pas exécuter les tests pendant le processus de construction.
- Avant cela, vous pouvez ajouter RUN mvn dependency:go-offline pour limiter le téléchargement du paquetage par Maven.
````
# Run stage
FROM amazoncorretto:17
````

- Cette ligne définit la deuxième étape de la construction en plusieurs étapes.
- Elle utilise l'image Amazon Corretto JDK avec Java 17 comme image de base pour l'étape d'exécution.
- Dans le cas de ce projet, le paquetage utilisé était eclipse-temurin:17.
````
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
````
- Définit la même variable d'environnement MYAPP_HOME et le même répertoire de travail qu'à l'étape de construction.
- Like the previous stage.
````
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar
````

- Copie le fichier JAR compilé de l'étape de construction vers l'étape d'exécution.
--from=myapp-build spécifie que la source de l'opération de copie provient de l'étape myapp-build.

````
ENTRYPOINT java -jar myapp.jar
````
Définir la commande à exécuter au démarrage du conteneur.
- Il lance l'application Java en exécutant le fichier JAR (myapp.jar) à l'aide de la commande java -jar.
- Vous ne pouvez remplacer le point d'entrée par CMD que si un autre point d'entrée est défini dans l'image. Cela permet à l'image d'initier l'environnement simple-api-student/src/main/resources/application.yml 

> Ne pas oublier de mettre les parametres pour la connection à votre bd voir la configuration dans 
**Ajustez la configuration de l'application ** : Ajustez la configuration dans simple-api/src/main/resources/application.yml pour vous connecter au conteneur de base de données. Vous devrez spécifier l'URL de la base de données, le nom d'utilisateur et le mot de passe dans le fichier de configuration. Par exemple : 
````
    url: jdbc:postgresql://database:5432/db
    username: usr
    password: pwd
````
Accès au conteneur de base de données du Backend:

Build the backend image
````
docker build -t spitii/devops-backend:latest Backend_2.0
````
Lancez le backend et connectez-le au même réseau que la base de données.
````
docker run -d --name backend --network bd-network -p 8080:8080 carottealpha/backend
````
Accès à l'API : Une fois que les deux conteneurs fonctionnent et sont connectés au même réseau, vous devriez pouvoir accéder à l'API de votre application. 
Disponible sur localhos

 Par exemple, si votre API expose des points d'extrémité comme /departments/, vous pouvez y accéder à l'aide d'un navigateur web [http://localhost/departments/](http://localhost/departments/)

Cela devrait renvoyer la réponse JSON contenant les informations sur les étudiants telles que spécifiées. 

## Http : Apache
Dockerfile 
````
# Use the official httpd image as the base
FROM httpd:latest

# Copy conf and entrypoint of Apache 2 server
COPY httpd.conf /usr/local/apache2/conf/httpd.conf
COPY index.html /usr/local/apache2/htdocs/index.html

````
Commandes de construction du conteneur de base de données Construisez l'image Docker : Exécutez la commande suivante dans le répertoire contenant le fichier Docker

````
docker build -t carottealpha/httpd .
````
Redémarrer l'Api sans port:

````
docker run -d --name backend --network app-network carottealpha/backend
````
Démarrer http :

````
docker run -d --net=app-network -p "80:80" --name=httpd carottealpha/httpd

````
Maintenant, vous avez accès à index.html dans localhost (ou ip du serveur).

> Et l'accès à l'api avec /api/ au milieu de la demande d'api et de l'élément frontal.



> Vous avez une autre alternative à apache. Comme caddy, ces outils ont une certification https automatique.

**Document your publication commands and published images in dockerhub.**

La commande docker tag backend carottealpha/backend:1.0 renomme l'image Docker appelée "backend" en lui attribuant une nouvelle étiquette, "carottealpha/backend:1.0". Cela permet de donner une nouvelle identité à l'image existante en lui assignant un nouveau nom (carottealpha/backend) ainsi qu'une nouvelle version (1.0).

La commande docker push carottealpha/backend:1.0 transfère l'image Docker étiquetée (carottealpha/backend:1.0) vers le dépôt Docker Hub. Ce processus la rend disponible publiquement pour toute personne disposant des autorisations adéquates pour accéder à ce référentiel spécifique sur Docker Hub.

** Why do we need a reverse proxy ? *
Le reverse proxy joue un rôle essentiel en nous permettant d'accéder à l'API via l'application web sans avoir à exposer directement notre port d'API. Il offre également la possibilité de nous connecter à d'autres API ou services ultérieurement, si nécessaire. En résumé, le reverse proxy assure une couche de sécurité et de flexibilité en facilitant l'accès à l'API tout en préservant la confidentialité de notre infrastructure.

** Why is docker-compose so important? **
Simplified Deployment: Docker-compose allows you to define and run multi-container Docker applications with a single command. This simplifies the deployment process, making it easier to manage complex applications with multiple services.

Service Orchestration: Docker-compose enables you to define the configuration of your entire application stack, including services, networks, and volumes, in a single YAML file. This facilitates the orchestration of services and ensures consistency across different environments.

Environment Standardization: With Docker-compose, developers can define the environment and dependencies required for their applications, ensuring consistency between development, testing, and production environments. This helps in reducing issues related to environment discrepancies.

Scalability: Docker-compose allows you to scale individual services independently based on demand. By specifying the desired number of container instances for each service, you can easily scale your application to handle varying workloads.

Resource Management: Docker-compose provides options for managing resource allocation, including CPU, memory, and storage limits for each service. This helps in optimizing resource utilization and ensuring that applications perform efficiently.

Portability: Docker-compose configurations are portable and can be shared across different environments and platforms. This facilitates collaboration among team members and ensures consistent deployment across various infrastructure setups.



## GitHub action 

**What are testcontainers ?** 

Testcontainers est une bibliothèque Java open-source qui fournit des instances légères et jetables de conteneurs Docker à utiliser dans les tests d'intégration. Elle permet aux développeurs de créer facilement des conteneurs Docker dans le cadre de leurs tests automatisés, d'exécuter des tests sur ces conteneurs, puis de démanteler les conteneurs une fois les tests terminés.
Le conteneur de test est un environnement léger,
il permet de fournir un environnement léger pour le test.
Dans votre cas, il fournit une image docker postgres légère pour le test junit.


## GitHub action test pipeline
The test pipeline like :

````
name: test-backend
on:
  # to begin you want to launch this job in main and develop
  push:
    # on donne les branches à tester (la develop existe pas encore)
    branches:
      - main
      - develop
  

jobs:
  test-backend:
   # if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/develop'
    runs-on: ubuntu-22.04
    steps:
      # checkout your github code using actions/checkout@v2.5.0
      - uses: actions/checkout@v2.5.0

      # do the same with another action (actions/setup-java@v3) that enable to setup jdk 17
      - name: Set up JDK 17
        # on utilise l'action setup java officiel
        uses: actions/setup-java@v3
        with:
          # on utilise le même jdk que le Dockerfile
          distribution: 'corretto'
          # on précise la même version que le Dockerfile
          java-version: '17'

      # finally build your app with the latest command
      - name: Build and test with Maven
        # on run maven clean verify dans le dossier du backend
        run: mvn -B verify sonar:sonar -Dsonar.projectKey=sonardevopssebou_tp2 -Dsonar.organization=sonardevopssebou -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }}  --file ./TP1/API/simple-api-student-main/simple-api-student-main/pom.xml

       # run: mvn clean verify --file ./TP1/API/simple-api-student-main/simple-api-student-main/pom.xml

 
````

> Lorsqu'un push est effectué sur la branche Main ou Develop, un test du Backend est lancé. Si ce push a été effectué sur la branche Main et qu'il est terminé avec succès, alors le build et le push sur Docker Hub sont déclenchés. Enfin, le déploiement est initié pour déployer le code sur les serveurs de production.

## Ansible

> Il faut  installer ansible sur votre machine de travail et définir une clé ssh pour vous connecter

> il faut variabiliser au maximun les variables tel que nom de la bd le port ect ect afin que le moindre  changement puisse être intégré sans modification directe des fichiers de configuration.
inventories/setup.yml

> il faut aussi pour plus de sécurité créeer des networks pour chaque service.

> Bien définir les roles dans playbouk.yml

