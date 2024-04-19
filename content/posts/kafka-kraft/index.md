---
title: "Kafka without Zookeeper"
date: "2023-03-06"
description: "Installing Kafka with kRaft and SSL"
tags: [
    "kafka",
    "kraft",
]
draft: true
url: /archive/examplepost
type: blog
---

For the last couple of days I have been experimenting with Kafka without Zookeeper. Below is the installation braindump that I have been using to get a fully functional multinode cluster using kRaft.
The biggest hurdle as always with java applications is working with the keystores to get everything correct.

Kafka cluster setup is 3 VMs running Debian 11 with 4 CPUs and 16GB Ram


## Installing Java JDK 11

```bash
apt-get install openjdk-jre-headless-11
```

Upon completion, you should get a similar output when doing `java -version`:
```bash
openjdk 11.0.18 2023-01-17
OpenJDK Runtime Environment (build 11.0.18+10-post-Debian-1deb11u1)
OpenJDK 64-Bit Server VM (build 11.0.18+10-post-Debian-1deb11u1, mixed mode, sharing)
```

## Create a kafka user and directories

We are doing this so that we dont have to run kafka as root or as your normal user.

```bash
useradd --system kafka
groupadd kafka
usermod -a -G kafka kafka
```


## Installing Kafka

Download and extract the kafka binaries

```bash
curl -LO https://downloads.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz /tmp/kafka_2.13-3.4.0.tgz
tar -zxf kafka_2.13-3.4.0.tgz
mv kafka_2.13-3.4.0 /opt/kafka
```

Change owner of the kafka directory to  the kafka user
```bash
chown -R kafka:kafka /opt/kafka
```


## Create certificates and keystore
TODO

## Kafka configuration

kafka0.properties
```bash
process.roles=broker,controller
node.id=0
controller.quorum.voters=0@kafka0.ventris.local:9093,1@kafka0.ventris.local:9093,2@kafka0.ventris.local:9093
listeners=SSL://kafka0.ventris.local:9092,CONTROLLER://kafka0.ventris.local:9093
inter.broker.listener.name=SSL
advertised.listeners=SSL://kafka0.ventris.local:9092
controller.listener.names=CONTROLLER
early.start.listeners=CONTROLLER
listener.security.protocol.map=CONTROLLER:SSL,SSL:SSL,PLAINTEXT:PLAINTEXT
ssl.client.auth=required
ssl.principal.mapping.rules=RULE:^CN=(.*?)$/$1/,RULE:^CN=(.*?),.*$/$1/,DEFAULT
authorizer.class.name=org.apache.kafka.metadata.authorizer.StandardAuthorizer
ssl.keystore.location=/opt/kafka/kafka.server.keystore.jks
ssl.keystore.password=password
ssl.truststore.type=PKCS12
ssl.truststore.location=/opt/kafka/kafka.server.truststore.jks
ssl.truststore.password=password
ssl.enabled.protocols:TLSv1.2
security.protocol=SSL
super.users=User:kafka0.ventris.local;User:kafka1.ventris.local;User:kafka2.ventris.local
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
log.dirs=/opt/kafka/data
num.partitions=1
num.recovery.threads.per.data.dir=1
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
log.retention.hours=168
log.segment.bytes=1073741824
```

kafka1.properties
```bash
process.roles=broker,controller
node.id=1
controller.quorum.voters=0@kafka0.ventris.local:9093,1@kafka1.ventris.local:9093,2@kafka2.ventris.local:9093
listeners=SSL://kafka1.ventris.local:9092,CONTROLLER://kafka1.ventris.local:9093
inter.broker.listener.name=SSL
advertised.listeners=SSL://kafka1.ventris.local:9092
controller.listener.names=CONTROLLER
early.start.listeners=CONTROLLER
listener.security.protocol.map=CONTROLLER:SSL,SSL:SSL,PLAINTEXT:PLAINTEXT
ssl.client.auth=required
ssl.principal.mapping.rules=RULE:^CN=(.*?)$/$1/,RULE:^CN=(.*?),.*$/$1/,DEFAULT
authorizer.class.name=org.apache.kafka.metadata.authorizer.StandardAuthorizer
ssl.keystore.location=/opt/kafka/kafka.server.keystore.jks
ssl.keystore.password=password
ssl.truststore.type=PKCS12
ssl.truststore.location=/opt/kafka/kafka.server.truststore.jks
ssl.truststore.password=password
ssl.enabled.protocols:TLSv1.2
security.protocol=SSL
super.users=User:kafka0.ventris.local;User:kafka1.ventris.local;User:kafka2.ventris.local
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
log.dirs=/opt/kafka/data
num.partitions=1
num.recovery.threads.per.data.dir=1
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
log.retention.hours=168
log.segment.bytes=1073741824
```

kafka2.properties
```bash
process.roles=broker,controller
node.id=2
controller.quorum.voters=0@kafka0.ventris.local:9093,1@kafka1.ventris.local:9093,2@kafka2.ventris.local:9093
listeners=SSL://kafka2.ventris.local:9092,CONTROLLER://kafka2.ventris.local:9093
inter.broker.listener.name=SSL
advertised.listeners=SSL://kafka2.ventris.local:9092
controller.listener.names=CONTROLLER
early.start.listeners=CONTROLLER
listener.security.protocol.map=CONTROLLER:SSL,SSL:SSL,PLAINTEXT:PLAINTEXT
ssl.client.auth=required
ssl.principal.mapping.rules=RULE:^CN=(.*?)$/$1/,RULE:^CN=(.*?),.*$/$1/,DEFAULT
authorizer.class.name=org.apache.kafka.metadata.authorizer.StandardAuthorizer
ssl.keystore.location=/opt/kafka/kafka.server.keystore.jks
ssl.keystore.password=password
ssl.truststore.type=PKCS12
ssl.truststore.location=/opt/kafka/kafka.server.truststore.jks
ssl.truststore.password=password
ssl.enabled.protocols:TLSv1.2
security.protocol=SSL
super.users=User:kafka0.ventris.local;User:kafka1.ventris.local;User:kafka2.ventris.local
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
log.dirs=/opt/kafka/data
num.partitions=1
num.recovery.threads.per.data.dir=1
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
log.retention.hours=168
log.segment.bytes=1073741824
```

## Prometheus monitoring of Kafka
TODO

## Kafka systemd service files

```bash
[Unit]
Description=Apache Kafka server
Documentation=http://kafka.apache.org/documentation.html
Requires=network.target
After=network.target

[Service]
Type=simple
User=kafka
Group=kafka
LimitNOFILE=300000
LimitFSIZE=infinity
Restart=on-failure
Environment=JMX_PORT=9999
Environment="KAFKA_OPTS=-javaagent:/opt/kafka/jmx_prometheus_javaagent-0.17.2.jar=7071:/opt/kafka/kafka-metrics.yml -Dcom.sun.management.jmxremote.host=localhost"
Environment="KAFKA_HEAP_OPTS=-Xmx8G -Xms8G"
ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/kraft/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh

[Install]
WantedBy=multi-user.target
```
