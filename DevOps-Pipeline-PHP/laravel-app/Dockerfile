# Pull php-apache image 
FROM php:7.4-apache-bullseye

# use root user
USER root 

# Set working directory to the apache public directory 
WORKDIR /var/www/html

# 1. تثبيت حزم النظام المطلوبة أولاً (تستفيد من الـ Cache ولا تتكرر إلا لو غيرت الحزم)
RUN apt-get update && apt-get install -y --no-install-recommends \
    libpng-dev \
    zlib1g-dev \
    libxml2-dev \
    libzip-dev \
    zip \
    curl \
    unzip \
    && docker-php-ext-configure gd \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install zip \
    && docker-php-source delete \
    && rm -rf /var/lib/apt/lists/*

# 2. تحميل الـ Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# 3. إعداد الـ Virtual Host وتفعيل الـ rewrite module الخاص بأباتشي
COPY ./vhost.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite

# 4. نسخ ملفات المشروع وتغيير ملكيتها مباشرة لمستخدم الويب أثناء النسخ (أسرع بكثير من chown منفصل)
COPY --chown=www-data:www-data . /var/www/html/

# Switch to non-root user for runtime
USER www-data
