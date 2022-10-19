FROM hostingmagentopl/clean-main-a2m80

RUN a2disconf php7.4-fpm && a2enconf php8.0-fpm \
    && update-alternatives --set php /usr/bin/php8.0 \
    && update-alternatives --remove php-fpm /usr/sbin/php-fpm7.4 \
    && update-alternatives --install /usr/sbin/php-fpm php-fpm /usr/sbin/php-fpm8.0 200

#RUN su - www-data -s /bin/bash -c "cp /var/www/.composer/auth.json html/var/composer_home/auth.json"

#RUN curl -Ss https://getcomposer.org/composer-stable.phar -o /usr/local/bin/composer

RUN su - www-data -s /bin/bash -c "cd html;rm -rf *;ls -a;composer create-project akeneo/pim-community-standard /var/www/html "6.0.*@stable""

#RUN su - www-data -s /bin/bash -c "cd html;cat .env"

COPY ./.env.local /var/www/html/.env.local

RUN chown -R www-data:www-data /var/www/html/.env.local

RUN find /var/lib/mysql/mysql -exec touch -c -a {} + \
    && service nginx start \
    && service mysql start \
    && su - www-data -s /bin/bash -c "cd html; NO_DOCKER=true make prod" \
    && service mysql stop \
    && service nginx stop

COPY ./apache2-www.conf /etc/apache2/sites-available/000-www.conf
COPY ./service-akeneo.conf /etc/supervisor/conf.d/akeneo.conf
COPY ./crontab.conf /var/www/

RUN su - www-data -s /bin/bash -c "cat /var/www/crontab.conf >> crontab"
RUN rm /var/www/crontab.conf

EXPOSE 80 22

CMD ["/usr/sbin/start.sh"]
