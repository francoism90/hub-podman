FROM php:8.2.13-fpm-alpine

ARG UID=1000
ARG GID=$UID

ENV TZ=UTC

RUN apk update --no-cache \
    && apk upgrade --no-cache

RUN apk add --no-cache --virtual .build-deps $PHPIZE_DEPS \
    shadow \
    gcc \
    libc-dev \
    make \
    findutils \
    alpine-sdk \
    linux-headers \
    brotli-dev \
    ffmpeg-dev \
    fdk-aac-dev \
    freetype-dev \
    icu-dev \
    imagemagick-dev \
    libass-dev \
    libdrm-dev \
    libjpeg-turbo-dev \
    libogg-dev \
    libpng-dev \
    libtheora-dev \
    libwebp-dev \
    libxml2-dev \
    libxslt-dev \
    libzip-dev \
    linux-headers \
    oniguruma-dev \
    openssl-dev \
    pcre2-dev \
    perl-dev \
    rtmpdump-dev \
    sqlite-dev \
    zlib-dev

RUN apk add --no-cache --virtual .run-deps \
    openssl \
    git \
    brotli \
    ffmpeg \
    fdk-aac \
    freetype \
    gifsicle \
    gsm \
    icu \
    imagemagick \
    jpegoptim \
    libass \
    libavif \
    libcap \
    libjpeg \
    libjpeg-turbo \
    libogg \
    libpng \
    libtheora \
    libvorbis \
    libvpx \
    libwebp \
    libxml2 \
    libxslt \
    libzip \
    mariadb-client \
    nodejs \
    npm \
    oniguruma \
    optipng \
    pcre2 \
    perl \
    pngquant \
    rtmpdump \
    speex \
    sqlite \
    supervisor \
    tzdata \
    unzip \
    x264 \
    x265 \
    xvidcore \
    zlib

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN usermod --non-unique --uid $UID www-data \
    && groupmod --non-unique --gid $GID www-data

RUN cp "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" \
    && sed -i "s/error_reporting = .*/error_reporting = E_ALL/" "$PHP_INI_DIR/php.ini" \
    && sed -i "s/display_errors = .*/display_errors = On/" "$PHP_INI_DIR/php.ini" \
    && sed -i "s/memory_limit = .*/memory_limit = 512M/" "$PHP_INI_DIR/php.ini" \
    && sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" "$PHP_INI_DIR/php.ini" \
    && sed -i "s/post_max_size = .*/post_max_size = 100M/" "$PHP_INI_DIR/php.ini" \
    && sed -i "s/;date.timezone.*/date.timezone = UTC/" "$PHP_INI_DIR/php.ini" \
    && php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) exif \
    && docker-php-ext-install -j$(nproc) fileinfo \
    && docker-php-ext-install -j$(nproc) intl \
    && docker-php-ext-install -j$(nproc) mbstring \
    && docker-php-ext-install -j$(nproc) mysqli \
    && docker-php-ext-install -j$(nproc) pcntl \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    && docker-php-ext-install -j$(nproc) soap \
    && docker-php-ext-install -j$(nproc) xml \
    && docker-php-ext-install -j$(nproc) zip \
    && pecl install igbinary \
    && pecl install imagick \
    && pecl install msgpack \
    && pecl install redis \
    && docker-php-ext-enable igbinary \
    && docker-php-ext-enable imagick \
    && docker-php-ext-enable msgpack \
    && docker-php-ext-enable opcache \
    && docker-php-ext-enable redis \
    && apk del .build-deps

RUN npm install -g svgo

RUN setcap "cap_net_bind_service=+ep" $(which php) \
    && setcap "cap_net_bind_service=+ep" $(which php-fpm)

COPY runtimes/supervisord.conf /etc/supervisord.conf
COPY runtimes/www.conf /usr/local/etc/php-fpm.d/www.conf

WORKDIR /src/api

STOPSIGNAL SIGQUIT

EXPOSE 5173
EXPOSE 8000
EXPOSE 9000

USER $UID

CMD ["supervisord"]
