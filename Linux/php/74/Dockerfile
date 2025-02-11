FROM php:7.4-fpm
LABEL MAINTAINER="1016681491@qq.com"
# 语言和时间
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

## 阿里云
RUN sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list

### 更新缓存
RUN apt-get clean && apt-get update

### 安装更新软件
RUN apt-get install -y procps build-essential zlib1g-dev unzip bzip2  \
    libbz2-dev libxml2 libxml2-dev libldap2-dev apt-utils libjpeg-dev libfreetype6-dev \
    libpng-dev gcc vim libzip-dev  libssl-dev pkg-config libcurl4-openssl-dev

### 初始化 /usr/src/php
RUN docker-php-source extract

### docker-php-ext 扩展php安装
RUN docker-php-ext-install pcntl pdo pdo_mysql gd zip sockets

# 安装扩展 psr
RUN pecl install psr  && docker-php-ext-enable psr

# 安装扩展 mongodb
RUN pecl install mongodb && docker-php-ext-enable mongodb

# 安装扩展 redis
RUN pecl install redis && docker-php-ext-enable redis

# 安装扩展 swoole
RUN pecl install swoole-4.6.6 && docker-php-ext-enable swoole \
### 按需 开启  swoole.use_shortname = 'Off'
&& echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini

# 安装 rabbitmq，amqp
RUN apt-get install -y librabbitmq-dev && pecl install amqp && docker-php-ext-enable amqp

### 更改composer 镜像
RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

########################################################################################################################
###  追加扩展
#RUN echo "extension=mongodb.so" > /usr/local/etc/php/conf.d/docker-php-ext-mongodb.ini


### 清除 安装包
RUN rm -rf /tmp/*
RUN rm -rf /var/www/html/*

WORKDIR /data
