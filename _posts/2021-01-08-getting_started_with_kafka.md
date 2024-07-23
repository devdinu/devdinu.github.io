---
title: "Getting started with Kafka"
tags: ["language", "kickstart", "kafka", "backend"]
toc: true
---

# Introduction

[Kafka](https://kafka.apache.org/) is distributed event streaming platform. You can adopt event driven architecture which helps for high scale systems. This’s similar pubsub model.

![kafka pubsub](https://storage.googleapis.com/revoir-storage/wiki/kafka-pubsub.png)

In this case `p1,p2` are producers, `c1,c2,ci` are consumers. Group of consumers can consume from same topic and kafka will enable assign/split data to consumers so it’s not processed twice.

# Running Kafka

Install kafka (zookeeper’ll be installed as dependency), and start it locally
```
brew reinstall kafka kafkacat
brew services start kafka
```  

## Verify with metadata

[kafkacat](https://github.com/edenhill/kafkacat) is a tool which you can use to produce and consume messages from kafka.

run the following command to get the cluster metadata of topic `__consumer-offsets`

```  
kafkacat -L -b localhost:9092 -t __consumer-offsets
```  

```
Metadata for __consumer-offsets (from broker -1: localhost:9092/bootstrap):
 1 brokers:
  broker 0 at ip:9092 (controller)
 1 topics:
  topic "__consumer-offsets" with 10 partitions:
    partition 0, leader 0, replicas: 0, isrs: 0
    partition 1, leader 0, replicas: 0, isrs: 0
    partition 2, leader 0, replicas: 0, isrs: 0
    partition 3, leader 0, replicas: 0, isrs: 0
    partition 4, leader 0, replicas: 0, isrs: 0
    partition 5, leader 0, replicas: 0, isrs: 0
    partition 6, leader 0, replicas: 0, isrs: 0
    partition 7, leader 0, replicas: 0, isrs: 0
    partition 8, leader 0, replicas: 0, isrs: 0
    partition 9, leader 0, replicas: 0, isrs: 0
```

The total partitions/result could differ based on default config. currently we’ve 1 broker in cluster.

# Publishing messages
```
kafkacat -P -b localhost:9092 -t sample-events
```  
You can alternatively do the same with kafka-consule-consumer. ensure `/usr/local/bin` is added to PATH env variable.

```  
kafka-console-producer --bootstrap-server localhost:9092 --topic sample-events
```    

# Consuming messages
```
kafkacat -C -b localhost:9092 -t sample-events -o beginning
```  

Use the following to run with kafka-console-producer
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic sample-events
```  
The demo have top 2 panes, which have 2 producers producing to same topic `sample-events` and two different consumers on bottom pane consuming events from the topic. ![kafka consumer producer demo](https://storage.googleapis.com/revoir-storage/wiki/publisher-subscriber.gif)
If your platform is linux based tools would be shell files, so use `kafka-console-producer.sh` instead of `kafka-console-producer`

# Communities
Join kafka communities to learn from kafka enthusiasts
*   [kafka mailing list](https://kafka.apache.org/contact)
*   [confluent kafka slack community](https://launchpass.com/confluentcommunity?src=DP)
*   join nearby [meetup groups](https://www.meetup.com/topics/apache-kafka/)

# [Articles](Articles)
*   [Official kafka documentation](https://kafka.apache.org/documentation/#gettingStarted)
