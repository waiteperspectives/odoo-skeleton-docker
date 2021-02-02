FROM ubuntu:focal
LABEL version="1.0"
LABEL maintainer="Waite Perspectives, LLC <zach@waiteperspectives.com>"
LABEL description="Odoo Server on Ubuntu 20.04 + python3.8 + node 14"

SHELL ["/bin/bash", "-xo", "pipefail", "-c"]

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
         curl \
         wget \
         gcc \
         g++ \
         python3 \
         python3-pip \
         python3-virtualenv \
         python3-setuptools \
         python3-dev \
         libpng-dev \
         libevent-dev \
         libjpeg-dev \
         libldap2-dev \
         libpq-dev \
         libsasl2-dev \
         libxml2-dev \
         libxslt1-dev \
         zlib1g-dev \
         xfonts-75dpi \
         xfonts-base \
         xz-utils \
         ca-certificates \
         gnupg \
         postgresql-client \
    && curl -o wkhtmltox.deb -sSL https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb \
    && apt-get install -y --no-install-recommends ./wkhtmltox.deb \
    && rm -rf /var/lib/apt/lists/* wkhtmltox.deb 

RUN curl -sL https://deb.nodesource.com/setup_14.x  | bash - \
    && apt-get install nodejs -y --no-install-recommends \
    && useradd -ms /bin/bash odoo \
    && mkdir /srv/odoo

COPY ./odoo/requirements.txt /srv/odoo
COPY ./odoo /srv/odoo/src/odoo
RUN chown -R odoo /srv/odoo \
  && pip3 install --no-cache-dir -r /srv/odoo/requirements.txt \
  && pip3 install --no-cache-dir /srv/odoo/src/odoo


# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./config/odoo.conf /etc/odoo/

# Set permissions and Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN chown odoo /etc/odoo/odoo.conf \
    && mkdir -p /mnt/extra-addons \
    && chown -R odoo /mnt/extra-addons \
    && mkdir -p /srv/odoo/filestore \
    && chown -R odoo /srv/odoo/filestore

# cleanup
RUN apt-get remove --purge -y \
         wget \
         gcc \
         g++ \
         python3-dev \
         libpng-dev \
         libevent-dev \
         libjpeg-dev \
         libldap2-dev \
         libpq-dev \
         libsasl2-dev \
         libxml2-dev \
         libxslt1-dev \
         xz-utils
  

VOLUME ["/mnt/extra-addons", "/srv/odoo/filestore"]

# Expose Odoo services
EXPOSE 8069 8071 8072

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

COPY wait-for-psql.py /usr/local/bin/wait-for-psql.py

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
