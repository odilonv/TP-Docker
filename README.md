# Installation de Docker sous windows

 ## Table des matières 
 - [Première installation](#id-premiereInstallation) 
 - [Configuration & Concepts utilisés](#id-section2) 
   1. [Apache](#id-section2)
   2. [Nginx](#id-section2)
   3. [PostgreSQL avec extension POSTGIS](#id-section2)
   4. [Pgadmin](#id-section2)
 - [Mise en place du docker sur la SAE : Explore](#id-section2)

<br>

## Première installation
 
 Tout au long de ce document, nous allons vous guider dans la création et la configuration de conteneurs avec Docker.
 
Se rendre sur le site web de docker et télécharger les fichiers d’installation : 
https://www.docker.com/
<br><br>
![image](https://user-images.githubusercontent.com/120033089/228777236-4a0c1e44-8e10-4cd1-a994-9f270cbd5a6a.png)

Procéder à l’installation :
https://www.docker.com/products/docker-desktop/



<br>

 
# Configurations & Concepts utilisés

Au niveau des différentes configurations, nous allons installer les conteneurs suivants : Apache, Nginx, Serveur de base de données PostgreSQL ainsi qu’un Pgadmin. 
Cependant pour installer ceux-ci nous allons mettre en application les notions : compose, network, volume et build.

<br>

## 1. Notion de Network :


### I/Bridge

Créer un conteneur nginx en mode “bridge” :
<code>docker run -dp 80:80 -- network bridge nginx</code>
Vous pouvez aussi utiliser : docker run -dp 81:80 nginx car le mode bridge est le mode de réseau par défaut de Docker.

Inspecter que l’installation a bien été réalisée :
<code>docker network inspect bridge</code>

Vous pouvez noter que @ip interne 172.x.x.x pour communiquer avec les autres conteneurs.

<br>

### II/None

Avec “none” les conteneurs ne possèdent pas d’interface réseau, ils ne peuvent pas communiquer avec d’autres conteneurs ou avec l’hôte

Créer un conteneur nginx en mode “none”:
<code>docker run -dp 80:80 --network bridge nginx</code>

<br>

### III/Network personnalisé

Cette notion sera évoquée dans le dépôt GitHub suivant : https://github.com/odilonv/Docker-SAE

<br>

## 2. Notion de Volume

Placez vous dans un fichier que vous avez créé, ici “Docker”, et créer un conteneur 
Apache : 

<code>docker run -dp 89:80 -v /c/users/[Votre nom d’utilisateur]/[Votre fichier créé]:/var/www/html --name web89 php:8.2-apache</code>

Si vous avez besoin de plusieurs points de montage du volume, vous pouvez ajouter plusieurs <code>-v</code> dans la commande.


Aller sur localhost:89, vous ne verrez rien, c’est normal pas de panique.

Créez dans votre dossier local docker, un fichier index.php avec <code><?php echo phpinfo();</code>

Retourner sur localhost:89, vous pourrez voir que tous les fichiers sont synchronisés en direct.



## 3. Notion de Compose

● Version à utiliser : 3.8 Compose file versions and upgrading (docker.com).
● Fichier docker-compose.yml à stocker dans votre dossier docker.

- Creer un fichier compose “docker-compose.yml” :
Pour chaque images de votre pile, créer un service dans votre fichier compose et renseigner leurs informations : 

<pre><code>
version: "3.8"

services:
  web89 :
    image: php:8.2-apache
    container_name: web89
    volumes:
      - ./web89/html:/var/www/html
      - ./web89/apache/sites_enabled:/etc/apache2/sites_enabled
      - ./web89/php/custom-php.ini:/use/local/etc/conf.d/custom-php.ini
    ports:
      - 89:80
    links:
      - mysql:mysql

  mysql:
    image: mysql:5.7
    container_name: mysql1
    environment:
      MYSQL_DATABASE: sae
      MYSQL_USER: sae
      MYSQL_PASSWORD: totoLolo
      MYSQL_ROOT_PASSWORD: totoLolo
      UPLOAD_LIMIT: 20M
    ports:
      - 3306:3306
    volumes:
      - ./mysql1/mysql:/var/lib/mysql
      - ./mysql1/db/custom.cnf:/etc/mysql/conf.d/custom.cnf

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: pma1
    depends_on:
      - mysql
    ports:
      - 83:80
    environment:
      - PMA_HOST=mysql

  web90:
    build: web90 # dossier contenant le fichier Dockerfile
    container_name: web90
    volumes:
      - ./web90/html:/var/www/html
      - ./web90/apache/sites_enabled:/etc/apache2/sites_enabled
      - ./web90/php/custom-php.ini:/use/local/etc/php/conf.d/custom-php.ini
    ports:
      - 90:80
    links:
      - mysql:mysql

</code></pre>

Puis exécuter :
<code>docker compose up -d</code>

<br>

## 4. Notion de build

Dans docker, créez un dossier web90, créez ensuite dans ce dossier un fichier <code>Dockerfile</code>.

Voici un exemple :
<pre><code>
FROM php:8.2-apache

# Installe Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Active le module de réécriture Apache2 et redémarre Apache2
RUN a2enmod rewrite && service apache2 restart

# Installe l'extension pdo_mysql pour PHP
RUN docker-php-ext-install pdo_mysql

# Définit le répertoire de travail par défaut
WORKDIR /var/www/html

RUN apt-get update && apt-get install -y nano 
RUN apt-get install -y git 
# renseignez votre mail 
RUN git config --global user.email "odilon.vidal@icloud.com" 

# renseignez votre nom de user 
RUN git config --global user.name "odilonv"
RUN apt-get install -y wget 
RUN docker-php-ext-install pdo_mysql 
RUN apt-get install -y zip

</pre></code>

La commande docker compose build va dans un premier temps télécharger et préparer l’image d’un conteneur décrit dans le fichier <code>web90/Dockerfile</code>

La commande <code>docker compose up -d</code> va lancer :
○ web89 : image php/8.2 -apache
○ web90 : image php/8.2 -apache
■ avec composer, mod_rewrite (réécriture d’url),
■ update du système, nano, git, wget,pdo_mysql, etc.
○ mysql5.7
○ phpmyadmin
● Vous pourrez atteindre la racine des serveurs web en travaillant dans les dossiers
web89 et web90, y assurer les sauvegardes / éditions de code

<br>

## 5. Versionner le tout

Vous pouvez versionner votre dossier de virtualisation avec le fichier docker <code>compose.yml</code>, le dossier web90 + <code>Dockerfile</code>.




