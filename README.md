# Installation de Docker sous windows


 ## Table des matières 
 - [Première installation](#id-premiereInstallation) 
 - [Configuration & Concepts utilisés](#id-section2) 
   1. [Apache](#id-section2)
   2. [Nginx](#id-section2)
   3. [PostgreSQL avec extension POSTGIS](#id-section2)
   4. [Pgadmin](#id-section2)
 - [Mise en place du docker sur la SAE : Explore](#id-section2)


 ### Première installation
 
 https://www.docker.com/
 ![image](https://user-images.githubusercontent.com/120033089/228777236-4a0c1e44-8e10-4cd1-a994-9f270cbd5a6a.png)

 
 
 
 ### Section 2 
<a name="_vgipqpe7nu2q"></a>Installation d’un site web PHP sous docker


Table des matières

` `- [Première installation](#id-premiereInstallation)

` `- [Configuration & Concepts utilisés](#id-section2)

`   `1. [Apache](#id-section2)

`   `2. [Nginx](#id-section2)

`   `3. [PostgreSQL avec extension POSTGIS](#id-section2)

`   `4. [Pgadmin](#id-section2)

` `- [Mise en place du docker sur la SAE : Explore](#id-section2)

## <a name="_gbjestx8re05"></a>**https://www.section.io/engineering-education/dockerized-php-apache-and-mysql-container-development-environment/**
## <a name="_hsh64ytjwk4b"></a>**Première installation**


Tout au long de ce document, nous allons vous guider dans la création et la configuration de conteneurs avec **Docker**.

1. Se rendre sur le site web de coker et télécharger les fichiers d’installation : 

<https://www.docker.com/products/docker-desktop/>

![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.001.png)

1. Procéder à l’installation

## <a name="_9zteps7oae1l"></a>**![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.002.png)**
## <a name="_n3xrlocabe6v"></a>**Configurations & Concepts utilisés**

Au niveau des différentes configurations, nous allons installer les conteneurs suivants : **Apache**, **Nginx**, Serveur de base de données **PostgreSQL** ainsi qu’un **Pgadmin**. 

Cependant pour installer ceux-ci nous allons mettre en application les notions : **compose**, **network**, **volume** et **build**.


### <a name="_qgxzmqp2lkz5"></a>**I/ Notion de Network :**

1. **Bridge** Network

Créer un conteneur **nginx** en mode “bridge” :

![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.003.png)

Vous pouvez aussi utiliser : **docker run -dp 81:80 nginx** car le mode bridge est le mode de réseau par défaut de Docker.

**Inspecter** que l’installation a bien été réalisée :

![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.004.png)

Vous pouvez noter que @ip interne **172.17.0.2** pour communiquer avec les autres conteneurs.

![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.005.png)


1. **None**

Avec “none” les conteneurs ne possèdent pas d’interface réseau, ils ne peuvent pas communiquer avec d’autres conteneurs ou avec l’hôte

Créer un conteneur **nginx** en mode “none”:

![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.006.png)


1. **Network personnalisé**

Cette notion sera évoquée dans le dépôt GitHub suivant : **https://github.com/odilonv/Docker-SAE**
### <a name="_e7gmgwjmalgv"></a>**II/ Notion de Volume**

Placez vous dans un fichier que vous avez créé, ici “Docker”, et créer un conteneur 

**Apache** : **docker run -dp 89:80 -v /c/users/[Votre nom d’utilisateur]/[Votre fichier créé]:/var/www/html --name web89 php:8.2-apache**

Si vous avez besoin de plusieurs points de montage du **volume**, vous pouvez ajouter plusieurs “-v” dans la commande.

![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.007.png)

Aller sur localhost:89, vous ne verrez rien, c’est normal pas de panique.

Créez dans votre dossier local docker, un fichier **index.php** avec <?php echo phpinfo();

Retourner sur localhost:89, vous pourrez voir que tous les fichiers sont synchronisés en direct.

![](Aspose.Words.c9c03f61-5f3a-4da0-ba43-a42407e10dd5.008.png)


### <a name="_5ozjomr7t8yq"></a>**III/ Notion de Compose**

● version à utiliser : 3.8 Compose file versions and upgrading (docker.com)

● fichier docker-compose.yml à stocker dans votre dossier docker

Creer un fichier compose “docker-compose.yml”

Pour chaque images de votre pile, créer un service dans votre fichier compose et renseigner leurs informations : 

version: ‘3.8’

services:

web89:

image :  php:8.2-apache

container\_name: web89

volumes:

- ./web89/html:/var/www/html
- ./web89/apache/sites\_enabled:/etc/apache2/sites\_enabled
- ./web89/php/custom-php.ini:/use/local/etc/conf.d/custom-php.ini

`	`ports:

`		`-89:90

mysql:

`	`image: mysql:5.7

`	`container\_name: mysql1

`	`environment:

`		`MYSQL\_DATABASE: sae

`		`MYSQL\_USER: sae

`		`MYSQL\_PASSWORD: totoLolo

`		`MYSQL\_ROOT\_PASSWORD: totoLolo

`		`UPLOAD\_LIMIT: 20M

`	`ports:

- 3306:3306

`	`volumes:

- ./mysql1/mysql:/var/lib/mysql
- ./mysql1/db/custom.cnf:/etc/mysql/conf.d/custom.cnf

phpmyadmin:

image: phpmyadmin/phpmyadmin

container\_name: pma1

depends\_on:

- mysql

ports:

- 83:80

`	`environment:

- PMA\_HOST=mysql

web90:

`	`build: web90 # dossier contenant le fichier Dockerfile

`	`container\_name: web90

`	`volumes:

- ./web90/html:/var/www/html
- ./web90/apache/sites\_enabled:/etc/apache2/sites\_enabled
- ./web90/php/custom-php.ini:/use/local/etc/php/conf.d/custom-php.ini

ports:

- 90:80



exécuter
#### <a name="_x1g3u480bc7p"></a>docker compose up -d

### <a name="_vyjdppkdnp9h"></a>**IV/ Notion de build**
Dans docker, créez un dossier web90 , créez ensuite dans ce dossier un fichier Dockerfile

FROM php:8.2-apache 

RUN curl -OL [https://github.com/composer/composer/releases/download/2.5.4/composer.p](https://github.com/composer/composer/releases/download/2.5.4/composer.ph-)har

RUN my composer.phar /usr/local/bin/composer 

RUN chmod +x /usr/local/bin/composer && a2enmod rewrite && service apache2 restart RUN apt-get update && apt-get install -y nano 

RUN apt-get install -y git 

\# renseignez votre mail 

RUN git config --global user.email "you@example.com" 

\# renseignez votre nom de user 

RUN git config --global user.name "you"

RUN apt-get install -y wget  

RUN docker-php-ext-install pdo\_mysql 

RUN apt-get install -y zip


● La commande docker compose build va dans un premier temps télécharger et

préparer l’image d’un conteneur décrit dans le fichier web90/Dockerfile

● la commande docker compose up -d va lancer

○ web89 : image php/8.2 -apache

○ web90 : image php/8.2 -apache

■ avec composer, mod\_rewrite (réécriture d’url),

■ update du système, nano, git, wget,pdo\_mysql, etc.

○ mysql5.7

○ phpmyadmin

● Vous pourrez atteindre la racine des serveurs web en travaillant dans les dossiers

web89 et web90, y assurer les sauvegardes / éditions de code

### <a name="_niba9i5i7yt2"></a>**V/ Versionner le tout**
● Vous pouvez versionner votre dossier de virtualisation avec le fichier docker -

compose.yml, le dossier web90 + Dockerfile

● Ensuite vous publiez le tout sur github, gitlab

● Il suffira de clôner votre dépôt sur une machine possédant la pile docker, de faire un

docker compose build, docker up -d et le tour est joué, vos services sont up !
###


### <a name="_rt119po9byvo"></a><a name="_hkjfp7lmdij5"></a>**Serveur de base de données**


Utilisez la commande suivante pour télécharger l'image Docker de PostgreSQL avec l'extension POSTSIG :
#### <a name="_o3z46itoofy7"></a>docker pull crisidev/postgresql-postgis

créer un conteneur Postgres avec la commande suivante : 
#### <a name="_khwesjbok1y"></a>docker run --name nom\_du\_conteneur -e POSTGRES\_USER=tp1 -e POSTGRES\_PASSWORD=tp12023 -p 8181:5432 -d crisidev/postgresql-postgis

Cette commande crée un conteneur nommé "nom\_du\_conteneur", utilise le nom d'utilisateur "tp1" et le mot de passe "tp12023" pour se connecter à la base de données, mappe le port 8181 de l'hôte vers le port 5432 du conteneur (le port par défaut de PostgreSQL), et démarre le conteneur en mode détaché.

Utilisez la commande suivante pour accéder au shell Bash du conteneur :
#### <a name="_6ra1tjx9a776"></a>docker exec -it nom\_du\_conteneur bash

Utilisez la commande suivante pour vous connecter à la base de données en tant qu'utilisateur "tp1" :

psql -U tp1

Utilisez la commande suivante pour créer une nouvelle base de données portant votre nom :

CREATE DATABASE nom\_de\_la\_bdd;

8. Utilisez la commande suivante pour accorder des privilèges d'administrateur à l'utilisateur "tp1" pour la nouvelle base de données :
#### <a name="_d7ef9nqyfrsn"></a>GRANT ALL PRIVILEGES ON DATABASE nom\_de\_la\_bdd TO tp1;

Vous devriez maintenant avoir un conteneur Docker PostgreSQL avec POSTSIG en cours d'exécution sur le port 8181, avec une nouvelle base de données portant votre nom et l'utilisateur "tp1" ayant les privilèges d'administrateur sur cette base de données.


### <a name="_s12v0o6nl2iy"></a>**PgAdmin**
Utilisez la commande suivante pour télécharger l'image Docker de pgAdmin :

docker pull dpage/pgadmin4

Utilisez la commande suivante pour démarrer un conteneur Docker avec pgAdmin :

docker run --name nom\_du\_conteneur -p 8182:80 -e "PGADMIN\_DEFAULT\_EMAIL=mon\_email" -e "PGADMIN\_DEFAULT\_PASSWORD=mon\_mot\_de\_passe" -d dpage/pgadmin4

Cette commande crée un conteneur nommé "nom\_du\_conteneur", mappe le port 8182 de l'hôte vers le port 80 du conteneur (le port par défaut de pgAdmin), définit l'adresse e-mail et le mot de passe par défaut pour le compte administrateur de pgAdmin, et démarre le conteneur en mode détaché.

Accédez à pgAdmin en ouvrant un navigateur web et en entrant l'adresse suivante :

http://localhost:8182

Connectez-vous à pgAdmin en utilisant les informations d'identification par défaut que vous avez définies dans la commande précédente.

Cliquez sur "Add New Server" (Ajouter un nouveau serveur) pour ajouter un serveur PostgreSQL.

Dans l'onglet "General" (Général), entrez un nom pour le serveur.

Dans l'onglet "Connection" (Connexion), entrez les informations de connexion suivantes pour se connecter à la base de données PostgreSQL créée précédemment :

- Host: localhost
- Port: 8181 (le port que vous avez défini lors de la création du conteneur PostgreSQL)
- Maintenance database: le nom de la base de données créée précédemment
- Username: tp1
- Password: tp12023

Cliquez sur "Save" (Enregistrer) pour enregistrer les informations de connexion.

Vous pouvez maintenant accéder à la base de données PostgreSQL créée précédemment à partir de pgAdmin.

Vous devriez maintenant avoir un conteneur Docker pgAdmin en cours d'exécution sur le port 8182, avec la possibilité d'atteindre la base de données créée précédemment avec les identifiants tp1/tp12023.




1. Apache

sur le port 8180

serveur de bdd postgresql sur le port 8181 avec extension POSTSIG

pgadmin sur le port 8182





DEPEND ON ET NETWORK
