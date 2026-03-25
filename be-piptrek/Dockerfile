# ---- Stage 1: Install dependencies ----
FROM alpine:3.21 AS build

RUN apk upgrade --no-cache \
    && apk add --no-cache \
       php84 php84-phar php84-curl php84-openssl php84-mbstring \
       php84-tokenizer php84-xml php84-simplexml php84-dom \
       php84-fileinfo php84-ctype php84-iconv php84-pdo \
       php84-pdo_pgsql php84-pgsql php84-zip php84-intl \
       php84-bcmath php84-gd php84-pcntl php84-session \
       php84-opcache php84-sodium php84-xmlwriter php84-xmlreader \
       unzip git \
    && ln -sf /usr/bin/php84 /usr/bin/php

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

ENV COMPOSER_ALLOW_SUPERUSER=1

WORKDIR /app
COPY composer.json composer.lock* ./
RUN composer install --no-dev --no-scripts --no-autoloader --prefer-dist
COPY . .
RUN composer dump-autoload --optimize --classmap-authoritative

# ---- Stage 2: Production (Alpine-native PHP — fewer CVEs) ----
FROM alpine:3.21

RUN apk upgrade --no-cache \
    && apk add --no-cache \
       php84 php84-fpm php84-pdo php84-pdo_pgsql php84-pgsql \
       php84-zip php84-intl php84-mbstring php84-bcmath php84-opcache \
       php84-gd php84-pcntl php84-tokenizer php84-xml php84-dom \
       php84-fileinfo php84-ctype php84-session php84-openssl \
       php84-curl php84-phar php84-iconv \
       nginx supervisor \
    && ln -sf /usr/bin/php84 /usr/bin/php \
    && adduser -D -S -G www-data www-data 2>/dev/null || true

# Configure PHP-FPM
RUN sed -i 's|^listen = .*|listen = 127.0.0.1:9000|' /etc/php84/php-fpm.d/www.conf \
    && sed -i 's|^user = .*|user = www-data|' /etc/php84/php-fpm.d/www.conf \
    && sed -i 's|^group = .*|group = www-data|' /etc/php84/php-fpm.d/www.conf

WORKDIR /var/www/html
COPY --from=build /app /var/www/html

COPY docker/nginx.conf /etc/nginx/http.d/default.conf
COPY docker/supervisord.conf /etc/supervisord.conf

RUN chown -R www-data:www-data storage bootstrap/cache \
    && chmod -R 775 storage bootstrap/cache

EXPOSE 80

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]
