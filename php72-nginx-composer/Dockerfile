FROM alpine:3.7

ARG BUILD_DATE
ARG VCS_REF

LABEL org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.vcs-url="https://github.com/php-earth/docker-php.git" \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.schema-version="1.0" \
      org.label-schema.vendor="PHP.earth" \
      org.label-schema.name="docker-php" \
      org.label-schema.description="Docker For PHP Developers - Docker image with PHP 7.2, Nginx, and Alpine" \
      org.label-schema.url="https://github.com/php-earth/docker-php"

ENV \
    # When using Composer, disable the warning about running commands as root/super user
    COMPOSER_ALLOW_SUPERUSER=1 \
    # Persistent runtime dependencies
    DEPS="nginx \
        nginx-mod-http-headers-more \
        php7 \
        php7-phar \
        php7-bcmath \
        php7-calendar \
        php7-mbstring \
        php7-exif \
        php7-ftp \
        php7-openssl \
        php7-zip \
        php7-sysvsem \
        php7-sysvshm \
        php7-sysvmsg \
        php7-shmop \
        php7-sockets \
        php7-zlib \
        php7-bz2 \
        php7-curl \
        php7-simplexml \
        php7-xml \
        php7-opcache \
        php7-dom \
        php7-xmlreader \
        php7-xmlwriter \
        php7-tokenizer \
        php7-ctype \
        php7-session \
        php7-fileinfo \
        php7-iconv \
        php7-json \
        php7-posix \
        php7-fpm \
        php7-mongodb \
        php7-redis \
        composer \
        phpunit \
        curl \
        php7-dev \
        gcc \
        g++ \
        ca-certificates \
        runit"

# PHP.earth Alpine repository for better developer experience

ADD https://php.codecasts.rocks/php-alpine.rsa.pub /etc/apk/keys/php-alpine.rsa.pub
RUN apk --update add ca-certificates
RUN set -x \
    && echo "https://php.codecasts.rocks/v3.7/php-7.2" >> /etc/apk/repositories \
    && apk add --no-cache $DEPS \
    && ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

RUN mkdir /run/nginx \
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
    && sed -i -e "/pid\s*=\s*\/run/c\pid = /run/php7-fpm.pid" /etc/php/7.2/php-fpm.conf \
    && sed -i -e "s/;listen.mode = 0660/listen.mode = 0750/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/user = www-data/user = root/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/group = www-data/group = root/g" /etc/php/7.2/php-fpm.d/www.conf \
    && sed -i -e "s/listen.owner = www-data/listen.owner = root/g" /etc/php/7.2/php-fpm.d/www.conf \
    && echo -e "server {\n\tlisten 80 default_server;\n\tlisten [::]:80 default_server;\n\tlisten 443 ssl default_server;\n\tlisten [::]:443 ssl default_server;\n\troot /var/www/public;\n\tindex index.php;\n\tserver_name localhost;\n\tlocation / {\n\t\ttry_files $uri $uri/ /index.php?$query_string;\n\t}\n\tlocation ~ \.php$ {\n\t\tinclude fastcgi.conf;\n\t\tfastcgi_pass unix:/run/php/php7-fpm.sock;\n\t\tfastcgi_split_path_info ^(.+\.php)(/.+)$;\n\t}\n\tlocation ~ /\.ht {\n\t\tdeny all;\n\t}\n}" > /etc/nginx/conf.d/default.conf



RUN apk add --update --no-cache php7-mcrypt php7-mysqli php7-pdo
RUN apk add --update --no-cache git


EXPOSE 80 443

CMD ["php-fpm","-d","&&","nginx","-g","daemon off;"]
