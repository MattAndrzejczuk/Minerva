FROM phusion/baseimage

# SETUP
RUN mkdir /mymdb
WORKDIR /mymdb
COPY requirements* /mymdb
COPY django/ /mymdb/django
COPY scripts/ /mymdb/scripts
RUN mkdir /var/log/mymdb/
RUN touch /var/log/mymdb/mymdb.log


# INSTALL PACKAGES
RUN apt-get update
RUN apt-get install -y \
    nginx \
    postgresql-client \
    python3 \
    python3-pip
RUN pip3 install virtualenv
RUN virtualenv /mymdb/venv
RUN bash /mymdb/scripts/pip_install.sh /mymdb
RUN bash /mymdb/scripts/collect_static.sh /mymdb


# NGINX
COPY nginx/minerva.nginx.conf /etx/nginx/sites-available/minerva.nginx.conf
RUN rm/etc/nginx/sites-enabled/*
RUN ln -s /etc/nginx/sites-available/minerva.nginx.conf /etc/nginx/sites-enabled/minerva.nginx.conf

COPY runit/nginx /etc/service/nginx
RUN chmod +x /etc/service/nginx/run


# CONFIGURE uWSGI
COPY uwsgi/mymdb.ini /etc/uwsgi/apps-enabled/minerva.ini
RUN mkdir -p /var/log/uwsgi/
RUN touch /var/log/uwsgi/
RUN chown www-data /var/log/uwsgi/mymdb.log
RUN chown www-data /var/log/mymdb.log

COPY runit/uwsgi /etc/service/uwsgi
RUN chmod +x /etc/service/uwsgi/run


RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
EXPOSE 80
