# student-list 
Ce dépôt est une application simple pour lister des étudiants avec un serveur web (PHP) et une API (Flask).

![project](https://user-images.githubusercontent.com/18481009/84582395-ba230b00-adeb-11ea-9453-22ed1be7e268.jpg)


------------


## Objectifs

Les objectifs de ce projet pratique sont de s'assurer que vous êtes capable de gérer une infrastructure Docker. 

### Themes:
- Améliorer le processus de déploiement d'une application existante
- Versionner vos releases d'infrastructure
- Aborder les meilleures pratiques lors de la mise en œuvre d'une infrastructure Docker
- Infrastructure en tant que code (Infrastructure as Code)

## Contexte

POZOS est une entreprise informatique située en France qui développe des logiciels pour les lycées.

Le département d'innovation souhaite révolutionner l'infrastructure existante pour garantir qu'elle puisse être évolutive, facilement déployable et maximisée en termes d'automatisation.

POZOS vous demande de construire un "POC" (Proof of Concept) pour montrer comment Docker peut les aider et à quel point cette technologie est efficace.

Pour ce POC, POZOS vous fournira une application et souhaite que vous construisiez une infrastructure "découplée" basée sur "Docker".

Actuellement, l'application fonctionne sur un serveur unique, sans évolutivité ni haute disponibilité.

Chaque fois que POZOS doit déployer une nouvelle version, quelque chose ne va pas.

En conclusion, POZOS a besoin d'agilité dans sa ferme logicielle.


## Infrastructure

Pour ce POC, vous n'utiliserez qu'une seule machine avec Docker installé dessus.

La construction et le déploiement se feront sur cette machine.

POZOS vous recommande d'utiliser le système d'exploitation CentOS 7.6 car c'est le plus utilisé dans l'entreprise.

Veuillez également noter que vous êtes autorisé à utiliser une machine virtuelle basée sur CentOS 7.6 et non votre machine physique.

La sécurité est un aspect très critique pour la DSI de POZOS, donc veuillez ne pas désactiver le pare-feu ou d'autres mécanismes de sécurité. Dans le cas contraire, veuillez expliquer vos raisons dans votre livraison.

## Application


L'application sur laquelle vous allez travailler s'appelle "*student_list*". Cette application est très basique et permet à POZOS d'afficher la liste des étudiants avec leur âge.

student_list a deux modules :

- Le premier module est une API REST (avec une authentification de base requise) qui envoie la liste souhaitée des étudiants basée sur un fichier JSON.
- Le deuxième module est une application web écrite en HTML + PHP qui permet à l'utilisateur final d'obtenir une liste d'étudiants.

Votre travail consiste à construire un conteneur pour chaque module et à les faire interagir entre eux.

Le code source de l'application peut être trouvé [ici](https://github.com/diranetafen/student-list.git "ici").

Les fichiers que vous devez fournir (dans votre livraison) sont ***Dockerfile*** et ***docker-compose.yml*** (actuellement, les deux sont vides).

Maintenant, il est temps de vous expliquer le rôle de chaque fichier :

- **docker-compose.yml** : pour lancer l'application (API et application web)
- **Dockerfile** : le fichier qui sera utilisé pour construire l'image de l'API (des détails seront fournis)
- **requirements.txt** : contient tous les paquets à installer pour faire fonctionner l'application
- **student_age.json** : contient les noms des étudiants avec leur âge au format JSON
- **student_age.py** : contient le code source de l'API en Python
- **index.php** : page PHP où l'utilisateur final se connectera pour interagir avec le service afin de lister les étudiants avec leur âge. Vous devez mettre à jour la ligne suivante avant de lancer le conteneur du site web pour adapter ***api_ip_or_name*** et ***port*** à votre déploiement :

```bash
$url = 'http://<api_ip_or_name:port>/pozos/api/v1.0/get_student_ages';




```

## Docker Registry

POZOS a besoin que vous déployiez un registre privé et stockiez les images construites.

Vous devez donc déployer :

- un [registre Docker](https://docs.docker.com/registry/ "registre")
- une [interface web](https://hub.docker.com/r/joxit/docker-registry-ui/ "interface") pour voir les images poussées en tant que conteneurs.

Ou vous pouvez utiliser [Portus](http://port.us.org/ "Portus") pour faire fonctionner les deux.

N'oubliez pas de pousser votre image sur votre registre privé et de les montrer dans votre livraison.


# PROPOSITION D'UNE SOLUTION

mkdir mini-projet-docker 
cd mini-projet-docker puis git clone https://github.com/nzapanarcisse/student-list.git

```bash
cd simple_api
docker build . -t api-pozos:1
```
![image](https://github.com/user-attachments/assets/09b6a479-be02-42c5-bddb-a28359aa0fb7)
```bash
docker images
```
![image](https://github.com/user-attachments/assets/cc34bf13-4a52-4f08-8984-572c134f175b)
# Création du Réseau Pozos

Nous venons de builder notre image pour l’API de l’application. Afin d’assurer une communication entre les deux microservices, nous allons créer un réseau dans lequel les deux applications vont tourner. Cela va faciliter la résolution de noms grâce aux fonctions DNS.

Vous devez savoir qu’il y a 4 types de réseaux sur Docker, comme vous l'avez vu en cours.
```bash
sudo docker network create pozos_network --driver=bridge
sudo docker network ls
```






