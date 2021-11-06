FROM yusufsanjaya24/alpine-caddy:base AS builder

FROM alpine:3.12
LABEL maintainer="MangUcub <yusufsanjaya@gmail.com>" \
    architecture="amd64/x86_64" \
    caddy-version="2.2.1" \
    alpine-version="3.12" \
	php-version="7.3" \
    build="03-Nov-2020"
ARG PUID="1000"
ARG PGID="1000"

COPY --from=ochinchina/supervisord:latest /usr/local/bin/supervisord /usr/bin/supervisord

# Let's Encrypt Agreement
ENV ACME_AGREE="false"

RUN apk add --no-cache --update \
		ca-certificates \
		curl \
		git \
		mailcap \
		openssh-client \
		php7-fpm \
		tar \
		tzdata \
		tini

RUN apk add --no-cache \
		php7-bcmath \
		php7-ctype \
		php7-curl \
		php7-dom \
		php7-exif \
		php7-fileinfo \
		php7-gd \
		php7-iconv \
		php7-json \
		php7-mbstring \
		php7-mysqli \
		php7-opcache \
		php7-openssl \
		php7-pdo \
		php7-pdo_mysql \
		php7-pdo_pgsql \
		php7-pdo_sqlite \
		php7-pgsql \
		php7-phar \
		php7-session \
		php7-simplexml \
		php7-sqlite3 \
		php7-tokenizer \
		php7-xml \
		php7-xmlreader \
		php7-xmlwriter \
		php7-zip


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
