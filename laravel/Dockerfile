# 使用 Laravel Sail 提供的 PHP 8.2 和 Composer 基础镜像
FROM laravelsail/php82-composer:latest

# 安装系统依赖和 PHP 扩展
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libzip-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd zip pdo pdo_mysql \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# 设置工作目录
WORKDIR /var/www

# 设置允许 Composer 在非交互模式下运行
ENV COMPOSER_ALLOW_SUPERUSER=1

RUN composer require barryvdh/laravel-debugbar --dev
# 安装项目依赖，忽略平台要求（根据需要取消注释）
# RUN composer install --ignore-platform-reqs

# 设定默认命令（可选）
CMD ["php", "-S", "0.0.0.0:8000", "-t", "public"]
