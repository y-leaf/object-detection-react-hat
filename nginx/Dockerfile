FROM nginx:latest

COPY ./files/object-detection.conf /etc/nginx/conf.d/default.conf
COPY ./ssl/server.crt /etc/ssl/certs/
COPY ./ssl/server.key /etc/ssl/certs/