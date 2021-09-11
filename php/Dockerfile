FROM ubuntu:20.04

LABEL maintainer="kesaralive@gmail.com"
LABEL description="Apache / PHP Flexguru Server"

ARG DEBIAN_FRONTEND=newt

RUN apt-get -y update && apt-get install -y \
apache2 \
php8.0 \
libapache2-mod-php8.0 \
php8.0-bcmath \
php8.0-gd \
php8.0-sqlite \
php8.0-mysql \
php8.0-curl \
php8.0-xml \
php8.0-mbstring \
php8.0-zip \
mcrypt \
nano  

RUN apt-get install locales
RUN locale-gen fr_FR.UTF-8
RUN locale-gen en_US.UTF-8
RUN locale-gen de_DE.UTF-8
#ENV LANG fr_FR.UTF-8
#ENV LANGUAGE fr_FR:fr
#ENV LC_ALL fr_FR.UTF-8 

# config PHP
# we want a dev server which shows PHP errors
RUN sed -i -e 's/^error_reporting\s*=.*/error_reporting = E_ALL/' /etc/php/8.0/apache2/php.ini
RUN sed -i -e 's/^display_errors\s*=.*/display_errors = On/' /etc/php/8.0/apache2/php.ini
RUN sed -i -e 's/^zlib.output_compression\s*=.*/zlib.output_compression = Off/' /etc/php/8.0/apache2/php.ini

# to be able to use "nano" with shell on "docker exec -it [CONTAINER ID] bash"
ENV TERM xterm

# Apache conf
# allow .htaccess with RewriteEngine
RUN a2enmod rewrite
# to see live logs we do : docker logs -f [CONTAINER ID]
# without the following line we get "AH00558: apache2: Could not reliably determine the server's fully qualified domain name"
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf
# autorise .htaccess files
RUN sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf
# for production :
# RUN echo "ServerTokens Prod\n" >> /etc/apache2/apache2.conf
# RUN echo "ServerSignature Off\n" >> /etc/apache2/apache2.conf

#RUN chown -R www-data:www-data /var/www
#RUN chmod 755 -R /var/www
RUN chgrp -R www-data /var/www
RUN find /var/www -type d -exec chmod 775 {} +
RUN find /var/www -type f -exec chmod 664 {} +

# Virtual hosts if needed
# RUN echo "<VirtualHost *:80>\n  ServerName subdomain1.localhost\n  DocumentRoot /var/www/subdomain1\n</VirtualHost>\n" > /etc/apache2/sites-available/subdomain1.conf
# RUN echo "<VirtualHost *:80>\n  ServerName subdomain2.localhost\n  DocumentRoot /var/www/subdomain2\n</VirtualHost>\n" > /etc/apache2/sites-available/subdomain2.conf
# RUN a2ensite subdomain1
# RUN a2ensite subdomain2

EXPOSE 80

# start Apache2 on image start
CMD ["/usr/sbin/apache2ctl","-DFOREGROUND"]

# NB : si update image and commit, do :
# docker commit -m "changes" --change "ENV TERM xterm" --change "CMD /usr/sbin/apache2ctl -DFOREGROUND" dc5239032732 apache-php-dev
