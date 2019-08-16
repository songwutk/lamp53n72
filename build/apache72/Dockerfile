FROM php:7.2.14-apache

MAINTAINER kuakling@gmail.com

ENV PATH $PATH:/root/.composer/vendor/bin

# PHP extensions come first, as they are less likely to change between Yii releases
RUN apt-get update \
    && apt-get -y install \
            git-core \
            g++ \
            libicu-dev \
            libmcrypt-dev \
            zlib1g-dev \
            libmagickwand-dev \
            libpq-dev \
            libmemcached-dev libmemcached11 \
#            libmemcached-dev \
            libmcrypt-dev \
        --no-install-recommends \
#    && rm -rf /var/lib/apt/lists/* \
    && a2enmod rewrite
    # Enable mod_rewrite

    # Install PHP extensions
RUN docker-php-ext-install intl \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install pdo mysqli \
    && docker-php-ext-install pdo pdo_pgsql \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install zip \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && docker-php-ext-configure gd --with-freetype-dir=/usr --with-jpeg-dir=/usr  \
    && docker-php-ext-install gd \
    && pecl install memcached-3.0.4 \ 
    && docker-php-ext-enable memcached \
    && docker-php-ext-install -j5 mbstring mysqli pdo pdo_mysql shmop \
    && pecl install apcu redis-3.1.3 && echo extension=apcu.so > /usr/local/etc/php/conf.d/apcu.ini \
    && docker-php-ext-enable apcu redis \
    && apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/* \
    # Fix write permissions with shared folders
    && usermod -u 1000 www-data
#    && docker-php-ext-install mcrypt \
    



# Copy php config
COPY conf/php.ini /usr/local/etc/php/conf.d/docker-php-ext-upd.ini


# Next composer and global composer package, as their versions may change from time to time
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && composer global require --no-progress "fxp/composer-asset-plugin:~1.2.0" \
    && composer global require --no-progress "codeception/codeception=2.0.*" \
    && composer global require --no-progress "codeception/specify=*" \
    && composer global require --no-progress "codeception/verify=*"


# Composer shell and run it
COPY composer.sh /usr/local/bin/composer.sh
RUN composer.sh && rm /usr/local/bin/composer.sh

WORKDIR /var/www/html
