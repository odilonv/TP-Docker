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


## 2. Notion de Volume

Placez vous dans un fichier que vous avez créé, ici “Docker”, et créer un conteneur 
Apache : docker run -dp 89:80 -v /c/users/[Votre nom d’utilisateur]/[Votre fichier créé]:/var/www/html --name web89 php:8.2-apache

Si vous avez besoin de plusieurs points de montage du volume, vous pouvez ajouter plusieurs “-v” dans la commande.


