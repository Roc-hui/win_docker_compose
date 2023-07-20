FROM php:7.3.5-fpm

RUN sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list \
   && sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list.d/buster.list

# 更新系统与安装需要的库
RUN apt-get update \
    	&& apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        cmake \
        openssl \
        libcurl4-openssl-dev \
        pkg-config \
        libssl-dev \
        python \
        vim \
        procps \
        cron \
        supervisor \
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
RUN docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include/ --with-freetype=/usr/include/freetype2 \
    && docker-php-ext-install gd \
    && docker-php-ext-install sockets \
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
RUN pecl install swoole-4.4.6 && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini

# yaf扩展安装
RUN pecl install yaf && echo "extension=yaf.so" > /usr/local/etc/php/conf.d/yaf.ini

# amqp扩展安装
WORKDIR  /var
COPY ./rabbitmq-c-0.7.1.tar.gz ./
COPY ./amqp-1.7.1.tgz ./

RUN tar zxf rabbitmq-c-0.7.1.tar.gz \
    && cd rabbitmq-c-0.7.1 \
    && ./configure --prefix=/usr/local/rabbitmq \
    && make && make install

WORKDIR  /var
RUN tar zxf amqp-1.7.1.tgz \
    && cd amqp-1.7.1 \
    && phpize \
    && ./configure --with-amqp --with-librabbitmq-dir=/usr/local/rabbitmq \
    && make && make install \
    && echo "extension=amqp.so" > /usr/local/etc/php/conf.d/amqp.ini

# composer安装
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    	&& composer config -g repo.packagist composer https://packagist.phpcomposer.com
