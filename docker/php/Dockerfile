FROM php:8.2.13RC1-fpm-alpine3.18

# Obtenemos argumento del exterior
ARG UID
EXPOSE $UID

# Creamos usuario y directorio
RUN adduser -u ${UID} --disabled-password --gecos "" appuser
RUN mkdir /home/appuser/.ssh
RUN chown -R appuser:appuser /home/appuser/

# Si en algún momento tenemos una clave privada en el contenedor, no nos pida confirmación (repo privado)
RUN echo "StrictHostKeyChecking no" >> /home/appuser/.ssh/config
RUN echo "export COLUMNS=300" >> /home/appuser/.bashrc
RUN echo "alias sf=/appdata/www/bin/console" >> /home/appuser/.bashrc

# Copiamos nuestra configuración php
COPY ./php.ini /usr/local/etc/php/php.ini

# Instalamos todas las dependencias necesarias
RUN apt/get update \
    && apt-get install -y git acl openssl openssh-client wget zip vim libssh-dev \
    && apt-get install -y libpng-dev zlib1g-dev libzip-dev libxml2-dev libicu-dev \
    && docker-php-ext-install intl pdo pdo_mysql zip gd soap bcmath sockets \
    && pecl install xdebug \
    && docker-php-ext-enable --ini-name 05-opcache.ini opcache xdebug

# Instalamos composer
RUN curl --insecure https://getcomposer.org/composer.phar -o /usr/bin/composer && chmod +x /usr/bin/composer
RUN composer self-update

RUN wget https://cs.symfony.com/download/php-cs-fixer-v2.phar -O php-cs-fixer
RUN chmod a+x php-cs-fixer
RUN mv php-cs-fixer /usr/local/bin/php-cs-fixer

RUN mkdir -p /appdata/www

# Cambiamos el usuario por defecto
USER appuser
RUN composer global require "hirak/prestissimo:^0.3" --prefer-dist --no-progress --no-suggest --no-suggest --classmap-authoritative \
    && composer clear-cache

WORKDIR /appdata/www