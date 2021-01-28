FROM postgres:12
MAINTAINER Mago Pancione  <magiopancionel@gmail.com>

ENV POSTGRES_VERSION 12
ENV POSTGIS_VERSION 3

ENV PGROUTING_VERSION 3.0.1
ENV PGROUTING_SHA256 b8da69ebe2f46eb5ec2eb6dfc0d3960d26cedf7e2a509cf58f459c2fd0ae53b3


RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        postgis \
        postgresql-${POSTGRES_VERSION}-postgis-${POSTGIS_VERSION} \
        postgresql-${POSTGRES_VERSION}-postgis-${POSTGIS_VERSION}-scripts \
        libboost-atomic1.67.0 \
        libboost-chrono1.67.0 \
        libboost-graph1.67.0 \
        libboost-date-time1.67.0 \
        libboost-program-options1.67.0 \
        libboost-system1.67.0 \
        libboost-thread1.67.0 \
        libcgal13 
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        wget \
        libboost-graph-dev \
        libcgal-dev \
        libpq-dev \
        postgresql-server-dev-${PG_MAJOR} \
        git \
        ca-certificates \
        build-essential \
        autoconf \
        automake \
        cmake \
        zlib1g-dev \
        postgresql-server-dev-all \
        libxml2-dev \       
 && wget -O pgrouting.tar.gz "https://github.com/pgRouting/pgrouting/archive/v${PGROUTING_VERSION}.tar.gz" \
 && echo "$PGROUTING_SHA256 *pgrouting.tar.gz" | sha256sum -c - \
 && mkdir -p /usr/src/pgrouting \
 && tar \
        --extract \
        --file pgrouting.tar.gz \
        --directory /usr/src/pgrouting \
        --strip-components 1 \
 && rm pgrouting.tar.gz \
 && cd /usr/src/pgrouting \
 && mkdir build \
 && cd build \
 && cmake .. \
 && make \
 && make install \
 && cd / \
 && rm -rf /usr/src/pgrouting \
 && apt-mark manual postgresql-12 \
    && git clone https://github.com/verma/laz-perf.git \
    && cd laz-perf \
    && cmake . \
    && make \
    && make install \
    && cd .. \
    && rm -r laz-perf \
    && git clone https://github.com/pgpointcloud/pointcloud \
    && cd pointcloud \
    && ./autogen.sh \
    && ./configure --with-lazperf=/usr/local --with-pgconfig=/usr/lib/postgresql/${POSTGRES_VERSION}/bin/pg_config CFLAGS="-Wall -Werror -O2 -g" \
    && make \
    && make install \
     && apt purge -y --autoremove \
        build-essential \
        cmake \
        wget \
        libcgal-dev \
        libpq-dev \
        libboost-graph-dev \
        postgresql-server-dev-${PG_MAJOR} \ 
    && apt-get purge -y --auto-remove \
        git \
        ca-certificates \
        build-essential \
        autoconf \
        automake \
        cmake \
        zlib1g-dev \
        postgresql-server-dev-all \
        libxml2-dev \
    && rm -rf /var/lib/apt/lists/* 
RUN mkdir -p /docker-entrypoint-initdb.d
RUN apt-get update -y && apt-get install -y postgresql-12-postgis-3 cmake libpq-dev postgresql-server-dev-12 zip unzip python-pip wget
RUN wget -O /home/pgMemento_v0.7.1.zip "https://github.com/pgMemento/pgMemento/archive/v0.7.1.zip" 
RUN cd /home/; unzip pgMemento_v0.7.1.zip;  mv pgMemento-0.7.1 pgmemento
ENV PG_EXTENSION_DIR="/usr/share/postgresql/12/extension/"
RUN ls -ls /home/
RUN cd /home/pgmemento/extension/tests && /bin/bash install.sh
COPY ./initdb-pgpointcloud.sh /docker-entrypoint-initdb.d/10_pgpointcloud.sh
