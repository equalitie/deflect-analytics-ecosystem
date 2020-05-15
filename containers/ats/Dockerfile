# based on: https://github.com/wernight/docker-trafficserver
FROM debian:jessie

ENV ATS_VERSION 7.0.0

# Update the package repository
RUN set -x \
 && DEBIAN_FRONTEND=noninteractive apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        locales \
        autoconf \
        build-essential \
        bzip2 \
        libssl-dev \
        libxml2 \
        libxml2-dev \
        libpcre3 \
        libpcre3-dev \
        tcl \
        tcl-dev \
        libboost-dev \
 # Configure locale
 && echo en_US UTF-8 >> /etc/locale.gen \
 && export LC_CTYPE=en_US.UTF-8 \
 && export LANGUAGE=en_US.UTF-8 \
 && export LANG=en_US.UTF-8 \
 && export LC_ALL=en_US.UTF-8 \
 && locale-gen \
 #  en_US.UTF-8 \
 && DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales \
 # Install TrafficServer
 # https://trafficserver.apache.org/downloads
 && mkdir /tmp/trafficserver \
 && cd /tmp/trafficserver \
 && curl -L http://www-us.apache.org/dist/trafficserver/trafficserver-$ATS_VERSION.tar.bz2 | tar -xj --strip-components 1 \
 && autoconf -i \
 && ./configure --enable-experimental-plugins \
 && make install \
 && cd / \
    # Install dumb-init
    # https://github.com/Yelp/dumb-init
 && DUMP_INIT_URI=$(curl -L https://github.com/Yelp/dumb-init/releases/latest | grep -Po '(?<=href=")[^"]+_amd64(?=")') \
 && curl -Lo /usr/local/bin/dumb-init "https://github.com/$DUMP_INIT_URI" \
 && chmod +x /usr/local/bin/dumb-init \
    # Clean-up
 && apt-get purge --auto-remove -y \
        curl \
        build-essential \
        bzip2 \
        libssl-dev \
        libxml2-dev \
        libpcre3-dev \
        tcl-dev \
        libboost-dev \
 && apt-get clean \
 && rm -rf /tmp/* /var/lib/apt/lists/*

# Add Stats over http configuration in /_statistics endpoint:
#RUN echo "/usr/local/libexec/trafficserver/stats_over_http.so _statistics" >> /usr/local/etc/trafficserver/plugin.config
#RUN echo "/usr/local/libexec/trafficserver/system_stats.so _statistics" >> /usr/local/etc/trafficserver/plugin.config
RUN echo "stats_over_http.so _statistics" >> /usr/local/etc/trafficserver/plugin.config
#RUN echo "system_stats.so" >> /usr/local/etc/trafficserver/plugin.config
#RUN echo "map http://workload.example.com/ http://127.0.0.1/ @plugin=generator.so" >> /usr/local/etc/trafficserver/remap.config
#RUN echo "map http://cache.example.com http://127.0.0.1/ @plugin=authproxy.so @pparam=--auth-transform=redirect @pparam=--auth-host=127.0.0.1 @pparam=--auth-port=8000" >> /usr/local/etc/trafficserver/remap.config

EXPOSE 8080

ENTRYPOINT ["dumb-init"]
CMD ["traffic_cop", "--debug", "--stdout"]