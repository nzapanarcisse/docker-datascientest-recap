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

Le code source de l'application peut être trouvé [ici](https://github.com/nzapanarcisse/docker-datascientest-recap.git)"ici").

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
cd mini-projet-docker puis git clone https://github.com/nzapanarcisse/docker-datascientest-recap.git

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
création du container api-pozos
```bash
cd ..
sudo docker run --rm -d -p 5000:5000 --name api_pozos --network pozos_network -v ./simple_api/:/data/ api-pozos:1
```

***L'option --rm indique à Docker de supprimer automatiquement le conteneur lorsque celui-ci s'arrête.***

***L'option -d signifie "détaché" (detached mode). Cela permet de lancer le conteneur en arrière-plan***

On monte le répertoire local `./simple_api/` dans le répertoire interne `/data/` du conteneur afin que l'API puisse utiliser la liste `student_age.json`.

on a donc monté un volume de type bind mound
```bash
docker ps -a

```
![image](https://github.com/user-attachments/assets/135aa1be-b5ba-4bf4-be94-e3dc9ada2c89)

# Création Webapp

Le front-end de  l'application est fait en PHP. Pour communiquer avec le back-end que vous venez de déployer, il faut modifier la configuration de l’application web pour lui donner l'hôte de l'API. C’est ce que vous allez faire.

Grâce aux fonctions DNS de notre réseau de type bridge, nous pouvons facilement utiliser le nom du conteneur API avec le port que nous avons vu juste avant pour adapter notre site web :

```bash
sed -i 's\<api_ip_or_name:port>\api_pozos:5000\g' ./website/index.php
```
lancement 
```bash
sudo docker run --rm -d --name webapp_pozos -p 80:80 --network pozos_network -v ./website/:/var/www/html -e USERNAME=toto -e PASSWORD=python php:apache
```
```bash
sudo docker ps
```
![image](https://github.com/user-attachments/assets/769341b2-4858-4ff1-a295-ae4cdac92897)

# Test Front-end et Back-end

Nous pouvons donc tester pour voir si effectivement le front-end communique avec le back-end. Pour ce faire, nous allons exécuter la commande suivante, qui va demander au conteneur front de faire une requête à l’API back-end et de retourner le résultat. L’objectif est de tester si l’API fonctionne et si le front-end peut obtenir la liste des étudiants à partir de celle-ci.

```bash
sudo docker exec webapp_pozos curl -u toto:python -X GET http://api_pozos:5000/pozos/api/v1.0/get_student_ages
```
![image](https://github.com/user-attachments/assets/13a12c59-2b51-471a-b566-3b41fe192007)
![image](https://github.com/user-attachments/assets/39d760b2-8306-4c3b-8006-6ecd47e5aab7)

Grâce à l'argument `--rm` que nous avons utilisé lors du démarrage de nos conteneurs, ceux-ci seront supprimés lorsqu'ils s'arrêteront. Supprimez le réseau précédemment créé :

```bash
sudo docker stop api_pozos
sudo docker stop webapp_pozos
sudo docker network rm pozos_network
sudo docker network ls
sudo docker ps
```
# Déploiement as Code

Comme les tests ont été passés, nous pouvons maintenant mettre les paramètres `docker run` que nous venons de voir dans un format d'infrastructure as code, dans un fichier `docker-compose.yml`. Cela va nous permettre d’avoir un livrable que nous pouvons envoyer au client.
voir le fichier ***docker-compose.yml***

***maintenant pour lancer l'application juste un sudo docker-compose up -d***
```bash
sudo docker-compose up -d
```
# Test Front-end et Back-end
```bash
sudo docker exec webapp_student_list curl -u toto:python -X GET http://api_pozos:5000/pozos/api/v1.0/get_student_ages
```
![image](https://github.com/user-attachments/assets/13a12c59-2b51-471a-b566-3b41fe192007)
![image](https://github.com/user-attachments/assets/39d760b2-8306-4c3b-8006-6ecd47e5aab7)
Nous avons donc notre application qui continue à fonctionner, avec l'avantage que nous pouvons désormais partager le code avec le client.

# Création du Registry Privé

Création du registry privé qui va abriter les images de l'entreprise.

Voir `docker-compose-registry.yml`.
```bash
sudo docker-compose -f docker-compose-registry.yml up -d
```
![image](https://github.com/user-attachments/assets/569d0e44-c456-47a6-976b-828833d82cc0)

![image](https://github.com/user-attachments/assets/501c3353-899e-4027-850e-078eac481720)

# Pousser sur le Registry Privé
```bash
sudo docker login localhost:5000
sudo docker image tag api-pozos:1 localhost:5000/pozos/api-pozos:1
sudo docker images
sudo docker image push localhost:5000/pozos/api-pozos:1
```
# Verification registry privé
![image](https://github.com/user-attachments/assets/7298ec67-efc9-4e21-91d2-daa9fabba3c0)

# Mise en route de ERP de l'entreprise
```bash
cd odoo
docker-compose up -d
```
## Vérification de la Disponibilité de l'Application
![image](https://github.com/user-attachments/assets/f610ed9e-7821-42cd-9ac7-138c5764b700)

# Pousser l'image odoo sur le Registry Privé de l'entreprise
```bash
sudo docker image tag odoo:10.0 localhost:5000/odoo:10.0
sudo docker images
sudo docker image push localhost:5000/odoo:10.0
```
![image](https://github.com/user-attachments/assets/44ce035a-f2f4-4546-8a5b-f77b997410b1)



