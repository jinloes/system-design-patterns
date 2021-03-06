# Create Postgres Connector
curl --location --request POST 'http://localhost:8083/connectors/' \
--header 'Content-Type: application/json' \
--data-raw '{
  "name": "users-connector",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "database.hostname": "postgres",
    "database.port": "5432",
    "database.user": "postgres",
    "database.password": "postgres",
    "database.dbname": "test_db",
    "database.server.name": "dbserver1",
    "table.include.list": "public.users",
    "plugin.name": "pgoutput",
    "transforms": "route",
    "transforms.route.type": "org.apache.kafka.connect.transforms.RegexRouter",
    "transforms.route.regex": "([^.]+)\\.([^.]+)\\.([^.]+)",
    "transforms.route.replacement": "$3"
  }
}'

# Create Elasticsearch Sink
curl --location --request POST 'http://localhost:8083/connectors/' \
--header 'Content-Type: application/json' \
--data-raw '{
  "name": "es-sink-users",
  "config": {
    "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
    "tasks.max": "1",
    "topics": "users",
    "connection.url": "http://elasticsearch:9200",
    "transforms": "unwrap,key",
    "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
    "transforms.unwrap.drop.tombstones": "false",
    "transforms.unwrap.drop.deletes": "false",
    "transforms.key.type": "org.apache.kafka.connect.transforms.ExtractField$Key",
    "transforms.key.field": "id",
    "key.ignore": "false",
    "type.name": "_doc",
    "behavior.on.null.values": "delete"
  }
}'


# List Connectors
curl --location --request GET 'http://localhost:8083/connectors'


# Create Users Index in Elasticsearch 
curl --location --request PUT 'http://localhost:9200/users' \
--header 'Content-Type: application/json' \
--data-raw '{
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
      "properties": {
        "id": {
          "type": "integer"
        },
        "email": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        }
    }
  }
}'

# Delete Connector
curl --location --request DELETE 'http://localhost:8083/connectors/es-sink-users'