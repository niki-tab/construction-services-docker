FROM php:8.2-apache

# Enable rewrite mode
RUN a2enmod rewrite

# Install necessary packages
RUN apt-get update && \
    apt-get install \
    libzip-dev \
    wget \
    git \
    unzip \
    -y --no-install-recommends \
    libpq-dev

# Install PHP Extensions
RUN docker-php-ext-install zip pdo_mysql pgsql pdo_pgsql 

RUN docker-php-ext-install bcmath

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions gd

RUN pecl install -o -f xdebug-3.2.2 \
    && docker-php-ext-enable xdebug
#     && rm -rf /tmp/pear

# Copy composer installable
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copy php.ini
COPY ./php.ini /usr/local/etc/php/

# Cleanup packages and install composer
RUN apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/* \
    && rm -rf /tmp/* 

# Change the current working directory
WORKDIR /var/www

# Change the owner of the container document root
RUN chown -R www-data:www-data /var/www

RUN echo 'ServerName 127.0.0.1' >> /etc/apache2/apache2.conf
RUN service apache2 restart

# Start Apache in foreground
CMD ["apache2-foreground"]