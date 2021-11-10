FROM yusufsanjaya24/alpine-caddy:base AS builder

FROM alpine:edge
LABEL maintainer="MangUcub <yusufsanjaya@gmail.com>" \
    architecture="amd64/x86_64" \
    caddy-version="2.4.6" \
    alpine-version="3.14" \
	php-version="7.4" \
    build="11-Nov-2021"
ARG PUID="1000"
ARG PGID="1000"

COPY --from=ochinchina/supervisord:latest /usr/local/bin/supervisord /usr/bin/supervisord

# Let's Encrypt Agreement
ENV ACME_AGREE="true"
RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
RUN apk update && apk upgrade
RUN apk add --no-cache --update \
		ca-certificates \
		curl \
		git \
		mailcap \
		php7-fpm \
		tar \
		tzdata \
		tini \
		php7 \
		php7-phar \
		php7-json \
		php7-iconv \
		php7-openssl

RUN apk add --no-cache\
	php7-ftp \
	php7-xdebug \
	php7-mcrypt \
	php7-mbstring \
	php7-soap \
	php7-gmp \
	php7-pdo_odbc \
	php7-dom \
	php7-pdo \
	php7-zip \
	php7-mysqli \
	php7-sqlite3 \
	php7-pdo_pgsql \
	php7-bcmath \
	php7-gd \
	php7-odbc \
	php7-pdo_mysql \
	php7-pdo_sqlite \
	php7-gettext \
	php7-xml \
	php7-xmlreader \
	php7-xmlwriter \
	php7-tokenizer \
	php7-xmlrpc \
	php7-bz2 \
	php7-pdo_dblib \
	php7-curl \
	php7-ctype \
	php7-session \
	php7-redis \
	php7-exif \
	php7-intl \
	php7-fileinfo \
	php7-ldap \
	php7-apcu \
	php7-simplexml


RUN ln -sf /usr/bin/php7 /usr/bin/php
RUN addgroup -g ${PGID} www-user && \
  adduser -D -H -u ${PUID} -G www-user www-user && \
  sed -i "s|^user = .*|user = www-user|g" /etc/php7/php-fpm.d/www.conf && \
  sed -i "s|^group = .*|group = www-user|g" /etc/php7/php-fpm.d/www.conf

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

RUN echo "clear_env = no" >> /etc/php7/php-fpm.conf

COPY --from=builder /usr/bin/caddy /usr/bin/caddy
# RUN /usr/bin/caddy -version
# RUN /usr/bin/caddy -plugins

EXPOSE 80 443
VOLUME /etc/caddy
VOLUME /srv
WORKDIR /srv
RUN mkdir -p /etc/caddy
ADD ./files/Caddyfile /etc/caddy/
COPY ./files/php.ini /etc/php7/
COPY ./files/index.php /srv
COPY ./files/supervisord.conf /supervisord.conf
COPY ./files/entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT [ "tini", "--", "/entrypoint.sh" ]
