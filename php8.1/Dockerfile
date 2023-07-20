FROM php:8.1.0-fpm

# 更新系统与安装需要的库
RUN apt-get update \
        && apt-get install -y \
        zlib1g-dev \
        libpng-dev \
        libwebp-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libcurl4-openssl-dev \
        pkg-config \
        libssl-dev \
        vim \
        procps \
        cron \
        supervisor \
        openssh-server \
        wget \
        graphviz

# 安装libzip依赖库
WORKDIR  /var
COPY ./libzip-1.2.0.tar.gz ./
RUN tar -zxvf libzip-1.2.0.tar.gz \
    && cd libzip-1.2.0 \
    && ./configure \
    && make && make install \
    && cd ../ && rm -Rf libzip-1.2.0 libzip-1.2.0.tar.gz

# 编译安装php常用的类库
RUN docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include \
    && docker-php-ext-install gd \
    && docker-php-ext-install  sockets \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install sysvsem \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install opcache \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install zip

# redis扩展安装
RUN pecl install redis && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini

# mongodb扩展安装
RUN pecl install mongodb && echo "extension=mongodb.so" > /usr/local/etc/php/conf.d/mongodb.ini

# swoole扩展安装
RUN pecl install swoole-5.0.2 && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini

# yaf扩展安装
RUN pecl install yaf && echo "extension=yaf.so" > /usr/local/etc/php/conf.d/yaf.ini

# composer安装
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    	&& composer config -g repo.packagist composer https://packagist.phpcomposer.com
