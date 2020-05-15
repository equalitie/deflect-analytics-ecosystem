FROM       ubuntu
EXPOSE     8000
ENTRYPOINT ["/usr/bin/atsexporter"]

ARG GITUSER
ARG GITKEY

ENV APPPATH /toolbox/prometheus-grafana-nginx-docker/ats_exporter
ADD . $APPPATH
WORKDIR $APPPATH

RUN apt-get update
RUN apt-get install -y git python2.7 python-pip
RUN git clone https://$GITUSER:$GITKEY@github.com/equalitie/toolbox.git \
    && cd $APPPATH \
    && python setup.py install

COPY data/ats_metrics.json data/ats_metrics.json
COPY docker-config.yaml.example config.yaml
