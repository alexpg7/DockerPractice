FROM debian:12

RUN apt-get update \
    && apt-get install -y php8.2-fpm \
    && rm -rf /var/lib/apt/lists/*

RUN sed -i 's#^listen = .*#listen = 9000#' /etc/php/8.2/fpm/pool.d/www.conf

COPY index.php /var/www/html/index.php

CMD ["php-fpm8.2", "-F"]