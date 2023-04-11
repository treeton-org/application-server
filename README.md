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
    %% Traefik
    traefik(traefik)-->|traefik|tempo(tempo)
    traefik(traefik)-->|traefik|arangodb(arangodb)

subgraph Arangodb
    arangodb(arangodb)
end

subgraph Grafana
    tempo(tempo)
end
```