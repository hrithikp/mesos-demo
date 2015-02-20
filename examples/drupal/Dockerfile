# Use Ubuntu 14.04 Trusty
FROM ubuntu:trusty

# RUN echo "deb http://ppa.launchpad.net/nginx/development/ubuntu trusty main" >> /etc/apt/sources.list.d/nginx.list
# RUN echo "deb-src http://ppa.launchpad.net/nginx/development/ubuntu trusty main " >> /etc/apt/sources.list.d/nginx.list

RUN apt-get update && \
    apt-get install -y \
    make \
    apache2 \
    php5 \
    php5-gd \
    php5-cli \
    php-pear \
    php5-mysql \
    php5-curl \
    mysql-client \
    libapache2-mod-php5 


RUN pear upgrade --force pear \
    && pear upgrade-all \
    && pear clear-cache \
    && pear channel-discover pear.drush.org \ 
    && pear install drush/drush \
    && drush

ADD . /var/www

WORKDIR /var/www/html
VOLUME /var/www/html
EXPOSE 80
CMD ["/usr/bin/make", "drupal"]