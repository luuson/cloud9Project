FROM php:7.4-fpm
RUN apt-get update \
    && apt-get install -y \
       libfreetype6-dev \
       libjpeg62-turbo-dev \
       libpng-dev \
       zlib1g-dev \
       libpq-dev \
       mariadb-client \
       unzip \
       libzip-dev \
       # Laravel Breeze用 node.js
       nodejs \
       npm \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd zip pdo_mysql mysqli \
    && docker-php-ext-enable mysqli

# Redis
RUN git clone https://github.com/phpredis/phpredis.git /usr/src/php/ext/redis
RUN docker-php-ext-install redis

# OPcache
RUN docker-php-ext-install opcache \
    && docker-php-ext-enable opcache

# APCu
RUN pecl install apcu \
    && docker-php-ext-enable apcu \
    # コマンドラインでAPCuを使えるようにする
    && echo "apc.enable_cli=1" >> /usr/local/etc/php/conf.d/docker-php-ext-apcu.ini \
    # APCuの有効化
    && echo "apc.enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-apcu.ini \
    # APCuの共有メモリに1G割り当(デフォルト30MB)
    && echo "apc.shm_size=1G" >> /usr/local/etc/php/conf.d/docker-php-ext-apcu.ini

# php.ini
COPY ./php/php.ini /usr/local/etc/php/php.ini

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /composer
ENV PATH $PATH:/composer/vendor/bin

# Laravel
WORKDIR /var/www/chaneclaire
RUN composer global require "laravel/installer"
