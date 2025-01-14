#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#
# To edit the 'php-fpm' base Image, visit its repository on Github
#    https://github.com/Laradock/php-fpm
#
# To change its version, see the available Tags on the Docker Hub:
#    https://hub.docker.com/r/laradock/php-fpm/tags/
#
# Note: Base Image name format {image-tag}-{php-version}
#

## docker login --username=singlesword@126.com registry.cn-hangzhou.aliyuncs.com
FROM registry.cn-hangzhou.aliyuncs.com/ss-prod/php-fpm:7.4

LABEL maintainer="SwordLee <277850636@qq.com>"

ARG SSDOCKER_PHP_VERSION

# Set Environment Variables
ENV DEBIAN_FRONTEND noninteractive

# If you're in China, or you need to change sources, will be set CHANGE_SOURCE to true in .env.

ARG CHANGE_SOURCE=false
RUN if [ ${CHANGE_SOURCE} = true ]; then \
    # Change application source from deb.debian.org to aliyun source
    sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list && \
    sed -i 's/security-cdn.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list \
;fi

# always run apt update when start and after add new source list, then clean up at end.
RUN set -xe; \
    apt-get update -yqq && \
    pecl channel-update pecl.php.net && \
    apt-get install -yqq \
      apt-utils \
      gnupg2 \
      git \
      #
      #--------------------------------------------------------------------------
      # Mandatory Software's Installation
      #--------------------------------------------------------------------------
      #
      # Mandatory Software's such as ("mcrypt", "pdo_mysql", "libssl-dev", ....)
      # are installed on the base image 'laradock/php-fpm' image. If you want
      # to add more Software's or remove existing one, you need to edit the
      # base image (https://github.com/Laradock/php-fpm).
      #
      # next lines are here becase there is no auto build on dockerhub see https://github.com/laradock/laradock/pull/1903#issuecomment-463142846
      libzip-dev zip unzip && \
      if [ ${SSDOCKER_PHP_VERSION} = "7.3" ] || [ ${SSDOCKER_PHP_VERSION} = "7.4" ] || [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
        docker-php-ext-configure zip; \
      else \
        docker-php-ext-configure zip --with-libzip; \
      fi && \
      # Install the zip extension
      docker-php-ext-install zip && \
      php -m | grep -q 'zip'

#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# Optional Software's will only be installed if you set them to `true`
# in the `docker-compose.yml` before the build.
# Example:
#   - INSTALL_SOAP=true
#

###########################################################################
# BZ2:
###########################################################################

ARG INSTALL_BZ2=false
RUN if [ ${INSTALL_BZ2} = true ]; then \
  apt-get -yqq install libbz2-dev; \
  docker-php-ext-install bz2 \
;fi

###########################################################################
# pgsql
###########################################################################

ARG INSTALL_PGSQL=false

RUN if [ ${INSTALL_PGSQL} = true ]; then \
    # Install the pgsql extension
    docker-php-ext-install pgsql \
;fi

###########################################################################
# pgsql client
###########################################################################

ARG INSTALL_PG_CLIENT=false
ARG INSTALL_POSTGIS=false
ARG PG_CLIENT_VERSION

RUN if [ ${INSTALL_PG_CLIENT} = true ]; then \
    apt-get install -yqq gnupg \
    && . /etc/os-release \
    && echo "deb http://apt.postgresql.org/pub/repos/apt $VERSION_CODENAME-pgdg main" > /etc/apt/sources.list.d/pgdg.list \
    && curl -sL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
    && apt-get update -yqq \
    && apt-get install -yqq postgresql-client-${PG_CLIENT_VERSION} postgis; \
    if [ ${INSTALL_POSTGIS} = true ]; then \
      apt-get install -yqq postgis; \
    fi \
    && apt-get purge -yqq gnupg \
;fi

###########################################################################
# pcov:
###########################################################################

USER root

ARG INSTALL_PCOV=false

RUN if [ ${INSTALL_PCOV} = true ]; then \
  if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]  || { [ $(php -r "echo PHP_MAJOR_VERSION;") = "7" ] && [ $(php -r "echo PHP_MINOR_VERSION;") != "0" ]; }; then \
    pecl install pcov && \
    docker-php-ext-enable pcov \
  ;fi \
;fi

###########################################################################
# PHP REDIS EXTENSION
###########################################################################

ARG INSTALL_PHPREDIS=false

RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    # Install Php Redis Extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      pecl install -o -f redis-4.3.0; \
    elif [ $(php -r "echo PHP_MAJOR_VERSION;") = "7" ] && { [ $(php -r "echo PHP_MINOR_VERSION;") = "0" ] || [ $(php -r "echo PHP_MINOR_VERSION;") = "1" ] ;}; then \
      pecl install -o -f redis-5.3.7; \
    else \
      pecl install -o -f redis; \
    fi \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis \
;fi

###########################################################################
# Swoole EXTENSION
###########################################################################

