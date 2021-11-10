FROM yusufsanjaya24/alpine-caddy:base AS builder

FROM alpine:3.14
LABEL maintainer="MangUcub <yusufsanjaya@gmail.com>" \
    architecture="amd64/x86_64" \
    caddy-version="2.4.6" \
    alpine-version="3.14" \
	php-version="5.3" \
    build="03-Nov-2020"
ARG PUID="1000"
ARG PGID="1000"

# Let's Encrypt Agreement
ENV ACME_AGREE="true"

RUN apk add --no-cache \
		ca-certificates \
		curl \
		git \
		mailcap \
		openssh-client \
		php7-fpm \
		tar \
		tzdata

RUN apk add --no-cache \
		php5-bcmath \
		php5-ctype \
		php5-curl \
		php5-dom \
		php5-exif \
		php5-fileinfo \
		php5-gd \
		php5-iconv \
		php5-json \
		php5-mbstring \
		php5-mysqli \
		php5-opcache \
		php5-openssl \
		php5-pdo \
		php5-pdo_mysql \
		php5-pdo_pgsql \
		php5-pdo_sqlite \
		php5-pgsql \
		php5-phar \
		php5-session \
		php5-simplexml \
		php5-sqlite3 \
		php5-tokenizer \
		php5-xml \
		php5-xmlreader \
		php5-xmlwriter \
		php5-zip


#RUN ln -sf /usr/bin/php5 /usr/bin/php
RUN addgroup -g ${PGID} www-user && \
  adduser -D -H -u ${PUID} -G www-user www-user && \
  sed -i "s|^user = .*|user = www-user|g" /etc/php/php-fpm.d/www.conf && \
  sed -i "s|^group = .*|group = www-user|g" /etc/php/php-fpm.d/www.conf

RUN curl --silent --show-error --fail --location \
  --header "Accept: application/tar+gzip, application/x-gzip, application/octet-stream" \
  "https://getcomposer.org/installer" \
  | php -- --install-dir=/usr/bin --filename=composer

RUN echo "clear_env = no" >> /etc/php/php-fpm.conf

COPY --from=builder /install/caddy /usr/bin/caddy
RUN /usr/bin/caddy -version
RUN /usr/bin/caddy -plugins

EXPOSE 80 443 2015
VOLUME /etc/caddy/Caddyfile /srv
WORKDIR /srv

COPY files/Caddyfile /etc/caddy/Caddyfile
COPY files/index.php /srv
ENTRYPOINT [ "caddy" ]
CMD ["--config", "/etc/caddy/Caddyfile", "--log", "stdout", "--agree=$ACME_AGREE"]