# Opis środowiska

## Wprowadzenie

Data Backbone wykorzystuje Docker do zarządzania środowiskiem deweloperskim i produkcyjnym. Plik `docker-compose.yaml` definiuje wszystkie niezbędne usługi i ich konfiguracje. Poniżej znajduje się szczegółowy opis każdej usługi i jej roli w systemie.

## Struktura docker-compose

### Volumes

Zdefiniowane są następujące woluminy:

- `hd_shared`: Współdzielony wolumin dla danych aplikacji.

### Services

#### vscode

Usługa `vscode` zapewnia środowisko programistyczne dostępne przez przeglądarkę.

```yaml
vscode:
  container_name: vscode
  image: codercom/code-server:latest
  ports:
    - '3991:8080'
  volumes:
    - hd_shared:/home/coder/project:rw
    - /var/run/docker.sock:/var/run/docker.sock
  environment:
    - 'PASSWORD=PSWD'
    - 'DOCKER_USER=$USER'
  user: '0:0'
  depends_on:
    - server
  networks:
    - external
```

- Port `3991` jest mapowany na port `8080` kontenera.
- Współdzielony wolumin `hd_shared` jest montowany w `/home/coder/project`.
- Usługa wymaga uruchomienia usługi `server`.

#### server

Usługa `server` to główna aplikacja Data Backbone.

```yaml
server:
  build:
    context: .
    dockerfile: ./app.Dockerfile
  env_file:
    - ./app/.env
  ports:
    - '8000:8000'
  volumes:
    - hd_shared:/app
  environment:
    MONGO_URI: ${MONGO_URI}
    WEAVIATE_URL: weaviate
    TOKEN: ${TOKEN}
    CORS_ORIGIN: '${CORS_ORIGIN}'
    OPENAI_KEY: ${OPENAI_KEY}
    STRIPE_KEY: ${STRIPE_KEY}
    CLICKUP_KEY: ${CLICKUP_KEY}
    FAKTUROWNIA_TOKEN: ${FAKTUROWNIA_TOKEN}
  depends_on:
    - mongo
    - mongo-admin
    - weaviate
```

- Port `8000` jest mapowany na port `8000` kontenera.
- Współdzielony wolumin `hd_shared` jest montowany w `/app`.
- Usługa wymaga uruchomienia usług `mongo`, `mongo-admin` i `weaviate`.

#### weaviate

Usługa `weaviate` to baza danych wektorowa używana w systemie.

```yaml
weaviate:
  image: cr.weaviate.io/semitechnologies/weaviate:latest
  restart: on-failure:0
  logging:
    driver: 'none'
  ports:
    - '8080:8080'
    - '50051:50051'
  environment:
    LOG_LEVEL: 'debug'
    QUERY_DEFAULTS_LIMIT: 1000
    ASYNC_INDEXING: 'true'
    AUTHENTICATION_ANONYMOUS_ACCESS_ENABLED: 'true'
    DEFAULT_VECTORIZER_MODULE: 'text2vec-openai'
    ENABLE_MODULES: 'text2vec-openai'
    PERSISTENCE_DATA_PATH: '/var/lib/weaviate'
    CLUSTER_HOSTNAME: 'node1'
```

- Porty `8080` i `50051` są mapowane na te same porty kontenera.
- Usługa jest skonfigurowana do używania modułu `text2vec-openai`.

#### mongo

Usługa `mongo` to baza danych MongoDB używana w systemie.

```yaml
mongo:
  image: mongo:4.4
  ports:
    - '27017:27017'
  logging:
    driver: 'none'
  environment:
    MONGO_INITDB_ROOT_USERNAME: root
    MONGO_INITDB_ROOT_PASSWORD: example
```

- Port `27017` jest mapowany na port `27017` kontenera.

#### mongo-admin

Usługa `mongo-admin` to interfejs administracyjny dla MongoDB.

```yaml
mongo-admin:
  image: mongo-express:latest
  ports:
    - '8081:8081'
  logging:
    driver: 'none'
  depends_on:
    - mongo
  environment:
    ME_CONFIG_MONGODB_ADMINUSERNAME: root
    ME_CONFIG_MONGODB_ADMINPASSWORD: example
    ME_CONFIG_MONGODB_URL: mongodb://root:example@mongo:27017/
```

- Port `8081` jest mapowany na port `8081` kontenera.
- Usługa wymaga uruchomienia usługi `mongo`.

## Podsumowanie

Plik `docker-compose.yaml` definiuje kompletne środowisko dla Data Backbone, zawierające wszystkie niezbędne usługi: serwer aplikacji, bazę danych MongoDB, bazę danych wektorową Weaviate oraz narzędzia deweloperskie. Dzięki tej konfiguracji możesz łatwo uruchomić cały system jednym poleceniem, co znacznie upraszcza proces rozwoju i wdrażania aplikacji.
