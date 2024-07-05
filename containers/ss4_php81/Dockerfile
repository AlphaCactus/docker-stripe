ARG PHP_VERSION="8.1"
FROM php:8.1-fpm-buster

# after every FROM statement, all the ARGs get collected and are no longer available. Be careful with multi-stage builds.
ARG PHP_INI="php.ini"
ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS="0"

ADD opcache.ini "$PHP_INI_DIR/conf.d/opcache.ini"

ARG pm_max_children=5
RUN echo "pm.max_children = $pm_max_children" >> /usr/local/etc/php-fpm.d/zz-docker.conf

ADD --chmod=0755 https://github.com/mlocati/docker-php-extension-installer/releases/download/2.2.9/install-php-extensions /usr/local/bin/

RUN install-php-extensions ctype dom fileinfo hash intl \
    mbstring session simplexml tokenizer opcache mysqli \
    gd bcmath zip xml xdebug soap pdo @composer

RUN apt-get update && apt-get install -y gettext-base

ARG fpm_uid=1000
ARG fpm_gid=1000

# Set the container user's UID and GID based on the passed-in build args. This is done
# to allow the container process to read and write files owned by the host machine when running
# the container as a non-root user. If file "permission denied" errors occur while trying
# to run the container, and the files being accessed are mapped into the container using a bind-mount volume
# from the host, it is likely that the UID/GID of the container's process does not match the user that owns those files
# on the host (likely your user). The container will need to rebuilt with the UID and GID as build arguments.
RUN usermod --non-unique --uid $fpm_uid www-data && groupmod --non-unique --gid $fpm_gid www-data


ADD ${PHP_INI} "$PHP_INI_DIR/php.ini"

ARG app_dir=/var/www/html/app
ENV app_dir=$app_dir
WORKDIR $app_dir
USER www-data

# RUN composer install --working-dir=$app_dir && chown -R www-data:www-data $app_dir

ADD env-template /

CMD envsubst < /env-template > .env && php-fpm
