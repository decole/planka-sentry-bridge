FROM php:8.1-fpm

MAINTAINER decole <decole2014@yandex.ru>

# Arguments defined in docker-compose.yml
ARG USER
ARG UID

RUN apt update -q -y \
    && apt upgrade -y \
    && apt install -q -y --no-install-recommends \
    zlib1g-dev \
    curl \
    libpq-dev \
    libzip-dev \
    libpng-dev \
    zip \
    libmcrypt-dev \
    unzip \
    git \
    procps \
    sudo \
    && rm -rf /var/lib/apt/lists/*

RUN apt update -q -y \
    && apt install -q -y --no-install-recommends \
    libzip-dev \
    zip \
    psmisc \
    librabbitmq-dev \
    libssh-dev

RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pgsql pdo_pgsql \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install zip \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && docker-php-ext-install sockets \
    && pecl install amqp \
    && docker-php-ext-enable amqp

COPY docker/php/php-fpm.ini /usr/local/etc/php/php.ini

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN pear install PHP_CodeSniffer

RUN echo -e "\n\n====================== Remove unised ======================\n" \
    && apt autoremove gcc -y \
    && apt clean all \
    && rm -rvf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && dpkg -la | awk '{print $2}' | grep '\-dev' | xargs apt remove -y \
    && rm -rf /tmp/*

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u ${UID} -d /home/${USER} ${USER}  \
    && mkdir -p /home/${USER}  \
    && chown -R ${USER}:${USER} /var/www

WORKDIR /var/www

USER ${USER}