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
    && curl -sSLf \
    -o /usr/local/bin/install-php-extensions \
    https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions \
    && chmod +x /usr/local/bin/install-php-extensions

RUN install-php-extensions \
    @composer \
    bcmath \
    exif \
    gd \
    igbinary \
    imagick \
    intl \
    msgpack \
    mysqli \
    opcache \
    pcntl \
    pdo_mysql \
    redis \
    soap \
    zip

RUN npm install -g svgo

RUN setcap "cap_net_bind_service=+ep" $(which php) \
    && setcap "cap_net_bind_service=+ep" $(which php-fpm)

RUN apk del .build-deps

COPY runtimes/supervisord.conf /etc/supervisord.conf
COPY runtimes/www.conf /usr/local/etc/php-fpm.d/www.conf

WORKDIR /src/api

STOPSIGNAL SIGQUIT

EXPOSE 5173
EXPOSE 8000
EXPOSE 9000

USER $UID

CMD ["supervisord"]
