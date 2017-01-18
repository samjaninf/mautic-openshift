#FROM php:7.0-fpm
FROM php:7.0-apache

ENV DEBIAN_FRONTEND noninteractive

RUN echo "force-unsafe-io" > /etc/dpkg/dpkg.cfg.d/02apt-speedup

RUN echo "Acquire::http {No-Cache=True;};" > /etc/apt/apt.conf.d/no-cache

RUN apt-get update && apt-get install -y \
        g++ \
        libicu-dev \
        libc-client-dev \
        libkrb5-dev \
        libssl-dev \
        unzip \
        zip \
        libfreetype6-dev \
        libjpeg-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        zlib1g-dev \
        mysql-client \
        openssh-client \
        libxml2-dev \
        nano \
        yui-compressor \
        git \
        mercurial \
        curl \
        libxslt1-dev \
        libcurl4-gnutls-dev \
        --no-install-recommends && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN docker-php-ext-configure imap --with-imap --with-imap-ssl --with-kerberos
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/ && \
    docker-php-ext-configure bcmath && \
    docker-php-ext-install -j$(nproc) gd \
                                    intl \
                                    xsl \
                                    json \
                                    zip \
                                    bcmath \
                                    soap \
                                    mcrypt \
                                    mysqli \
                                    pdo_mysql \
                                    imap

COPY ./php.ini /usr/local/etc/php/
COPY 000-default.conf /etc/apache2/sites-available/

ENV MAUTIC_VERSION 2.5.1
ENV MAUTIC_SHA1 f0f89343f9ce67b6b4cafb44fd7b15f325ed726f

RUN curl -o mautic.zip -SL https://s3.amazonaws.com/mautic/releases/${MAUTIC_VERSION}.zip \
    && unzip mautic.zip -d /var/www/html/ \
    && rm mautic.zip \
    && chown -R www-data:www-data /var/www/html/*

# Copy init scripts and custom .htaccess
#COPY docker-entrypoint.sh /entrypoint.sh
#COPY makeconfig.php /makeconfig.php
#COPY makedb.php /makedb.php
#RUN chmod 700 /entrypoint.sh
#ENTRYPOINT ["/entrypoint.sh"]
CMD ["apache2-foreground"]