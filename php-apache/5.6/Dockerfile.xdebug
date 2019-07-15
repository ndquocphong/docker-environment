FROM php:5.6-apache

RUN apt-get update -y && apt install -y apt-utils

RUN apt-get install -y zip unzip curl libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng-dev zlib1g-dev libicu-dev g++ libcurl4-gnutls-dev libxml2-dev libpq-dev

RUN docker-php-ext-install -j$(nproc) iconv mcrypt gd intl bcmath mbstring curl soap mysqli pdo pdo_mysql pdo_pgsql opcache zip
RUN pecl install redis  
RUN docker-php-ext-enable redis

RUN touch /usr/local/etc/php/conf.d/custom.ini

RUN echo "ServerName localhost" | tee /etc/apache2/conf-available/fqdn.conf
RUN mkdir -p /etc/apache2/vhosts
RUN echo "IncludeOptional vhosts/*.conf" >> /etc/apache2/apache2.conf

RUN a2enconf fqdn

RUN a2enmod rewrite

RUN a2enmod deflate 

RUN service apache2 restart