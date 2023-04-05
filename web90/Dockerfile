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
