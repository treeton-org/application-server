# Application server

Refactored copy of [evernode-ds](https://github.com/tonlabs/evernode-ds)

## Up local

```shell
docker network create traefik
docker compose --env-file .env.local up
```

## Docker network scheme

```mermaid
flowchart TD
    %% Labels
    traefik(traefik)
    tempo(tempo)
    arangodb(arangodb)
    zookeeper(zookeeper)
    kafka(kafka)
    schema-registry(schema-registry)
    connect(connect)
    ever-node(ever-node)

    %% Traefik
    traefik-->|traefik|arangodb
    traefik-->|traefik|tempo
    traefik-->|traefik|schema-registry

    %% Kafka
    kafka-->|kafka|zookeeper
    kafka-->|kafka|arangodb
    schema-registry-->|kafka|kafka
    connect-->|kafka|zookeeper
    connect-->|kafka|kafka
    connect-->|kafka|schema-registry

    %% EverNode
    ever-node-->|ever-node|kafka

subgraph Arangodb
    arangodb
end

subgraph Kafka
    zookeeper
    kafka
    schema-registry
    connect
end

subgraph EverNode
    ever-node
end
```

## Handmade

[kafka-connect-arangodb](https://www.confluent.io/hub/jaredpetersen/kafka-connect-arangodb) v1.0.4 downloaded manually and unzipped into `/docker/kafka-connect-arangodb` because have no exist any good solution. 
