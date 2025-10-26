# 设置镜像为 php:8.3-fpm
FROM php:8.3-fpm

# 设置维护者
LABEL maintainer="elai <elai.job@qq.com>"

# 编译安装核心扩展 
# @see https://hub.docker.com/_/php?uuid=e869fb5e-0bec-49c8-bd69-dcf7ed0b45fe
RUN apt-get update && apt-get install -y \
    libfreetype-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

# 安装扩展
RUN pecl install redis \
    && pecl install swoole \
    && docker-php-ext-enable redis swoole

# 下载并安装 Composer（全局安装）
RUN curl -sS https://getcomposer.org/installer | php -- \
    --install-dir=/usr/local/bin \
    --filename=composer

# 设置时区
ENV TZ=Asia/Shanghai
