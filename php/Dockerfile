FROM php:fpm-bookworm

COPY php.ini /usr/local/etc/php/php.ini

RUN apt-get update && \
    apt-get install -y \
        unzip \
        zlib1g-dev \
        libzstd-dev \
        libfreetype6-dev \
		libicu-dev \
		libjpeg-dev \
		libmagickwand-dev \
		libpng-dev \
		libwebp-dev \
		libzip-dev \
        libmemcached-dev \
        libpq-dev \
		libxslt-dev \
                libxml2-dev \
    && docker-php-ext-configure gd \
	    --with-freetype \
	    --with-jpeg \
	    --with-webp \
    && pecl install \
         --configureoptions 'enable-redis-igbinary="yes" enable-redis-lzf="yes" enable-redis-zstd="yes"' \
            igbinary \
            redis \
            memcached \
            imagick \
            apcu \
            zstd \
            mongodb \
    && docker-php-ext-enable \
        igbinary \
        apcu redis imagick memcached mongodb \
    && docker-php-ext-install -j "$(nproc)" bcmath gettext intl opcache gd pdo pdo_mysql pdo_pgsql pgsql shmop soap sockets sysvsem fileinfo exif xsl zip mysqli
	
RUN \
    { \
    echo 'post_max_size = 100M;';\
    echo 'upload_max_filesize = 100M;';\
    echo 'max_execution_time = 600S;';\
    } > /usr/local/etc/php/conf.d/docker-php-upload.ini; \
    \
    { \
    echo 'opcache.enable=1'; \
    echo 'opcache.interned_strings_buffer=8'; \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.max_accelerated_files=10000'; \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.save_comments=1'; \
    echo 'opcache.revalidate_freq=2'; \
    echo 'opcache.jit=1235'; \
    echo 'opcache.jit_buffer_size=128M'; \
    } > /usr/local/etc/php/conf.d/opcache-recommended.ini; \
    \
    #echo 'apc.enable_cli=1' >> /usr/local/etc/php/conf.d/docker-php-ext-apcu.ini; \
    \
    echo 'memory_limit=512M' > /usr/local/etc/php/conf.d/memory-limit.ini;

RUN { \
	echo 'error_reporting = E_ERROR | E_WARNING | E_PARSE | E_CORE_ERROR | E_CORE_WARNING | E_COMPILE_ERROR | E_COMPILE_WARNING | E_RECOVERABLE_ERROR'; \
	echo 'display_errors = Off'; \
	echo 'display_startup_errors = Off'; \
	echo 'log_errors = On'; \
	echo 'error_log = /dev/stderr'; \
	echo 'log_errors_max_len = 1024'; \
	echo 'ignore_repeated_errors = On'; \
	echo 'ignore_repeated_source = Off'; \
	echo 'html_errors = Off'; \
	} > /usr/local/etc/php/conf.d/error-logging.ini

#VOLUME [ "/usr/local/etc/php/php.ini","/var/www" ]
RUN apt-get autoremove -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN rm -rf /var/www/html
WORKDIR /var/www

RUN chown -R www-data:www-data /var/www
USER www-data

CMD [ "php-fpm" ]

