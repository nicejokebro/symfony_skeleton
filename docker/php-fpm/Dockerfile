FROM php:8.3.0-fpm-alpine

# Install packages
RUN apk add --no-cache bash curl git build-base zlib-dev oniguruma-dev autoconf bash linux-headers libpq-dev icu-dev libxslt-dev nodejs npm
RUN curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.alpine.sh' | bash
RUN apk add symfony-cli

# Xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug;
COPY ./docker/php-fpm/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# PDO_PGSQL
RUN docker-php-ext-configure pgsql -with-pgsql=/var/lib/postgresql
RUN docker-php-ext-install pdo pdo_pgsql pgsql
RUN docker-php-ext-enable pdo pdo_pgsql pgsql

RUN docker-php-ext-install intl xsl

# Configure non-root user.
ARG PUID=1000
ARG PGID=1000
RUN apk --no-cache add shadow && \
    groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

# Source code
COPY ./ /var/www
WORKDIR /var/www

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

CMD php-fpm

EXPOSE 9000