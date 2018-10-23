FROM php:7.2-fpm-alpine as symfony_php
LABEL maintainer="Alexandre Jardin <info@ajardin.fr>"

# Install Symfony requirements
RUN \
    apk add --no-cache --virtual .persistent-deps \
        git \
        icu-libs \
        postgresql-libs \
        yarn \
        zlib-dev && \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        icu-dev \
        postgresql-dev && \
    docker-php-ext-install \
        intl \
        opcache \
        pdo_pgsql \
        zip && \
    apk del .build-deps

# Install Composer globally
ENV COMPOSER_ALLOW_SUPERUSER 1
RUN \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    composer self-update --preview

# Install custom entrypoint
COPY entrypoint.sh /usr/local/bin/docker-custom-entrypoint
RUN chmod 777 /usr/local/bin/docker-custom-entrypoint
CMD ["php-fpm"]
ENTRYPOINT ["docker-custom-entrypoint"]

# ======================================================================================================================
FROM symfony_php as symfony_php_blackfire
RUN \
    curl -sS https://packages.blackfire.io/binaries/blackfire-php/1.23.1/blackfire-php-alpine_amd64-php-72.so \
        --output $(php -r "echo ini_get('extension_dir');")/blackfire.so && \
    docker-php-ext-enable blackfire
# ======================================================================================================================

# ======================================================================================================================
FROM symfony_php as symfony_php_xdebug
RUN \
    apk add --no-cache --virtual .build-deps $PHPIZE_DEPS && \
    yes "" | pecl install xdebug && \
    docker-php-ext-enable xdebug
# ======================================================================================================================
