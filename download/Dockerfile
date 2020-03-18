FROM php:7.3-fpm-alpine

LABEL maintainer="slairmy"
LABEL version="1.0"
LABEL description="PHP image by Slairmy"

# 环境变量
ENV COMPOSER_VERSION 1.9.0
ENV SWOOLE_VERSION 4.4.12
ENV REDIS_VERSION 5.0.2
ENV ZIP_VERSION 1.15.5


# 构建过程所需的包
ENV BUILD_DEPS \
    gzip \
    git \
    cmake \
    build-base \
    autoconf \
	libzip-dev \
    libpng-dev

# 运行依赖包
ENV PKG \
    libstdc++ \
    bash \
	libzip \
    gnu-libiconv \
    libpng


# 换源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

# 更新当包
RUN apk update --no-cache
# 安装依赖包，构建包
RUN apk add --no-cache ${PKG} ${BUILD_DEPS}




# 安装composer并配置中国镜像
RUN cd /tmp \
    && curl -L http://getcomposer.org/installer -o composer-setup.php \
    && php composer-setup.php --version=${COMPOSER_VERSION} --install-dir=/usr/local/bin --filename=composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com


# 源码方式安装 swoole
RUN cd /tmp \
    && pecl download swoole-${SWOOLE_VERSION} \
    && tar -xzf swoole-${SWOOLE_VERSION}.tgz \
    && cd swoole-${SWOOLE_VERSION} \
    && phpize \
    && ./configure \
    && make && make install \
    && touch /usr/local/etc/php/conf.d/swoole.ini \
    && echo 'extension=swoole.so' > /usr/local/etc/php/conf.d/swoole.ini


# 安装php拓展
RUN pecl install redis-${REDIS_VERSION} zip-${ZIP_VERSION} \
    && docker-php-ext-enable redis zip \
    && docker-php-ext-install mysqli bcmath gd pdo_mysql



RUN rm -rf /tmp/* \
    && rm -rf /var/lib/apk/ && rm -rf /etc/apk/cache/ \
    && apk del ${BUILD_DEPS}

ADD ./Shanghai /etc/localtime

WORKDIR /app






