FROM laradock/php-fpm:latest-7.2

# 设置工作目录
WORKDIR /var/www

# 复制项目文件到容器中
COPY . /var/www

# If you're in China, or you need to change sources, will be set CHANGE_SOURCE to true in .env.

ARG CHANGE_SOURCE=false
RUN if [ ${CHANGE_SOURCE} = true ]; then \
    # Change application source from deb.debian.org to aliyun source
    sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list && \
    sed -i 's/security-cdn.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list \
;fi

ARG TZ=Asia/Shanghai

# always run apt update when start and after add new source list, then clean up at end.
RUN set -xe; \
    echo "Current TZ: $TZ" && \
    # 更新包列表
    apt-get update -yqq && \
    # 更新 PECL 频道
    pecl channel-update pecl.php.net && \
    # 安装所需的包
    apt-get install -yqq \
      apt-utils \
      gnupg2 \
      git \
      libzip-dev \
      zip \
      unzip \
      tzdata && \
    # 设置系统时区
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    # 修改 php.ini 文件中的时区设置
    echo "date.timezone = $TZ" > /usr/local/etc/php/conf.d/timezone.ini && \
    # 根据 PHP 版本配置 zip 扩展
    if [ ${LARADOCK_PHP_VERSION} = "7.3" ] || [ ${LARADOCK_PHP_VERSION} = "7.4" ] || [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
        docker-php-ext-configure zip; \
    else \
        docker-php-ext-configure zip --with-libzip; \
    fi && \
    # 安装 zip 扩展
    docker-php-ext-install zip && \
    # 确认 zip 扩展安装成功
    php -m | grep -q 'zip' && \
    # 安装 Composer
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    # 确认 Composer 安装成功
    composer --version && \
    # 清理缓存以减小镜像大小
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


RUN touch /var/www/composer.lock

#安装项目依赖

###########################################################################
# PHP REDIS EXTENSION
###########################################################################
ARG INSTALL_PHPREDIS=true
RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    # Install Php Redis Extension
    # if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
    #   pecl install -o -f redis-4.3.0; \
    # else \
      pecl install -o -f redis-5.3.7 && \
    # fi \
    rm -rf /tmp/pear && \
    docker-php-ext-enable redis \
;fi

###########################################################################
# Mysqli Modifications:
###########################################################################

ARG INSTALL_MYSQLI=true

RUN if [ ${INSTALL_MYSQLI} = true ]; then \
    docker-php-ext-install mysqli \
;fi


# 修改文件权限
RUN chown -R www-data:www-data /var/www

# 设置权限
RUN chmod -R 755 /var/www

# 暴露PHP-FPM端口
EXPOSE 9000

# 启动PHP-FPM
CMD ["php-fpm"]
