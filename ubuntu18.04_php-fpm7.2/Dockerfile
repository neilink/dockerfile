FROM base1804:ssh
MAINTAINER  neilda@zoho.com

RUN DEBIAN_FRONTEND=noninteractive apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends sed locales curl \
        vim \
        curl \
        ca-certificates\
        php7.2-fpm \
        php7.2-common\
        php7.2-mbstring\
        php7.2-curl\
        php7.2-cli\ 
        php7.2-json\
        php7.2-gd \
        php7.2-cli \
        php7.2-mbstring \
        php7.2-xml \
        php7.2-opcache\
        php7.2-mysql \
        php7.2-dev \
        php7.2-cgi \
        php-pear  \
        php7.2-intl\
        php7.2-xml\
        php-redis \
        php-memcache \
        php-mongodb \
        unzip
WORKDIR /tmp 
RUN curl -sS https://getcomposer.org/installer | php \
&& mv composer.phar /usr/local/bin/composer \
&&  mkdir -p /run/php/ \ 
&& sed -i "s/;date.timezone =.*/date.timezone = Asia\/\Taipei/" /etc/php/7.2/cli/php.ini \
&& sed -i "s/;date.timezone =.*/date.timezone = Asia\/\Taipei/" /etc/php/7.2/fpm/php.ini \
&& sed -i "s/upload_max_filesize = .*/upload_max_filesize = 10M/" /etc/php/7.2/fpm/php.ini \
&& sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/7.2/fpm/php.ini \
&& sed -i "s/listen = .*/listen = 9000/" /etc/php/7.2/fpm/pool.d/www.conf 

RUN useradd neil -ms /bin/bash \
&& echo 'neil:asaburu' | chpasswd 

WORKDIR /home/neil

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
&& su - neil

ADD naxsi.rules /usr/share/naxsi.rules
EXPOSE 9000
CMD ["/usr/sbin/php-fpm7.2","-F"]
