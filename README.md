# Application server

Refactored copy of [evernode-ds](https://github.com/tonlabs/evernode-ds)

## Up local

```shell
docker network create traefik
docker compose --env-file .env.local up
```

## Docker network scheme

* `- - -` lines - `traefik` docker network 
* `—————` lines - `application` docker network

```mermaid
flowchart TD
    subgraph Metrics
        VictoriaMetrics
        NodeExporter
        Grafana
    end

        subgraph VictoriaMetrics
            vmagent(vmagent)
            victoria-metrics(victoria-metrics)
            vmagent---victoria-metrics
        end

        subgraph Grafana
            tempo(tempo)
            grafana(grafana)
            tempo---grafana
        end

        subgraph NodeExporter
            node-exporter(node-exporter)
            node-exporter
        end

    subgraph Application
        Kafka
        QueryServer
        Blockchain
    end

        subgraph Kafka
            zookeeper(zookeeper)
            kafka(kafka)
            schema-registry(schema-registry)
            connect(connect)
            zookeeper---kafka
            connect---zookeeper & kafka
            schema-registry---kafka & connect
        end

        subgraph QueryServer
            q-server(q-server)
            arangodb(arangodb)
            q-server---arangodb
        end

        subgraph Blockchain
            statsd(statsd)
            ever-node(ever-node)
            blockchain{Blockchain}
            statsd---ever-node---|ADNL/udp|blockchain
        end


    user((User))
    traefik(traefik)

    user---|HTTP|traefik
    traefik-.-arangodb & tempo & schema-registry & vmagent & victoria-metrics & q-server & statsd & node-exporter & grafana
    kafka---ever-node
    connect---arangodb
    q-server---kafka
    vmagent-----grafana & statsd & node-exporter & arangodb
    victoria-metrics---grafana
```

Main workflow reading data from blockchain
```mermaid
flowchart TD
    subgraph Metrics
        VictoriaMetrics
        NodeExporter
        Grafana
    end

        subgraph VictoriaMetrics
            vmagent(vmagent)
            victoria-metrics(victoria-metrics)
            vmagent~~~victoria-metrics
        end

        subgraph Grafana
            tempo(tempo)
            grafana(grafana)
            tempo~~~grafana
        end

        subgraph NodeExporter
            node-exporter(node-exporter)
            node-exporter
        end

    subgraph Application
        Kafka
        QueryServer
        Blockchain
    end

        subgraph Kafka
            zookeeper(zookeeper)
            kafka(kafka)
            schema-registry(schema-registry)
            connect(connect)
            zookeeper~~~kafka
            connect~~~zookeeper
            connect===|3. Read data from kafka|kafka
            schema-registry~~~kafka & connect
        end

        subgraph QueryServer
            q-server(q-server)
            arangodb(arangodb)
            q-server---|7. Read from database|arangodb
        end

        subgraph Blockchain
            statsd(statsd)
            ever-node(ever-node)
            blockchain{Blockchain}
            statsd~~~ever-node---|1. ADNL/udp|blockchain
        end


    user((User))
    traefik(traefik)

    user---|5. HTTP query|traefik
    traefik~~~arangodb & tempo & schema-registry & vmagent & victoria-metrics & statsd & node-exporter & grafana
    traefik---|6. Proxy redirect|q-server
    kafka---|2. Parsed data: blocks, accounts, messages, transaction|ever-node
    connect---|4. Store data|arangodb
    q-server~~~kafka
    vmagent~~~~~grafana & statsd & node-exporter & arangodb
    victoria-metrics~~~grafana
```

## Handmade

`docker/kafka-connect-arangodb` files compiled from [kafka-connect-arangodb](https://github.com/tonlabs/kafka-connect-arangodb) and placed manually into directory 
