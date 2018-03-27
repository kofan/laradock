FROM nginx:alpine

LABEL maintainer="Mahmoud Zalt <mahmoud@zalt.me>"

ADD nginx.conf /etc/nginx/

# If you're in China, or you need to change sources, will be set CHANGE_SOURCE to true in .env.

ARG CHANGE_SOURCE=false
RUN if [ ${CHANGE_SOURCE} = true ]; then \
    # Change application source from dl-cdn.alpinelinux.org to aliyun source
    sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/' /etc/apk/repositories \
;fi

RUN apk update \
    && apk upgrade \
    && apk add --no-cache bash \
    && apk add --no-cache openssl \
    && adduser -D -H -u 1000 -s /bin/bash www-data

ARG SSL_SITE_NAME=laradock.test
ARG PHP_UPSTREAM_CONTAINER=php-fpm
ARG PHP_UPSTREAM_PORT=9000

# Set upstream conf and remove the default conf
RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf \
    && rm /etc/nginx/conf.d/default.conf

# create a folder for the keys
RUN mkdir /etc/nginx/ssl 2> /dev/null
# generate the keys for your local domain
RUN openssl genrsa -out "/etc/nginx/ssl/${SSL_SITE_NAME}.key" 2048 \
    && openssl req -new -key "/etc/nginx/ssl/${SSL_SITE_NAME}.key" -out "/etc/nginx/ssl/${SSL_SITE_NAME}.csr" -subj "/CN=${SSL_SITE_NAME}/O=${SSL_SITE_NAME}/C=UK" \
    && openssl x509 -req -days 365 -in "/etc/nginx/ssl/${SSL_SITE_NAME}.csr" -signkey "/etc/nginx/ssl/${SSL_SITE_NAME}.key" -out "/etc/nginx/ssl/${SSL_SITE_NAME}.crt"

CMD ["nginx"]

EXPOSE 80 443
