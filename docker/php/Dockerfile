FROM php:7.2-fpm-alpine

ARG INSTALL_XDEBUG=false

RUN apk add --update vim bash

RUN apk add --update ${PHPIZE_DEPS} && \
    #
    # GD:
    #
    apk add --update freetype-dev libjpeg-turbo-dev libpng-dev libwebp-dev libxpm-dev gd-dev \
    freetype libjpeg-turbo libpng libwebp libxpm gd && \
    docker-php-ext-configure gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
        --with-xpm-dir=/usr/include/ \
        --with-webp-dir=/usr/include/ \
        --with-gd=/usr/include && \
    docker-php-ext-install gd && \
    #
    # Imagick:
    #
    apk add --update imagemagick imagemagick-dev && \
    pecl install imagick && \
    docker-php-ext-enable imagick && \
    #
    # MySQL:
    #
    apk --update add mysql-client && \
    docker-php-ext-install mysqli pdo_mysql && \
    #
    # Intl:
    #
    apk add --update zlib-dev icu-dev g++ && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl && \
    #
    # Xsl:
    #
    apk add --update libxslt-dev && \
    docker-php-ext-install xsl && \
    #
    # Zip:
    #
    apk add --update libzip-dev zip unzip && \
    docker-php-ext-configure zip --with-libzip && \
    docker-php-ext-install zip && \
    #
    # BCMath:
    # SOAP:
    #
    docker-php-ext-install bcmath soap && \
    #
    # MCrypt:
    #
    apk add --update libmcrypt-dev && \
    pecl install mcrypt-1.0.2 && \
    docker-php-ext-enable mcrypt && \
    #
    # OpCache:
    #
    docker-php-ext-enable opcache && \
    #
    # Clean up:
    #
    apk del --purge ${PHPIZE_DEPS}

#
# xDebug:
#
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
        apk add --update ${PHPIZE_DEPS} && \
        pecl install xdebug && \
        docker-php-ext-enable xdebug && \
        apk del --purge ${PHPIZE_DEPS} \
    ;fi

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php --install-dir=/usr/bin --filename=composer && \
    php -r "unlink('composer-setup.php');"

RUN touch /var/log/php_error.log && \
    chown www-data:www-data /var/log/php_error.log

RUN mkdir /var/www/app && \
    chown www-data:www-data /var/www/app
WORKDIR /var/www/app

COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
COPY errors.ini /usr/local/etc/php/conf.d/php-error.ini
COPY datetime.ini /usr/local/etc/php/conf.d/php-datetime.ini
COPY resources.ini /usr/local/etc/php/conf.d/php-resources.ini
