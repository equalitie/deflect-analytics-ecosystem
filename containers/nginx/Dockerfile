FROM nginx:1.12.2

ENV NGINX_VERSION     "1.14.0"
ENV NGINX_VTS_VERSION "0.1.15"

RUN echo "deb-src http://nginx.org/packages/debian/ stretch nginx" >> /etc/apt/sources.list \
  && apt-get update \
  && apt-get install -y dpkg-dev curl apt-utils \
  && mkdir -p /opt/rebuildnginx \
  && chmod 0777 /opt/rebuildnginx \
  && cd /opt/rebuildnginx \
  && su --preserve-environment -s /bin/bash -c "apt-get source nginx" _apt \
  && apt-get build-dep -y nginx=${NGINX_VERSION} \
  && apt --fix-broken install -y \
  && apt-get update --fix-missing -y \
  && cd /opt \
  && curl -sL https://github.com/vozlt/nginx-module-vts/archive/v${NGINX_VTS_VERSION}.tar.gz | tar -xz \
  && sed -i -r -e "s/\.\/configure(.*)/.\/configure\1 --add-module=\/opt\/nginx-module-vts-${NGINX_VTS_VERSION}/" /opt/rebuildnginx/nginx-${NGINX_VERSION}/debian/rules \
  && cd /opt/rebuildnginx/nginx-${NGINX_VERSION} \
  && dpkg-buildpackage -b \
  && cd /opt/rebuildnginx \
  && dpkg --install nginx_${NGINX_VERSION}-1~stretch_amd64.deb \
  && apt --fix-broken install -y \
  && apt-get remove --purge -y dpkg-dev curl && apt-get -y --purge autoremove && rm -rf /var/lib/apt/lists/*

COPY ./conf/nginx.conf /etc/nginx/
COPY ./conf/default.conf /etc/nginx/conf.d/
COPY ./html/index.html  /usr/share/nginx/html/

CMD ["nginx", "-g", "daemon off;"]
