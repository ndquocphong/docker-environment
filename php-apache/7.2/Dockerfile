FROM php:7.2-apache

# update latest repository 
RUN apt-get update -y && apt install -y apt-utils

# common
RUN apt-get install -y sudo zip unzip curl zlib1g-dev g++ libcurl4-gnutls-dev libpq-dev git vim mysql-client rsync sshpass bzip2 msmtp

# ssh2
RUN apt-get install -y libssh2-1-dev libssh2-1
RUN pecl install ssh2-1.1.2
RUN echo extension=ssh2.so >> /usr/local/etc/php/conf.d/ssh2.ini

# memcached extension
RUN apt-get install -y libz-dev libmemcached-dev
RUN pecl install memcached-3.0.3
RUN echo extension=memcached.so >> /usr/local/etc/php/conf.d/memcached.ini

# gd extension
RUN apt-get install -y \
  libpng-dev \
  libfreetype6-dev \
  libjpeg-dev \
  libxpm-dev \
  libxml2-dev \
  libxslt-dev \
  libmcrypt-dev \
  libwebp-dev  # php >=7.0 (use libvpx for php <7.0)
RUN docker-php-ext-configure gd \
    --with-freetype-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ \
    --with-xpm-dir=/usr/include \
    --with-webp-dir=/usr/include/ # php >=7.0 (use libvpx for php <7.0)
RUN docker-php-ext-install gd

# intl extension
RUN apt-get install -y libicu-dev
RUN docker-php-ext-install intl

# ldap extension
RUN apt-get install libldap2-dev -y && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap

# pcntl extension
RUN docker-php-ext-configure pcntl --enable-pcntl \
    && docker-php-ext-install pcntl    

# redis extension
RUN pecl install redis 
RUN docker-php-ext-enable redis         

# xdebug extension
RUN pecl install redis xdebug 
RUN docker-php-ext-enable redis xdebug   

# common extension
RUN docker-php-ext-install -j$(nproc) iconv intl bcmath mbstring curl xsl soap mysqli pdo pdo_mysql pdo_pgsql opcache zip calendar


RUN touch /usr/local/etc/php/conf.d/custom.ini

# environment variable 
ENV PHP_DATE_TIMEZONE="" \
    PHP_LOG_ERRORS_MAX_LEN=1024 \
    PHP_LOG_ERRORS="" \
    PHP_MAX_EXECUTION_TIME=0 \
    PHP_MAX_FILE_UPLOADS=20 \
    PHP_MAX_INPUT_VARS=1000 \
    PHP_MEMORY_LIMIT=128M \
    PHP_POST_MAX_SIZE=8M \
    PHP_SENDMAIL_PATH="/usr/sbin/sendmail -t -i" \      
    PHP_SESSION_SAVE_HANDLER=files \
    PHP_SESSION_SAVE_PATH="" \
    PHP_UPLOAD_MAX_FILESIZE=2M \
    PHP_XDEBUG_DEFAULT_ENABLE=0 \
    PHP_XDEBUG_IDEKEY=''\
    PHP_XDEBUG_PROFILER_ENABLE=0 \
    PHP_XDEBUG_REMOTE_AUTOSTART=0 \
    PHP_XDEBUG_REMOTE_CONNECT_BACK=0 \
    PHP_XDEBUG_REMOTE_ENABLE=0 \
    PHP_XDEBUG_REMOTE_HOST=0

# composer 
RUN curl -sS https://getcomposer.org/installer | php -- --filename=composer --install-dir=/usr/local/bin

RUN chown -R www-data: /var/www
USER www-data
RUN composer global require --optimize-autoloader "hirak/prestissimo"

USER root    
RUN echo "ServerName localhost" | tee /etc/apache2/conf-available/fqdn.conf
RUN mkdir -p /etc/apache2/vhosts
RUN echo "IncludeOptional vhosts/*.conf" >> /etc/apache2/apache2.conf

RUN a2enconf fqdn

RUN a2enmod rewrite

RUN a2enmod deflate 

RUN service apache2 restart
