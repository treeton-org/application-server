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
            vmagent(Vmagent)
            victoria-metrics(Victoria Metrics)
            vmagent---victoria-metrics
        end

        subgraph Grafana
            tempo(Tempo)
            grafana(Grafana)
            tempo---grafana
        end

        subgraph NodeExporter
            node-exporter(Node exporter)
            node-exporter
        end

    subgraph Application
        Kafka
        QueryServer
        Blockchain
    end

        subgraph Kafka
            zookeeper(Zookeeper)
            kafka(Kafka)
            schema-registry(Schema Registry)
            connect(Connect)
            zookeeper---kafka
            connect---zookeeper & kafka
            schema-registry---kafka & connect
        end

        subgraph QueryServer
            q-server(q-server)
            arangodb[(ArangoDB)]
            q-server---arangodb
        end
        style QueryServer color:transparent,stroke:transparent

        subgraph Blockchain
            statsd(StatsD)
            ever-node(ever-node)
            blockchain{Blockchain}
            statsd---ever-node---|ADNL/udp|blockchain
        end


    user((User))
    traefik(Traefik)

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
            vmagent(Vmagent)
            victoria-metrics(Victoria Metrics)
            vmagent~~~victoria-metrics
        end

        subgraph Grafana
            tempo(Tempo)
            grafana(Grafana)
            tempo~~~grafana
        end

        subgraph NodeExporter
            node-exporter(Node exporter)
            node-exporter
        end

    subgraph Application
        Kafka
        QueryServer
        Blockchain
    end

        subgraph Kafka
            zookeeper(Zookeeper)
            kafka(Kafka)
            schema-registry(Schema Registry)
            connect(Connect)
            zookeeper~~~kafka
            connect~~~zookeeper
            connect===|3. Read data from kafka|kafka
            schema-registry~~~kafka & connect
        end

        subgraph QueryServer
            q-server(q-server)
            arangodb[(ArangoDB)]
           q-server---|7. Read from database|arangodb
        end
        style QueryServer color:transparent,stroke:transparent

        subgraph Blockchain
            statsd(StatsD)
            ever-node(ever-node)
            blockchain{Blockchain}
            statsd~~~ever-node---|1. ADNL/udp|blockchain
        end


    user((User))
    traefik(Traefik)

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
