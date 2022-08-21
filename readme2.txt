# create topic
docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --create --topic first_topic --command-config /opt/client-properties/adminclient.properties --partitions 1 --replication-factor 1
# list topics
docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --list
# add acl producer
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic first_topic --allow-principal User:producer --producer --add
# add acl consumer
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic first_topic --allow-principal User:consumer --consumer --add --group "*"

#run producer
docker exec -it kafka-docker-security-acls-kafka-1 kafka-console-producer --broker-list localhost:9093 --producer.config /opt/client-properties/producer.properties --topic first_topic
#run consumer
docker exec -it kafka-docker-security-acls-kafka-1 kafka-console-consumer --bootstrap-server localhost:9093 --consumer.config /opt/client-properties/consumer.properties --group test-consumer-group --topic first_topic

# add acl exporter
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --allow-principal User:exporter --operation describe --add --group "*" --topic "*" --cluster "*"
# add acl kowl
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --allow-principal User:kowl --operation describe --add --group "*" --topic "*" --cluster "*"
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --allow-principal User:kowl --operation DescribeConfigs --add --topic "*" --cluster "*"
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --allow-principal User:kowl --operation Read --add --topic "*"

# create topic
docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --create --topic docker-connect-configs --command-config /opt/client-properties/adminclient.properties --partitions 1 --replication-factor 1
docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --create --topic docker-connect-offsets --command-config /opt/client-properties/adminclient.properties --partitions 1 --replication-factor 1
docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --create --topic docker-connect-status --command-config /opt/client-properties/adminclient.properties --partitions 1 --replication-factor 1
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic docker-connect-configs --allow-principal User:connect --producer --add
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic docker-connect-configs --allow-principal User:connect --consumer --add --group "compose-connect-group"
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic docker-connect-offsets --allow-principal User:connect --producer --add
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic docker-connect-offsets --allow-principal User:connect --consumer --add --group "compose-connect-group"
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic docker-connect-status --allow-principal User:connect --producer --add
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic docker-connect-status --allow-principal User:connect --consumer --add --group "compose-connect-group"

docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --create --topic demo --command-config /opt/client-properties/adminclient.properties --partitions 1 --replication-factor 1
docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --create --topic demo-dbhistory --command-config /opt/client-properties/adminclient.properties --partitions 1 --replication-factor 1
docker exec -it kafka-docker-security-acls-kafka-1 kafka-topics --bootstrap-server localhost:9093 --create --topic demo.dbo.ORDERS --command-config /opt/client-properties/adminclient.properties --partitions 1 --replication-factor 1
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic demo --allow-principal User:connect --producer --add
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic demo --allow-principal User:connect --consumer --add --group "compose-connect-group"
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic demo-dbhistory --allow-principal User:connect --producer --add
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic demo-dbhistory --allow-principal User:connect --consumer --add --group "demo-dbhistory"
docker exec -it kafka-docker-security-acls-kafka-1 kafka-acls --bootstrap-server localhost:9093 --command-config /opt/client-properties/adminclient.properties --topic demo.dbo.ORDERS --allow-principal User:connect --producer --add



docker exec -it kafka-connect-01 bash
cat << EOF > conn3.json
{
  "name": "conn3",
  "config": {
    "connector.class": "io.debezium.connector.sqlserver.SqlServerConnector",
    "database.history.producer.sasl.mechanism": "PLAIN",
    "database.history.producer.sasl.jaas.config": "org.apache.kafka.common.security.plain.PlainLoginModule required username=\"connect\" password=\"connect-secret\";",
    "database.user": "sa",
    "database.dbname": "demo",
    "database.history.consumer.sasl.jaas.config": "org.apache.kafka.common.security.plain.PlainLoginModule required username=\"connect\" password=\"connect-secret\";",
    "database.history.producer.security.protocol": "SASL_PLAINTEXT",
    "database.history.kafka.bootstrap.servers": "kafka:9092",
    "database.history.kafka.topic": "demo-dbhistory",
    "database.server.name": "demo",
    "offset.flush.timeout.ms": "15000",
    "database.history.consumer.security.protocol": "SASL_PLAINTEXT",
    "database.port": "1433",
    "decimal.handling.mode": "double",
    "database.hostname": "mssql",
    "database.password": "Admin123",
    "poll.interval.ms": "5000",
    "table.include.list": "dbo.ORDERS",
    "database.history.consumer.sasl.mechanism": "PLAIN",
    "snapshot.isolation.mode": "read_uncommitted"
  }
}
EOF
curl -X POST -H "Content-Type: application/json" -H "Accept: application/json" -d @conn3.json http://localhost:8083/connectors

docker exec --tty --interactive mssql bash -c '/opt/mssql-tools/bin/sqlcmd -l 30 -S localhost -U sa -P $SA_PASSWORD'
use demo;
insert into demo.dbo.ORDERS (order_id, customer_id, order_ts, order_total_usd, item) values (9, 2, '2019-11-28T10:27:19Z', '3.67', '=============== Me ================');
go
