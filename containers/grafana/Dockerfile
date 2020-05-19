FROM grafana/grafana:6.5.0

ARG BASKERVILLE_DB

ENV DS_BASKERVILLE_DB $BASKERVILLE_DB

ENV GF_SECURITY_ADMIN_PASSWORD pass
# optional:
ENV GF_INSTALL_PLUGINS grafana-clock-panel,grafana-simple-json-datasource

VOLUME /var/lib/grafana

COPY ./dashboards_config /etc/grafana/provisioning/dashboards
COPY ./datasources /etc/grafana/provisioning/datasources
COPY ./dashboards /etc/grafana/provisioning/dashboards/baskerville

EXPOSE 3000
