# Kafka Commands

## Increase replication factor:
```
kafka-reassign-partitions.sh --zookeeper $ZOOS --reassignment-json-file increase-replication-factor.json --execute
kafka-topics.sh --describe --topic kafka-topic-to-alter --zookeeper $ZOOS

kafka-run-class kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic test-messages-01
```


## Kafka partitions reassignment
```
kafka-reassign-partitions.sh --bootstrap-server $KAFKAS --zookeeper $ZOOS  --broker-list "1,2,3,4,5,6,7,8,9,10,11,12" --topics-to-move-json-file topics-to-move.json --generate
kafka-reassign-partitions.sh --bootstrap-server $KAFKAS --zookeeper $ZOOS --execute --reassignment-json-file proposal.json
```


## Adminstration

```
kafka-preferred-replica-election.sh --zookeeper $ZOOS
kafka-topics.sh  --zookeeper $ZOOS --alter --topic $TOPIC --partitions 24
kafka-consumer-groups.sh --bootstrap-server $KAFKAS --group $GROUP_ID --reset-offsets --all-topics --to-datetime=YYYY-MM-DDTHH:mm:SS.sss

```

## Investigation

```
kafka-topics.sh --zookeeper $ZOOS --describe --topics-with-overrides

kafka-consumer-groups.sh --bootstrap-server $BOOTSTRAP_SERVERS --group frontingConsumers --describe
kafka-consumer-groups.sh --zookeeper $ZOOS --group mainstream_mirror --describe

kafka-consumer-groups.sh --group group-name --bootstrap-server $KAFKAS  --reset-offsets --to-offset  123456 --execute --topic kafka-topic-to-alter

```

## Generic

```
KAFKA_BIN=/opt/kafka/bin && export PATH=$PATH:$KAFKA_BIN
ZOOS=$(cat /opt/kafka/config/server.properties  | grep zoo | cut -d '=' -f 2)
KAFKAS=`hostname`:6667
```

## Helper
```
kafka-topics.sh --describe  --zookeeper $ZOOS > /alltopics_description 
less /tmp/alltopics_description  | grep  PartitionCount:12 |  awk '{ print $1 }' | cut -d ':' -f2 | xargs -n1 -I{} bash -c  \
"kafka-topics.sh  --zookeeper $ZOOS --alter --topic {} --partitions 24" > /tmp/increased_partitions.log
```
