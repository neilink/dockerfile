ARG RESTY_IMAGE_BASE="base1804"
ARG RESTY_IMAGE_TAG="ssh"

FROM ${RESTY_IMAGE_BASE}:${RESTY_IMAGE_TAG}

LABEL maintainer="NEIL.Cheng <neilink@zoho.com>"

ARG RESTY_VERSION="1.13.6.2"
ARG RESTY_LUAROCKS_VERSION="2.4.4"
ARG RESTY_OPENSSL_VERSION="1.1.0i"
ARG RESTY_PCRE_VERSION="8.42"
ARG RESTY_J="2"
ARG RESTY_CONFIG_OPTIONS="\
    --prefix=/usr/local/openresty \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \ 
    --user=www-data \ 
    --group=www-data \
    --with-compat \
    --add-module=../naxsi/naxsi_src/ \
    --with-file-aio \
    --with-http_addition_module \
    --with-http_auth_request_module \
    --with-http_dav_module \
    --with-http_flv_module \
    --with-http_geoip_module=dynamic \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_image_filter_module=dynamic \
    --with-http_mp4_module \
    --with-http_random_index_module \
    --with-http_realip_module \
    --with-http_secure_link_module \
    --with-http_slice_module \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-http_sub_module \
    --with-http_v2_module \
    --with-http_xslt_module=dynamic \
    --with-mail \
    --with-mail_ssl_module \
    --with-pcre-jit \
    --with-stream \
    --with-stream_ssl_module \
    --with-threads \ 
    "
ARG RESTY_CONFIG_OPTIONS_MORE=""
LABEL resty_version="${RESTY_VERSION}"
LABEL resty_luarocks_version="${RESTY_LUAROCKS_VERSION}"
LABEL resty_openssl_version="${RESTY_OPENSSL_VERSION}"
LABEL resty_pcre_version="${RESTY_PCRE_VERSION}"
LABEL resty_config_options="${RESTY_CONFIG_OPTIONS}"
LABEL resty_config_options_more="${RESTY_CONFIG_OPTIONS_MORE}"

# These are not intended to be user-specified
ARG _RESTY_CONFIG_DEPS="--with-openssl=/usr/local/openssl-${RESTY_OPENSSL_VERSION} --with-pcre=/usr/local/pcre-${RESTY_PCRE_VERSION}"

RUN DEBIAN_FRONTEND=noninteractive apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        vim \
        curl \
        net-tools \
        iputils-ping \
        iproute2 \
        gettext-base \
        libgd-dev \
        libgeoip-dev \
        libncurses5-dev \
        libperl-dev \
        libreadline-dev \
        libxslt1-dev \
        make \
        perl \
        unzip \
        zlib1g-dev \
        autoconf  \ 
        automake  \
        build-essential \  
        git  \
        libcurl4-openssl-dev \
        libgeoip-dev  \
        liblmdb-dev  \  
        libpcre++-dev  \ 
        libtool  \
        libxml2-dev  \
        libyajl-dev  \
        pkgconf  \ 
        zlib1g-dev \
        doxygen \
        libssl-dev \
        daemon \
        wget \
        bzip2 \
        unzip 
         
WORKDIR /usr/local        
RUN git clone https://github.com/nbs-system/naxsi.git \ 
    && curl -fSL https://www.openssl.org/source/openssl-${RESTY_OPENSSL_VERSION}.tar.gz -o openssl-${RESTY_OPENSSL_VERSION}.tar.gz \
    && tar xzf openssl-${RESTY_OPENSSL_VERSION}.tar.gz \
    && curl -fSL https://ftp.pcre.org/pub/pcre/pcre-${RESTY_PCRE_VERSION}.tar.gz -o pcre-${RESTY_PCRE_VERSION}.tar.gz \
    && tar xzf pcre-${RESTY_PCRE_VERSION}.tar.gz \
    && curl -fSL https://openresty.org/download/openresty-${RESTY_VERSION}.tar.gz -o openresty-${RESTY_VERSION}.tar.gz \
    && tar xzf openresty-${RESTY_VERSION}.tar.gz \
    && cd /usr/local/openresty-${RESTY_VERSION} \
    && ./configure -j${RESTY_J} ${_RESTY_CONFIG_DEPS} ${RESTY_CONFIG_OPTIONS} ${RESTY_CONFIG_OPTIONS_MORE} \
    && make -j${RESTY_J} \
    && make -j${RESTY_J} install \
    && mkdir -p /usr/local/openresty/conf.d     
