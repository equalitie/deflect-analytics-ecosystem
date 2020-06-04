# Docker files for the  Deflect Analytics Ecosystem
## Usage
- Before running: 
    - for the kafka service: `export DOCKER_KAFKA_HOST=$(ipconfig getifaddr en0)` where `en0` is the name of the interface you are currently using.
    - if you are using the baskerville container, make sure you have a `.env` file where `docker-compose.yaml` is. You can use the `dot_env_file` example file by renaming it to `.env` and
changing the variable values.
- To run: `docker-compose up -d` to create and bring up the containers defined in `docker-compose.yaml`
- To see the logs: `docker-compose logs <name of your service that was defined in docker-compose file, e.g. baskerville>`
- To stop: in the `docker-compose.yaml` directory, run `docker-compose down` to stop all containers.
- To permanently stop and remove `docker `containers:
```bash
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```
- Useful urls:
    - Baskerville metrics exporter: http://localhost:8998
    - Grafana: http://localhost:3000
    - Prometheus: http://localhost:9090
    - Prometheus Status: http://localhost:9090/targets (to check which services are up)
    - Prometheus Push Gateway: http://localhost:9091
    - Spark: http://localhost:4040 (available only when Baskerville is running)

### Baskerville specific usage:
To run the following pipelines, change the `command` part in docker-compose baskerville section:
- rawlog: 
    ```yaml
     # -e is optional, used to export metrics
     python ./main.py -c /app/baskerville/conf/baskerville.yaml rawlog -e -t
    ```
- kafka:
    ```yaml
     # -s is used to start a script that will post logs in kafka, to simulate incoming log traffic
     python ./main.py -c /app/baskerville/conf/baskerville.yaml kafka -e -s -t
    ```
- es:
    ```yaml
     # note that an elasticsearch service is not currently provided. There is a dockerfile that can be used
     # but with no sample data.
     python ./main.py -c /app/baskerville/conf/baskerville.yaml es -e -t
    ```
  
 Note: ` -t adds a test model in database`
  
All the services defined here are to support [Baskerville](https://github.com/equalitie/baskerville), an Analytics Engine that leverages Machine Learning to identify anomalies in web traffic.
Components to Pipelines:

For Baskerville `kafka` you'll need:
  - Kafka
  - Zookeeper
  - Postgres
  - Prometheus  [optional]
  - Grafana     [optional]

For Baskerville `rawlog` you'll need:
  - Postgres
  - Elastic Search
  - Prometheus  [optional]
  - Grafana     [optional]

For Baskerville `es` you'll need:
  - Postgres
  - Elastic Search
  - Prometheus  [optional]
  - Grafana     [optional]

__An ElasticSearch service is not provided.__

For Baskerville `training` you'll need:
  - Postgres
  - Prometheus  [optional]
  - Grafana     [optional]

## The containers:
- Postgres: a Postgres instance with the Timescale extension
- Postgres Exporter (postgres-exporter): to monitor Postgres with Prometheus
- Prometheus: To gather metrics about components
- Prometheus Postgresql Adapter (prometheus-postgresql-adapter): To use Postgres as a backend for Prometheus
- Prometheus Push Gateway (prometheus_push_gw): For short lived metrics, like Spark metrics
- Grafana: For metrics visualisation and alerts. Grafana comes preconfigured with two Datasources: Prometheus and Postgres. The Baskerville, spark and kafka-related dashboards are also pre-loaded.
- Zookeeper: For managing the Kafka instance
- Kafka: For log publishing/ queuing so that Baskerville can consume and process the incoming weblogs (practically used to transport logs from the web servers to Baskerville)
- Kafka Exporter: To monitor Kafka through Prometheus
- Baskerville: the analytics engine

### Grafana:
- Kafka metrics:
    - https://grafana.com/dashboards/721
    - https://grafana.com/dashboards/5484
- Postgres metrics:
    - Two ways:
        - Directly connect to a Postgres database and set up a dashboard with queries like:
            ```sql
             SELECT
              $__time(request_sets.created_at),
              count(id)
            FROM
              request_sets
            GROUP BY target, time
            
            OR 
  
            SELECT
              $__time(request_sets.created_at),
              count(id),
              CAST(target AS TEXT) AS metric
            FROM
              request_sets
            WHERE $__timeFilter(request_sets.created_at)
            GROUP BY target, time
            ORDER BY time
            ```
           to have an overview of what is going on in the data
           
           NOTE: A user for grafana must be created to avoid running weird queries, like delete * from...
           ```sql
             CREATE USER grafanareader WITH PASSWORD 'password';
             GRANT USAGE ON SCHEMA schema TO grafanareader;
             GRANT SELECT ON schema.table TO grafanareader;
          ```
          
          More on this [here](https://github.com/grafana/grafana/blob/master/docs/sources/features/datasources/postgres.md)
        - Set up a postgres exporter to monitor requests, ram etc



<a rel="license" href="http://creativecommons.org/licenses/by/4.0/">
<img alt="Creative Commons Licence" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/80x15.png" /></a><br />
This work is copyright (c) 2020, eQualit.ie inc., and is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.