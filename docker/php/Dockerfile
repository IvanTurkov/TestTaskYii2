FROM ubuntu:latest

ARG HOST_UID=1000
ARG HOST_GID=1000
ARG HOST_USER=host-user
ARG HOST_GROUP=host-group

# install via apt-get
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install  \
    php7.2 php7.2-fpm php7.2-cli \
    php7.2-curl php7.2-mysql php7.2-sqlite3 \
    php7.2-mbstring php7.2-bcmath php7.2-soap \
    php7.2-xml php7.2-zip php7.2-gd php7.2-ldap \
    php-xdebug php-pear php7.2-dev \
    wget curl mc nano supervisor sudo cron \
    fontconfig libssl1.0.0 libxext6 libxrender1 xfonts-75dpi xfonts-base xdg-utils ghostscript

#install pecl extensions
RUN pecl install channel://pecl.php.net/weakref-0.3.3

#install composer
RUN curl --insecure https://getcomposer.org/composer.phar -o /usr/bin/composer && chmod +x /usr/bin/composer

RUN groupadd -g $HOST_GID $HOST_GROUP && groupadd sudonopswd && \
    useradd -m -l -g $HOST_GROUP -u $HOST_UID $HOST_USER && \
    usermod -aG sudo $HOST_USER && usermod -aG sudonopswd $HOST_USER

#sudo no password
RUN echo '%sudonopswd ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

#php-fpm
RUN sed -e 's#listen = /run/php/php7.2-fpm.sock#listen = 0.0.0.0:9000#g' -i /etc/php/7.2/fpm/pool.d/www.conf
RUN service php7.2-fpm start

ADD php-fpm.ini /etc/php/7.2/fpm/conf.d/10-fpm-user-setting.ini
ADD php-cli.ini /etc/php/7.2/cli/conf.d/10-cli-user-setting.ini

ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf

USER host-user
WORKDIR /var/www/app

ENTRYPOINT ["sudo", "/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf"]
