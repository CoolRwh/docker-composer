FROM php:7.4-fpm

# 语言和时间
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

## 阿里云
# RUN  sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
# RUN  sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list 
RUN sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list

RUN  apt-get clean && apt-get update

RUN apt-get install -y procps build-essential zlib1g-dev unzip bzip2 libbz2-dev libxml2 libxml2-dev libldap2-dev apt-utils libjpeg-dev libfreetype6-dev libpng-dev gcc libzip-dev 
 
### 扩展安装  
RUN docker-php-ext-install pcntl pdo pdo_mysql gd zip 


### 安装 cphalcon
# ADD ./ext/cphalcon-4.0.6.zip cphalcon-4.0.6.zip
# RUN unzip cphalcon-4.0.6.zip
# RUN cd cphalcon-4.0.6/build && ./install
# RUN echo 'extension=phalcon.so' > /usr/local/etc/php/conf.d/10_phalcon.ini
# RUN rm -rf cphalcon-4.0.6.zip


### 安装psr
RUN pecl install psr
RUN echo 'extension=psr.so' > /usr/local/etc/php/conf.d/11_psr.ini


### 安装扩展 redis , swoole 
RUN pecl install redis \
    && pecl install swoole \
    && docker-php-ext-enable redis swoole
### 开启 swoole.use_shortname = 'Off' 
RUN echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini

# 安装 supervisor
RUN apt-get install -y supervisor


ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

### 更改composer 镜像
RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/




WORKDIR /data