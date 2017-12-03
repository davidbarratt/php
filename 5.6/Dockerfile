FROM php:5.6-apache

RUN a2enmod env rewrite

# System Dependencies.
RUN apt-get update && apt-get install -y \
			libicu-dev \
		--no-install-recommends && rm -r /var/lib/apt/lists/*

# PHP Extensions
RUN set -ex \
	&& buildDeps=' \
			libsqlite3-dev \
		' \
	&& apt-get update && apt-get install -y --no-install-recommends $buildDeps && rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-install intl opcache pdo_mysql pdo_sqlite \
	&& apt-get purge -y --auto-remove $buildDeps

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=60'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

# Set the max upload size.
RUN { \
		echo 'upload_max_filesize = 32M'; \
		echo 'post_max_size = 32M'; \
} > /usr/local/etc/php/conf.d/upload-filesize.ini

# Optomize container for a low-memory VPS.
RUN { \
    echo 'StartServers 1'; \
		echo 'MinSpareServers 1'; \
	} | tee "$APACHE_CONFDIR/conf-available/low-memory.conf" \
&& a2enconf low-memory

WORKDIR /var/www/html
