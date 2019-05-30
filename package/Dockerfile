FROM python:2.7

RUN pip install jinja2 datetime PyYAML && \
    apt-get update && \
    apt install -y nginx

COPY . /var/www/html
RUN cd /var/www/html/slides && \
    TEMPLATE="workshop-vmware.html" ./build.sh once

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

WORKDIR /var/www/html
EXPOSE 80
STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
