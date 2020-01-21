FROM alpine:3.8

ARG V_NGINX=1.13.4
ARG V_NDK=0.3.1
ARG V_HASH=0.32
ARG V_ECHO=0.61
ARG V_DAVEXT=3.0.0
ARG V_LET=0.0.4

RUN set -x \
    && addgroup -g 101 -S nginx \
    && adduser -S -D -H -u 101 -h /var/cache/nginx -s /sbin/nologin -G nginx -g nginx nginx \
    && apk add --no-cache --virtual .build-deps \
        openssl-dev \
        pcre-dev \
        zlib-dev \
        geoip-dev \
        gd-dev \
        libxslt-dev \
        build-base autoconf libtool

RUN set -x \
    && wget -qO- https://nginx.org/download/nginx-${V_NGINX}.tar.gz | tar xz -C /tmp \
    && wget -qO- https://github.com/simplresty/ngx_devel_kit/archive/v${V_NDK}.tar.gz | tar xz -C /tmp \
    && wget -qO- https://github.com/openresty/echo-nginx-module/archive/v${V_ECHO}.tar.gz | tar xz -C /tmp \
    && wget -qO- https://github.com/arut/nginx-dav-ext-module/archive/v${V_DAVEXT}.tar.gz | tar xz -C /tmp \
    && wget -qO- https://github.com/arut/nginx-let-module/archive/v${V_LET}.tar.gz | tar xz -C /tmp \
    && wget -qO- https://github.com/openresty/set-misc-nginx-module/archive/v${V_HASH}.tar.gz | tar xz -C /tmp/

RUN cd /tmp/nginx-${V_NGINX} && \
    ./configure  --prefix=/usr \
                 --conf-path=/etc/nginx/nginx.conf \
                 --error-log-path=/var/log/nginx/error.log \
                 --http-client-body-temp-path=/dev/shm/nginx_temp_path \
                 --http-fastcgi-temp-path=/dev/shm/nginx_fastcgi \
                 --http-proxy-temp-path=/dev/shm/nginx_proxy \
                 --lock-path=/dev/shm/nginx.lock \
                 --pid-path=/var/run/nginx.pid \
                 --with-debug \
                 --with-http_secure_link_module \
                 --with-http_flv_module \
                 --with-http_geoip_module \
                 --with-http_gzip_static_module \
                 --with-http_realip_module \
                 --with-http_stub_status_module \
                 --with-http_ssl_module \
                 --with-http_sub_module \
                 --with-mail \
                 --with-mail_ssl_module \
                 --add-module=/tmp/ngx_devel_kit-${V_NDK} \
                 --add-module=/tmp/set-misc-nginx-module-${V_HASH} \
                 --add-module=/tmp/echo-nginx-module-${V_ECHO} \
                 --add-module=/tmp/nginx-let-module-${V_LET} \
                 --with-http_sub_module \
                 --with-http_gunzip_module \
                 --with-http_image_filter_module \
                 --with-http_dav_module \
                 --add-module=/tmp/nginx-dav-ext-module-${V_DAVEXT} \
                 && make -j2 && make install

RUN set -x \
    && rm -rf /var/cache/apk/* /tmp/* \
    && ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

VOLUME ["/var/log/nginx"]

EXPOSE 80 443

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