ARG INSTALL_SWOOLE=false
RUN set -eux; \
    if [ ${INSTALL_SWOOLE} = true ]; then \
      # Install Php Swoole Extension
      if   [ $(php -r "echo PHP_VERSION_ID - PHP_RELEASE_VERSION;") = "50600" ]; then \
        pecl install swoole-2.0.10; \
      elif [ $(php -r "echo PHP_VERSION_ID - PHP_RELEASE_VERSION;") = "70000" ]; then \
        pecl install swoole-4.3.5; \
      elif [ $(php -r "echo PHP_VERSION_ID - PHP_RELEASE_VERSION;") = "70100" ]; then \
        pecl install swoole-4.5.11; \
      elif [ $(php -r "echo PHP_MAJOR_VERSION;") = "7" ]; then \
        pecl install swoole-4.8.12; \
      else \
        pecl install swoole; \
      fi; \
      docker-php-ext-enable swoole; \
      php -m | grep -q 'swoole'; \
    fi

###########################################################################
# MongoDB:
###########################################################################

ARG INSTALL_MONGO=false

RUN if [ ${INSTALL_MONGO} = true ]; then \
    # Install the mongodb extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      pecl install mongo; \
      docker-php-ext-enable mongo; \
      php -m | grep -oiE '^mongo$'; \
    else \
      if [ $(php -r "echo PHP_MAJOR_VERSION;") = "7" ] && [ $(php -r "echo PHP_MINOR_VERSION;") != "4" ]; then \
        if [ $(php -r "echo PHP_MINOR_VERSION;") = "0" ] || [ $(php -r "echo PHP_MINOR_VERSION;") = "1" ]; then \
          pecl install mongodb-1.9.2; \
        else \
          pecl install mongodb-1.16.2; \
        fi; \
      else \
        pecl install mongodb; \
      fi; \
      docker-php-ext-enable mongodb; \
      php -m | grep -oiE '^mongodb$'; \
    fi; \
fi

###########################################################################
# Xhprof:
###########################################################################

ARG INSTALL_XHPROF=false

RUN set -eux; \
    if [ ${INSTALL_XHPROF} = true ]; then \
      # Install the php xhprof extension
      if   [ $(php -r "echo PHP_MAJOR_VERSION;") != 5 ]; then \
        pecl install xhprof; \
      else \
        curl -L -o /tmp/xhprof.tar.gz "https://codeload.github.com/phacility/xhprof/tar.gz/master"; \
        mkdir -p /tmp/xhprof; \
        tar -C /tmp/xhprof -zxvf /tmp/xhprof.tar.gz --strip 1; \
        ( \
            cd /tmp/xhprof/extension; \
            phpize; \
            ./configure; \
            make; \
            make install; \
        ); \
        rm -r /tmp/xhprof; \
        rm /tmp/xhprof.tar.gz; \
      fi; \
      docker-php-ext-enable xhprof; \
      php -m | grep -q 'xhprof'; \
    fi

# if [ ${INSTALL_XHPROF_USE_TIDYWAYS} = true ]; then \
#   https://github.com/tideways/php-xhprof-extension
# fi

# COPY ./xhprof.ini /usr/local/etc/php/conf.d

# RUN if [ ${INSTALL_XHPROF} = false ]; then \
#     rm /usr/local/etc/php/conf.d/xhprof.ini \
# ;fi


###########################################################################
# PHP Memcached:
###########################################################################

ARG INSTALL_MEMCACHED=false

RUN if [ ${INSTALL_MEMCACHED} = true ]; then \
    # Install the php memcached extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      echo '' | pecl -q install memcached-2.2.0; \
    else \
      echo '' | pecl -q install memcached; \
    fi \
    && docker-php-ext-enable memcached \
;fi

###########################################################################
# Opcache:
###########################################################################

ARG INSTALL_OPCACHE=false

RUN if [ ${INSTALL_OPCACHE} = true ]; then \
    docker-php-ext-install opcache \
;fi

# Copy opcache configration
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

###########################################################################
# Mysqli Modifications:
###########################################################################

ARG INSTALL_MYSQLI=false

RUN if [ ${INSTALL_MYSQLI} = true ]; then \
    docker-php-ext-install mysqli \
;fi


###########################################################################
# Human Language and Character Encoding Support:
###########################################################################

ARG INSTALL_INTL=false

RUN if [ ${INSTALL_INTL} = true ]; then \
    # Install intl and requirements
    apt-get install -yqq zlib1g-dev libicu-dev g++ && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl \
;fi

###########################################################################
# MySQL Client:
###########################################################################

USER root

ARG INSTALL_MYSQL_CLIENT=false

RUN if [ ${INSTALL_MYSQL_CLIENT} = true ]; then \
      apt-get -y install default-mysql-client \
;fi

###########################################################################
# ping:
###########################################################################

USER root

ARG INSTALL_PING=false

RUN if [ ${INSTALL_PING} = true ]; then \
    apt-get -y install inetutils-ping \
;fi


###########################################################################
# Check PHP version:
###########################################################################

RUN set -xe; php -v | grep -q "PHP ${SSDOCKER_PHP_VERSION}."

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

#COPY ./laravel.ini /usr/local/etc/php/conf.d
#COPY ./xlaravel.pool.conf /usr/local/etc/php-fpm.d/

USER root

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm -f /var/log/lastlog /var/log/faillog

# Configure non-root user.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

# Adding the faketime library to the preload file needs to be done last
# otherwise it will preload it for all commands that follow in this file
RUN if [ ${INSTALL_FAKETIME} = true ]; then \
    echo "/usr/lib/x86_64-linux-gnu/faketime/libfaketime.so.1" > /etc/ld.so.preload \
;fi

# Configure locale.
ARG LOCALE=POSIX
ENV LC_ALL ${LOCALE}

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000