# Druid - Apache Kafka ingestion - Example using Docker

A docker, docker-compose based example. Data ingested to Kafka moves to  Druid automatically by Druid supervisor (tested only on `Docker for Mac`).


#### Steps

- Bring Druid, Kafka, Zookeeper up
```
docker-compose up -d
```

-  Create supervisor to pull data from Kafka.
```
curl -XPOST -H'Content-Type: application/json' -d @supervisor_spec.json http://localhost:8081/druid/indexer/v1/supervisor
```

- Download Kafka tarball for some helpful Kafka CLI scripts.
```
curl -O https://archive.apache.org/dist/kafka/2.7.0/kafka_2.13-2.7.0.tgz
tar -xzf kafka_2.13-2.7.0.tgz
cd kafka_2.13-2.7.0
```

- Create Kafka Topic `druidstream`:
```
$$ ./bin/kafka-topics.sh --create --topic druidstream --bootstrap-server localhost:9093
```

- Send sample data to Topic `druidstream`:
```
`$$ ./bin/kafka-console-producer.sh --topic druidstream --bootstrap-server localhost:9093 < <path_to_this_location>/kafka_input.json
```

- Observe newly created supervisor, datasource on Druid UI: http://localhost:8888/

- Use Query tab on UI to select data from datasource: Example `SELECT * FROM "data_from_kafka"`

- Bring containers down using `docker-compose down`

### Reference

- Druid docker images, docker-compose yaml are from https://github.com/apache/druid/blob/master/distribution/docker/. Note: By default `druid-kafka-indexing-service` extension won't be loaded on above images. This has been added in `environment` file.

- Kafka and Zookeeper docker images from: https://hub.docker.com/r/bitnami/kafka/. Have made slight modifications to `docker-compose.yml`.

- Documents:
  - https://druid.apache.org/docs/latest/tutorials/tutorial-kafka.html
  - https://druid.apache.org/docs/latest/development/extensions-core/kafka-ingestion.html
