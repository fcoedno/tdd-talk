ARG BUILD_TYPE="cli"

FROM arquivei/php:8.1-${BUILD_TYPE}-debian as base
WORKDIR /application
COPY docker/php-ini-overrides.ini /usr/local/etc/php/conf.d/99-overrides.ini
COPY docker/php-fpm.conf /usr/local/etc/php-fpm.d/docker.conf

FROM base as dev
RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && apt-get update --allow-releaseinfo-change && apt-get install -y redis-tools
RUN pecl install xdebug && docker-php-ext-enable xdebug

FROM dev AS builder
ARG COMPOSER_AUTH
WORKDIR /application
COPY composer.json composer.lock ./
RUN composer install -o --no-dev --no-scripts

FROM base as prod
COPY . /application
COPY --from=builder /application/vendor /application/vendor
