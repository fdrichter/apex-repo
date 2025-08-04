FROM jonasal/nginx-certbot:latest AS stage

RUN mkdir -p /etc/nginx/user_conf.d

COPY ./custom.conf /etc/nginx/user_conf.d/custom.conf
