mvn clean verify

=> creation dossier target 

Attention mettre son jdk à jour 
 sudo apt-get install default-jdk

 jdk 17 : 

 sudo add-apt-repository ppa:linuxuprising/java
  sudo apt install oracle-java17-installer
# Questions

2-1 Que sont les conteneurs de test?
  Testcontainers est une bibliothèque Java qui fournit des instances légères et jetables de bases de données courantes. Elle prend en charge les tests JUnit et est distribuée sous forme de JAR distincts avec un numéro de version commun. 
  Les conteneurs de test facilitent différents types de tests, tels que les tests d'intégration des couches d'accès aux données, les tests d'interface utilisateur/acceptation et les tests d'intégration des applications. Ils permettent de définir les dépendances des tests en tant que code, ce qui évite d'avoir recours à des mocks ou à des configurations d'environnement compliquées. Testcontainers est disponible en tant que dépendance Maven, et la dernière dépendance Maven de la bibliothèque principale est "org.testcontainers:testcontainers

  Ce sont simplement des bibliothèques java qui vous permettent d'exécuter un tas de conteneurs docker pendant les tests