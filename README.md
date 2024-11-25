
# Kafka and Zookeeper Multi-Node Cluster Setup Using Docker

This README provides the steps to set up a multi-node Kafka and Zookeeper cluster using Docker. The configuration includes three Zookeeper nodes and three Kafka brokers for high availability and fault tolerance.

---

## Prerequisites

- Ensure Docker is installed and running.
- Host IP address: `192.168.193.73`.

---

## Zookeeper Setup

### Zookeeper Node 1
```bash
docker run --name=zk1 -d -p 22181:2181 \
-e ALLOW_ANONYMOUS_LOGIN=yes \
-e ZOOKEEPER_SERVER_ID=1 \
-e ZOOKEEPER_CLIENT_PORT=22181 \
-e ZOOKEEPER_TICK_TIME=2000 \
-e ZOOKEEPER_INIT_LIMIT=5 \
-e ZOOKEEPER_SYNC_LIMIT=2 \
-e ZOOKEEPER_SERVERS="192.168.193.73:2888:3888;192.168.193.73:2888:3888;192.168.193.73:2888:3888" \
bitnami/zookeeper:latest
```

### Zookeeper Node 2
```bash
docker run --name=zk2 -d -p 32181:2181 \
-e ALLOW_ANONYMOUS_LOGIN=yes \
-e ZOOKEEPER_SERVER_ID=2 \
-e ZOOKEEPER_CLIENT_PORT=32181 \
-e ZOOKEEPER_TICK_TIME=2000 \
-e ZOOKEEPER_INIT_LIMIT=5 \
-e ZOOKEEPER_SYNC_LIMIT=2 \
-e ZOOKEEPER_SERVERS="192.168.193.73:2888:3888;192.168.193.73:2888:3888;192.168.193.73:2888:3888" \
bitnami/zookeeper:latest
```

### Zookeeper Node 3
```bash
docker run --name=zk3 -d -p 42181:2181 \
-e ALLOW_ANONYMOUS_LOGIN=yes \
-e ZOOKEEPER_SERVER_ID=3 \
-e ZOOKEEPER_CLIENT_PORT=42181 \
-e ZOOKEEPER_TICK_TIME=2000 \
-e ZOOKEEPER_INIT_LIMIT=5 \
-e ZOOKEEPER_SYNC_LIMIT=2 \
-e ZOOKEEPER_SERVERS="192.168.193.73:2888:3888;192.168.193.73:2888:3888;192.168.193.73:2888:3888" \
bitnami/zookeeper:latest
```

---

## Kafka Setup

### Kafka Broker 1
```bash
docker run --name kafka1 -d -p 9091:9092 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.193.73:22181,192.168.193.73:32181,192.168.193.73:42181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.193.73:9091 \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-e KAFKA_BROKER_ID=1 \
-e KAFKA_LOG_CLEANUP_POLICY=compact,delete \
-e KAFKA_DEFAULT_REPLICATION_FACTOR=3 \
-e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=3 \
bitnami/kafka
```

### Kafka Broker 2
```bash
docker run --name kafka2 -d -p 9095:9092 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.193.73:22181,192.168.193.73:32181,192.168.193.73:42181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.193.73:9095 \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-e KAFKA_BROKER_ID=2 \
-e KAFKA_LOG_CLEANUP_POLICY=compact,delete \
-e KAFKA_DEFAULT_REPLICATION_FACTOR=3 \
-e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=3 \
bitnami/kafka
```

### Kafka Broker 3
```bash
docker run --name kafka3 -d -p 9094:9092 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.193.73:22181,192.168.193.73:32181,192.168.193.73:42181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.193.73:9094 \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-e KAFKA_BROKER_ID=3 \
-e KAFKA_LOG_CLEANUP_POLICY=compact,delete \
-e KAFKA_DEFAULT_REPLICATION_FACTOR=3 \
-e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=3 \
bitnami/kafka
```

---

## Kafka Topic Management

### List Topics
```bash
kafka-topics.sh --list --bootstrap-server 192.168.193.73:9091,192.168.193.73:9095,192.168.193.73:9094
```

### Describe a Topic
```bash
kafka-topics.sh --describe --topic demo --bootstrap-server 192.168.193.73:9091,192.168.193.73:9094,192.168.193.73:9095
```

### Create a Topic
```bash
kafka-topics.sh --create --topic demo \
--bootstrap-server 192.168.193.73:9091,192.168.193.73:9095,192.168.193.73:9094 \
--replication-factor 3 --partitions 6
```

### Delete a Topic
```bash
kafka-topics.sh --delete --topic demo \
--bootstrap-server 192.168.193.73:9091,192.168.193.73:9095,192.168.193.73:9094
```

---

## Notes
- Ensure all containers are running before executing Kafka commands.
- Modify host IP and ports if running on a different machine or network.

---

## Video Link
https://youtu.be/BgWbjs4F2BA?si=k-1wa1aSkuYv8kP2

This setup provides a distributed Kafka cluster backed by Zookeeper, with fault tolerance and scalability.
