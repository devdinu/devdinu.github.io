---
title: "Kafka Cheatsheet"
tags: ["cheatsheet", "kafka"]
categories:
    - kafka
---

# Kafka Commands

For instance of this cheatsheet,
- we'll use topic name `user-signups`
- consumer group id as `userMailGenerator`

## Generic

run this or add this to bashrc so you've the broker & zookeeper address as env vars so you can easily access it in other commands.
```
KAFKA_BIN=/opt/kafka/bin && export PATH=$PATH:$KAFKA_BIN
ZOOS=$(cat /opt/kafka/config/server.properties  | grep zoo | cut -d '=' -f 2)
KAFKAS=`hostname`:6667
```

## Increase replication factor
```
kafka-reassign-partitions.sh --zookeeper $ZOOS --reassignment-json-file increase-replication-factor.json --execute
kafka-topics.sh --describe --topic user-signups --zookeeper $ZOOS
kafka-topics.sh --zookeeper $ZOOS --alter --topic user-signups --config retention.ms=86400000 # 1 day retention

```

## Investigation

```
kafka-topics.sh --zookeeper $ZOOS --describe --topics-with-overrides

kafka-consumer-groups.sh --bootstrap-server $BOOTSTRAP_SERVERS --group userMailGenerator --describe
kafka-consumer-groups.sh --bootstrap-server $BOOTSTRAP_SERVERS --group userMailGenerator --delete
kafka-consumer-groups.sh --bootstrap-server $BOOTSTRAP_SERVERS  --list
kafka-consumer-groups.sh --zookeeper $ZOOS --group mainstream_mirror --describe


# complex need
kafka-console-consumer.sh --topic __consumer_offsets --bootstrap-server $KAFKAS --formatter "kafka.coordinator.GroupMetadataManager\$OffsetsMessageFormatter"

kafka-run-class kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic test-messages-01

```

## Adminstration

```
kafka-preferred-replica-election.sh --zookeeper $ZOOS
kafka-topics.sh  --zookeeper $ZOOS --alter --topic $TOPIC --partitions 24
kafka-topics.sh --topic $TOPIC --delete --zookeeper $ZOOS
kafka-topics.sh --zookeeper $ZOOS --alter --topic $TOPIC --config retention.bytes=1073741824

kafka-consumer-groups.sh --bootstrap-server $KAFKAS --group $GROUP_ID --reset-offsets --to-offset  123456 --execute --topic user-signups
kafka-consumer-groups.sh --bootstrap-server $KAFKAS --group $GROUP_ID --reset-offsets --all-topics --to-datetime=YYYY-MM-DDTHH:mm:SS.sss
kafka-consumer-groups.sh --bootstrap-server $KAFKAS --group $GROUP_ID --reset-offsets --to-datetime <YYYY-MM-DDTHH:mm:SS.sss>2019-11-25T05:30:00.000 --topic user-signups --execute
```

### Alter retention and compaction policy
```
kafka-topics.sh --zookeeper $ZOOS --alter --topic user-signups --config retention.bytes=5368709120
kafka-topics.sh --zookeeper $ZOOS --alter --topic user-signups --config retention.ms=2592000000
kafka-topics.sh --zookeeper $ZOOS --alter --topic user-singups --config cleanup.policy=compact,delete
```

ms Time reference:
```
1 day: 86400000
1 hour: 3600000
1 min : 60000

```

## Kafka partitions reassignment
**This would cause degradation on kafka cluster, you should reassign in batches and with bytes transfer limits**
```
kafka-reassign-partitions.sh --bootstrap-server $KAFKAS --zookeeper $ZOOS  --broker-list "1,2,3,4,5,6,7,8,9,10,11,12" --topics-to-move-json-file topics-to-move.json --generate
kafka-reassign-partitions.sh --bootstrap-server $KAFKAS --zookeeper $ZOOS --execute --reassignment-json-file proposal.json
```


## Helper

#### Update all topics with 12 partitions to have 24 partitions
```
kafka-topics.sh --describe  --zookeeper $ZOOS > /alltopics_description
less /tmp/alltopics_description  | grep  PartitionCount:12 |  awk '{ print $1 }' | cut -d ':' -f2 | xargs -n1 -I{} bash -c  \
"kafka-topics.sh  --zookeeper $ZOOS --alter --topic {} --partitions 24" > /tmp/increased_partitions.log
```
#### Delete all consumer groups from file
```
head /tmp/some_consumer_groups  | awk '{ print "kafka-consumer-groups.sh --bootstrap-server $(hostname -i):6667 --delete  --group " $0}'   | xargs -n1 -I {} bash -c '{}'
```

