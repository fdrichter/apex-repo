FROM jonasal/nginx-certbot:latest AS stage

RUN mkdir -p /etc/nginx/user_conf.d
# Copiar arquivos de configuração do Nginx

COPY ./custom.conf /etc/nginx/user_conf.d/custom.conf
