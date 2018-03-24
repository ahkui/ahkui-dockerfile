FROM alpine:3.7

ENV \
    COMPOSER_ALLOW_SUPERUSER=1 \
    DEPS="nginx \
        nginx-mod-http-headers-more \
        php7.2 \
        php7.2-phar \
        php7.2-bcmath \
        php7.2-calendar \
        php7.2-mbstring \
        php7.2-exif \
        php7.2-ftp \
        php7.2-openssl \
        php7.2-zip \
        php7.2-sysvsem \
        php7.2-sysvshm \
        php7.2-sysvmsg \
        php7.2-shmop \
        php7.2-sockets \
        php7.2-zlib \
        php7.2-bz2 \
        php7.2-curl \
        php7.2-simplexml \
        php7.2-xml \
        php7.2-opcache \
        php7.2-dom \
        php7.2-xmlreader \
        php7.2-xmlwriter \
        php7.2-tokenizer \
        php7.2-ctype \
        php7.2-session \
        php7.2-fileinfo \
        php7.2-iconv \
        php7.2-json \
        php7.2-posix \
        php7.2-fpm \
        php7.2-mongodb \
        php7.2-redis \
        composer \
        phpunit \
        curl \
        php7.2-dev \
        gcc \
        g++ \
        ca-certificates"


ADD https://php.codecasts.rocks/php-alpine.rsa.pub /etc/apk/keys/php-alpine.rsa.pub
ADD https://repos.php.earth/alpine/phpearth.rsa.pub /etc/apk/keys/phpearth.rsa.pub
RUN apk --update add ca-certificates
RUN echo "@php https://php.codecasts.rocks/v3.7/php-7.2" >> /etc/apk/repositories
RUN set -x \
    && echo "https://repos.php.earth/alpine/v3.7" >> /etc/apk/repositories \
    && apk add --no-cache $DEPS \
    && ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log
RUN apk add --no-cache php7.2-mcrypt@php php7.2-mysqli@php php7.2-pdo@php php7.2-pdo_mysql@php
RUN apk add --no-cache git
RUN mkdir /run/nginx \
    && sed -i -e"s/worker_processes auto/worker_processes 5/" /etc/nginx/nginx.conf \
    && sed -i -e"s/keepalive_timeout\s*65/keepalive_timeout 2/" /etc/nginx/nginx.conf \
    && sed -i -e"s/keepalive_timeout 2/keepalive_timeout 2;\n\tproxy_buffer_size 256k;\n\tproxy_buffers 4 512k;\n\tproxy_busy_buffers_size 512k/" /etc/nginx/nginx.conf \
    && sed -i -e"s/client_max_body_size 1m;/client_max_body_size 128m;/" /etc/nginx/nginx.conf \
    && sed -i -e"s/user nginx/user root/" /etc/nginx/nginx.conf \
    && sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g" /etc/php/7.2/php.ini \
    && sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php/7.2/php.ini \
    && sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php/7.2/php.ini \
    && sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php/7.2/php-fpm.conf \
    && sed -i -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/pm.max_children = 5/pm.max_children = 9/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/pm.start_servers = 2/pm.start_servers = 3/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/pm.min_spare_servers = 1/pm.min_spare_servers = 2/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/pm.max_spare_servers = 3/pm.max_spare_servers = 4/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/pm.max_requests = 500/pm.max_requests = 200/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "/pid\s*=\s*\/run/c\pid = /run/php7.2-fpm.pid" /etc/php/7.2/php-fpm.conf \
    && sed -i -e "s/;listen.mode = 0660/listen.mode = 0750/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/user = www-data/user = root/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/group = www-data/group = root/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/listen.owner = www-data/listen.owner = root/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/listen.group = www-data/listen.group = root/g" /etc/php/7.2/php-fpm.d/www.conf \
    && echo -e "server {\n\tlisten 80 default_server;\n\tlisten [::]:80 default_server;\n\tlisten 443 ssl default_server;\n\tlisten [::]:443 ssl default_server;\n\troot /var/www/public;\n\tindex index.php;\n\tserver_name localhost;\n\tlocation / {\n\t\ttry_files \$uri \$uri/ /index.php?\$query_string;\n\t}\n\tlocation ~ \.php$ {\n\t\tinclude fastcgi.conf;\n\t\tfastcgi_pass unix:/run/php/php-fpm.sock;\n\t\tfastcgi_split_path_info ^(.+\.php)(/.+)$;\n\t\tfastcgi_read_timeout 600;\n\t}\n\tlocation ~ /\.ht {\n\t\tdeny all;\n\t}\n}\n" > /etc/nginx/conf.d/default.conf

EXPOSE 80 443

CMD php-fpm -D -R; nginx -g "daemon off;"