RUN  rm -rf \
        openssl-${RESTY_OPENSSL_VERSION} \
        openssl-${RESTY_OPENSSL_VERSION}.tar.gz \
        openresty-${RESTY_VERSION}.tar.gz openresty-${RESTY_VERSION} \
        pcre-${RESTY_PCRE_VERSION}.tar.gz pcre-${RESTY_PCRE_VERSION} \
    && curl -fSL https://github.com/luarocks/luarocks/archive/${RESTY_LUAROCKS_VERSION}.tar.gz -o luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && tar xzf luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && cd luarocks-${RESTY_LUAROCKS_VERSION} \
    && ./configure \
        --prefix=/usr/local/openresty/luajit \
        --with-lua=/usr/local/openresty/luajit \
        --lua-suffix=jit-2.1.0-beta3 \
        --with-lua-include=/usr/local/openresty/luajit/include/luajit-2.1 \
    && make build \
    && make install \
    && cd /usr/local \
    && rm -rf luarocks-${RESTY_LUAROCKS_VERSION} luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && DEBIAN_FRONTEND=noninteractive apt-get autoremove -y \
    && ln -sf /dev/stdout /var/log/nginx/access.log  \
    && ln -sf /dev/stderr /var/log/nginx/error.log \
    && cd /usr/local/naxsi/naxsi_config \
    && cp naxsi_core.rules /usr/local/openresty/conf.d/naxsi_core.rules
 
###############################################i

# Add additional binaries into PATH for convenience
ENV PATH=$PATH:/usr/local/openresty/luajit/bin:/usr/local/openresty/nginx/sbin:/usr/local/openresty/bin

# Add LuaRocks paths
# If OpenResty changes, these may need updating:
#    /usr/local/openresty/bin/resty -e 'print(package.path)'
#    /usr/local/openresty/bin/resty -e 'print(package.cpath)'
ENV LUA_PATH="/usr/local/openresty/site/lualib/?.ljbc;/usr/local/openresty/site/lualib/?/init.ljbc;/usr/local/openresty/lualib/?.ljbc;/usr/local/openresty/lualib/?/init.ljbc;/usr/local/openresty/site/lualib/?.lua;/usr/local/openresty/site/lualib/?/init.lua;/usr/local/openresty/lualib/?.lua;/usr/local/openresty/lualib/?/init.lua;./?.lua;/usr/local/openresty/luajit/share/luajit-2.1.0-beta3/?.lua;/usr/local/share/lua/5.1/?.lua;/usr/local/share/lua/5.1/?/init.lua;/usr/local/openresty/luajit/share/lua/5.1/?.lua;/usr/local/openresty/luajit/share/lua/5.1/?/init.lua"

ENV LUA_CPATH="/usr/local/openresty/site/lualib/?.so;/usr/local/openresty/lualib/?.so;./?.so;/usr/local/lib/lua/5.1/?.so;/usr/local/openresty/luajit/lib/lua/5.1/?.so;/usr/local/lib/lua/5.1/loadall.so;/usr/local/openresty/luajit/lib/lua/5.1/?.so"
# Copy nginx configuration files
RUN mkdir -p /usr/local/openresty/ssl \
&& cd /usr/local/openresty/ssl \
&& openssl req -x509 -newkey rsa:4086 \
  -subj "/C=TW/ST=Taiwan/L=New Taipei City/O=ICPO/CN=localhost" \
  -keyout "localhost.key" \
  -out "localhost.pem" \
  -days 3650 -nodes -sha256 \
&& chmod 600 localhost.key

ADD nginx.conf /usr/local/openresty/nginx/conf/nginx.conf
ADD default.conf /usr/local/openresty/conf.d/default.conf
ADD naxsi.rules /usr/share/naxsi.rules
ADD error-400.html /usr/local/openresty/nginx/html/error-400.html
ADD favicon.ico  /usr/local/openresty/nginx/html/favicon.ico
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
WORKDIR /usr/local/openresty
EXPOSE 22 80 443
CMD ["/usr/local/openresty/bin/openresty", "-g", "daemon off;"]

