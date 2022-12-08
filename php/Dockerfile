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
RUN apt-get install -y procps build-essential zlib1g-dev unzip bzip2 libbz2-dev libxml2 libxml2-dev libldap2-dev apt-utils libjpeg-dev libfreetype6-dev libpng-dev gcc \
    vim libzip-dev git

### 初始化 /usr/src/php
RUN docker-php-source extract

### docker-php-ext 扩展php安装
RUN docker-php-ext-install pcntl pdo pdo_mysql gd zip sockets

### pecl 安装扩展 redis , psr ，swoole
#RUN pecl install redis \
#    && pecl install psr \
#    && pecl install https://pecl.php.net/get/swoole-4.8.12.tgz \
#    && docker-php-ext-enable redis swoole \
#    && echo 'extension=psr.so' > /usr/local/etc/php/conf.d/11_psr.ini \
#    ### 开启 swoole.use_shortname = 'Off'
#    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini
#
# 安装扩展 psr
RUN pecl install psr && echo 'extension=psr.so' > /usr/local/etc/php/conf.d/11_psr.ini

# 安装扩展 mongodb
RUN pecl install mongodb && docker-php-ext-enable mongodb \
&& echo "extension=mongodb.so" > /usr/local/etc/php/conf.d/docker-php-ext-mongodb.ini

# 安装扩展
ADD ./ext/swoole-v4.6.6.zip swoole-v4.6.6.zip
ADD ./ext/redis-5.3.7.zip redis-5.3.7.zip
ADD ./ext/cphalcon-4.0.6.zip cphalcon-4.0.6.zip
RUN unzip swoole-v4.6.6.zip   && mv swoole-v4.6.6  /usr/src/php/ext/swoole \
&&  unzip redis-5.3.7.zip     && mv redis-5.3.7 /usr/src/php/ext/redis \
&&  unzip cphalcon-4.0.6.zip  && mv cphalcon-4.0.6 /usr/src/php/ext/cphalcon-4.0.6  &&  cd /usr/src/php/ext/cphalcon-4.0.6/build && ./install \
&& docker-php-ext-install swoole redis  \
### 开启 swoole.use_shortname = 'Off'
&& echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini \
&&  echo 'extension=phalcon.so' > /usr/local/etc/php/conf.d/10_phalcon.ini

# 安装 rabbitmq-c
ADD ./ext/rabbitmq-c-0.8.0.zip rabbitmq-c-0.8.0.zip
RUN unzip rabbitmq-c-0.8.0.zip
RUN cd rabbitmq-c-0.8.0 && chmod -R 777 configure &&  ./configure --prefix=/usr/local/rabbitmq-c &&  make install
RUN rm -rf rabbitmq-c-0.8.0.zip

# 安装 amqp 扩展
ADD ./ext/amqp-1.10.2.zip amqp-1.10.2.zip
RUN unzip amqp-1.10.2.zip && mv amqp-1.10.2 /usr/src/php/ext/amqp
RUN docker-php-ext-configure amqp --with-php-config=/usr/local/bin/php-config  --with-librabbitmq-dir=/usr/local/rabbitmq-c
RUN docker-php-ext-install amqp
RUN rm -rf amqp-1.10.2.zip

### 安装composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

### 更改composer 镜像
RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

### 清除 安装包
#RUN rm -rf /tmp/*
#RUN rm -rf /var/www/html/*

WORKDIR /data