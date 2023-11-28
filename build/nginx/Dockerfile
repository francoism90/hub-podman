FROM nginxinc/nginx-unprivileged:mainline-alpine

ARG UID=1000
ARG GID=$UID

ENV TZ=UTC

USER root

RUN apk update --no-cache \
    && apk upgrade --no-cache

RUN apk add --no-cache --virtual .build-deps \
    shadow \
    git \
    gcc \
    libc-dev \
    make \
    findutils \
    alpine-sdk \
    linux-headers \
    bash \
    brotli-dev \
    fdk-aac-dev \
    gsm-dev \
    libass-dev \
    libdrm-dev \
    libjpeg-turbo-dev \
    libogg-dev \
    libpng-dev \
    libtheora-dev \
    libva-dev \
    libvorbis-dev \
    libvpx-dev \
    libwebp-dev \
    libxslt-dev \
    openssl-dev \
    pcre2-dev \
    perl-dev \
    rtmpdump-dev \
    speex-dev \
    x264-dev \
    x265-dev \
    xvidcore-dev \
    zlib-dev

RUN apk add --no-cache --virtual .run-deps \
    openssl \
    brotli \
    ffmpeg-dev \
    fdk-aac \
    gsm \
    libass \
    libjpeg \
    libjpeg-turbo \
    libogg \
    libtheora \
    libvorbis \
    libwebp \
    libvpx \
    rtmpdump \
    speex \
    tzdata \
    unzip \
    x264 \
    x265 \
    xvidcore

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN usermod --non-unique --uid $UID nginx \
    && groupmod --non-unique --gid $GID nginx

RUN mkdir -p /usr/local/src && rm -rf /usr/local/src/* \
    && cd /usr/local/src \
    && wget "https://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" -O nginx.tar.gz \
    && set -x \
    && tar xzvf nginx.tar.gz \
    && rm -rf nginx.tar.gz \
    && mv /usr/local/src/nginx-${NGINX_VERSION} /usr/local/src/nginx \
    && CONFARGS=$(nginx -V 2>&1 | sed -n -e 's/^.*arguments: //p')

RUN cd /usr/local/src \
    && git clone --recursive --depth 1 https://github.com/google/ngx_brotli.git \
    && MODULEDIR=$(pwd)/ngx_brotli \
    && cd /usr/local/src/nginx \
    && ./configure --with-compat $CONFARGS --add-dynamic-module=$MODULEDIR --with-cc-opt="-O3" \
    && make \
    && make install

RUN cd /usr/local/src \
    && git clone --depth 1 https://github.com/kaltura/nginx-vod-module.git \
    && MODULEDIR=$(pwd)/nginx-vod-module \
    && cd /usr/local/src/nginx \
    && sed -i "s/\/\/ XXXXX add //g" "$MODULEDIR/ngx_http_vod_module.c" \
    && ./configure --with-compat $CONFARGS --add-dynamic-module=$MODULEDIR --with-file-aio --with-threads --with-cc-opt="-O3" \
    && make \
    && make install

RUN apk del .build-deps

USER nginx

COPY runtimes/. /etc/nginx

STOPSIGNAL SIGQUIT

EXPOSE 1936
EXPOSE 8080
EXPOSE 4443

CMD ["nginx", "-g", "daemon off;"]
