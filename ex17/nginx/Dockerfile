FROM debian:12

RUN apt-get update \
    && apt-get install -y nginx \
    && rm -rf /var/lib/apt/lists/*

COPY default.conf /etc/nginx/sites-available/default

CMD ["nginx", "-g", "daemon off;"]