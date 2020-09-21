FROM prom/prometheus:v2.21.0

COPY prometheus.yml  /etc/prometheus/prometheus.yml

EXPOSE     9090
VOLUME     /prometheus
WORKDIR    /prometheus
ENTRYPOINT [ "/bin/prometheus" ]
CMD        [ "--config.file=/etc/prometheus/prometheus.yml" ]
