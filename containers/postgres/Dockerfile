#FROM timescale/pg_prometheus
FROM timescale/pg_prometheus:latest-pg10

RUN echo "shared_preload_libraries = 'timescaledb'" >> /var/lib/postgresql/data/postgresql.conf
# echo "shared_preload_libraries = 'timescaledb'" >> /etc/postgresql/9.6/main/postgresql.conf

EXPOSE 5432

# RUN su - postgres -c "psql -U postgres -c \"SELECT pg_reload_conf();\""
