# Apache Kafka – Complete Guide

## Table of Contents
1. [Overview](#overview)  
2. [Kafka Architecture](#kafka-architecture)  
   - [Kafka Cluster Components](#kafka-cluster-components)  
   - [Kafka Cluster Diagram](#kafka-cluster-diagram)  
3. [Topics, Partitions, and Offsets](#topics-partitions-and-offsets)  
   - [__consumer_offsets](#consumer_offsets)  
4. [Producers](#producers)  
   - [Producer with Key vs Without Key](#producer-with-key-vs-without-key)  
5. [Consumers](#consumers)  
   - [Consumer Groups and Partition Assignment](#consumer-groups-and-partition-assignment)  
   - [Consumer Crash & Reprocessing Timeline](#consumer-crash--reprocessing-timeline)  
   - [JoinGroup, SyncGroup, Heartbeats Flow](#joingroup-syncgroup-heartbeats-flow)  
6. [Replication & Fault Tolerance](#replication--fault-tolerance)  
   - [Replication Factor](#replication-factor)  
   - [ISR (In-Sync Replicas)](#isr-in-sync-replicas)  
7. [Commit Log, Segments & Retention](#commit-log-segments--retention)  
8. [Kafka Connect](#kafka-connect)  
9. [Kafka Streams](#kafka-streams)  
10. [Kafka CLI Commands](#kafka-cli-commands)  
11. [Spring Boot Kafka Example](#spring-boot-kafka-example)  
12. [Final Summary](#final-summary)
13. [Kafka Interview Pro Tips](#kafka-interview-pro-tips)

---

## Overview
Apache Kafka is a **distributed system** for sending, storing, and reading messages in real time. It is widely used to build **fast, reliable, and scalable data pipelines**.

✅ **Key Points:**  
- High throughput  
- Fault-tolerant  
- Scalable  
- Real-time processing  

---

## Kafka Architecture

### Kafka Cluster Components
- **Kafka Broker** – Individual server storing topic partitions and handling producer/consumer requests.  
- **Controller Broker** – One broker manages partition leadership among brokers.  
- **ZooKeeper (older versions)** – Manages cluster metadata, leader election, and broker health.  
- **KRaft Mode (newer versions)** – Raft-based internal quorum replaces ZooKeeper.  

**Key Notes:**  
1. Each broker has a unique **broker ID**.  
2. Clusters usually have multiple brokers → enables replication and fault tolerance.  
3. One broker acts as **Controller**.  
4. Metadata coordination: ZooKeeper (old) or KRaft (new).  

---

### Kafka Cluster Diagram
```text
                           Kafka Cluster
                                 |
             ----------------------------------------
            |                  |                     |
     Kafka Broker 1       Kafka Broker 2       Kafka Broker 3
       (Controller)                               
            |                  |                     |
       -------------      -------------         -------------
      | Partition 1 |(L)| Partition 1 |(F)   | Partition 1 |(F)
      | Partition 2 |(F)| Partition 2 |(L)   | Partition 2 |(F)
       -------------
```

Legend: (L) → Leader, (F) → Follower

Message Flow:

- Producers → send to Leader Partition

- Consumers → read from Leader Partition

- Followers replicate leader for fault tolerance

---

## Topics, Partitions, and Offsets
- Topic – Logical channel storing related messages.

- Partition – Ordered log of messages, each identified by a unique offset.

- Replication – Partitions are replicated across brokers to ensure fault tolerance.

Old Kafka: uses ZooKeeper for metadata/leader election
New Kafka: uses KRaft for internal metadata management


###  `__consumer_offsets`
- Built-in topic storing last committed offsets for each partition.

- Enables fault tolerance and replayability.

Example:

```text

__consumer_offsets (50 partitions)
│
├── Partition 12 → Group1
│      ├─ (Group1, TopicA, P0) → offset 150
│      ├─ (Group1, TopicA, P1) → offset 245
│      └─ (Group1, TopicA, P2) → offset 98
│
├── Partition 27 → Group2
       ├─ (Group2, TopicA, P0) → offset 120
       ├─ (Group2, TopicA, P1) → offset 250
       └─ (Group2, TopicA, P2) → offset 89
```
---

## Producers
- Sends messages to topic partitions (Leader).

- Leader replicates to followers for fault tolerance.

- Producer is notified when write succeeds.

## Producer with Key vs Without Key
  Without Key (Round-Robin)

```text

Message1 -> P0
Message2 -> P1
Message3 -> P2
Message4 -> P0
```
- ✅ Good load balancing

- ⚠️ Order not guaranteed across partitions

With Key (Hash-based)

```text
Copy code
Key = customer123
Hash(Key) -> Partition 2
All messages with same key -> Partition 2
```
- ✅ Guarantees order for messages with same key

---

## Consumers
- Subscribe to topics and read sequentially by offset.

- Offsets track progress → supports fault tolerance.
  
---

## Consumer Groups and Partition Assignment
Example:

```text
TopicA: P0, P1, P2, P3
Group1: C1-1, C1-2
Group2: C2-1, C2-2

Assignments:

Group1:
C1-1 -> P0, P1
C1-2 -> P2, P3

Group2:
C2-1 -> P0, P1
C2-2 -> P2, P3
```
- ✅ Each group is independent

- ✅ Partition assignment occurs within each group

---

## Consumer Crash & Reprocessing Timeline

```text
Copy code
[Consumer1 reads 151–160]
151 152 153 154 155 156 157 158 159 160
 ↑                   ↑
 Start reading      CRASH before commit

State:
Last committed = 150
Processed 151–155 (not committed)

Rebalance → Partition assigned to Consumer2
Consumer2 resumes from 151 → duplicates possible
After commit → offset = 160
```
- ✅ At-least-once guarantee

- ⚠️ Avoid duplicates: commit after processing

- ✅ Exactly-once: use producer+consumer transactions

## JoinGroup, SyncGroup, Heartbeats Flow

```text
Consumer → JoinGroupRequest → Coordinator (Group Broker)
Coordinator elects group leader → collects members
Leader → assigns partitions → SyncGroupRequest → Coordinator
Coordinator → forwards assignment to all consumers
Consumers start → send Heartbeats to coordinator
If heartbeats stop → trigger rebalance
```
---

## Replication & Fault Tolerance

## Replication Factor
- Number of copies per partition across brokers.

- Minimum: 1, typical: 3

- Ensures durability and high availability

---

## ISR (In-Sync Replicas)

- ISR = Replicas fully caught up with leader

- Only ISR members can become leader during failure

Example:

```text

Leader = B1
Followers = B2, B3
ISR = {B1, B2, B3}

If B2 lags → ISR = {B1, B3}
New leader selected from ISR → ensures no data loss
```
Related Settings:
- min.insync.replicas → minimum ISR for write success
 
- unclean.leader.election=false → prevents stale replica as leader

---

## Commit Log, Segments & Retention

- Each partition = commit log, immutable

- Segments → smaller log files for manageability (.log + .index)

- Retention Policy

   - Time-based → retention.ms

   - Size-based → retention.bytes

   - Log compaction → keeps latest value per key

---

## Kafka Connect

- Moves data between Kafka and external systems

- Source Connector → external → Kafka

- Sink Connector → Kafka → external

---

## Kafka Streams

- Java library for real-time stream processing

- Supports filter, join, aggregate directly on Kafka topics

---

## Kafka CLI Commands

```
1. Start Zookeeper (if using old versions < Kafka 2.8)
zookeeper-server-start.bat ..\..\config\zookeeper.properties

2. Start Kafka Broker
kafka-server-start.bat ..\..\config\server.properties

3. List all Topics
kafka-topics.sh --list --bootstrap-server localhost:9092

4. Create Topics with 3 partiton and 1 replication factor
kafka-topics.bat --create --topic my-topic --bootstrap-server localhost:9092 --replication-factor 1 --partitions 3

5. Describe a Topic -Shows details (leader, partitions, replication)
kafka-topics.sh --describe --topic test-topic --bootstrap-server localhost:9092

6. Produce Messages to a Topic - Opens a console where you can type messages, and they go to Kafka
kafka-console-producer.bat --broker-list localhost:9092 --topic my-topic

7. Consume Messages from a Topic - Reads all messages from the beginning of the topic
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic my-topic --from-beginning

8. List Consumer Groups (Shows all consumer groups)
kafka-consumer-groups.sh --list --bootstrap-server localhost:9092

9. Describe a Consumer Group - Shows lag, partitions, offsets for the consumer group
kafka-consumer-groups.sh --describe --group my-group --bootstrap-server localhost:9092

10. Delete a Topic - Deletes a topic (if delete.topic.enable=true in config)
kafka-topics.sh --delete --topic test-topic --bootstrap-server localhost:9092

11. Check Kafka Broker Logs - Debugging broker issues
tail -f logs/server.log

12. Alter Topic (e.g., partitions) - Increases partitions of an existing topic.
kafka-topics.sh --alter --topic test-topic --partitions 5 --bootstrap-server localhost:9092
```
---

## Spring Boot Kafka Example

1. Maven Dependency
```
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

2. application.yml
```
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: demo-group
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
```

3. Producer Service
```
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

@Service
public class ProducerService {
    private final KafkaTemplate<String, String> kafkaTemplate;
    private static final String TOPIC = "demo-topic";

    public ProducerService(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void sendMessage(String message) {
        kafkaTemplate.send(TOPIC, message);
        System.out.println("Produced message: " + message);
    }
}
```

4. Consumer Listener
```
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;

@Service
public class ConsumerService {
    @KafkaListener(topics = "demo-topic", groupId = "demo-group")
    public void consumeMessage(String message) {
        System.out.println("Consumed message: " + message);
    }
}
```

5. Controller to Trigger Producer
```
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class KafkaController {
    private final ProducerService producerService;

    public KafkaController(ProducerService producerService) {
        this.producerService = producerService;
    }

    @GetMapping("/publish")
    public String publishMessage(@RequestParam String message) {
        producerService.sendMessage(message);
        return "Message sent: " + message;
    }
}
```

✅ Usage

1- Start Kafka (Zookeeper or KRaft).

2- Run Spring Boot app.

3- Publish message:

```
http://localhost:8080/publish?message=hello-kafka
```

## Consumer prints:

```
Consumed message: hello-kafka
```
---

## Final Summary

- Kafka is a distributed, fault-tolerant messaging system.

- Topics are split into partitions; partitions are replicated for durability.

- Producers push data → Consumers pull data → Commit offsets track progress.

- Consumer Groups enable scaling and load balancing.

- ISR ensures only up-to-date replicas serve as leaders.

- Kafka Connect and Streams provide integration & real-time processing.

- CLI + Spring Boot examples demonstrate practical usage.

---

## Kafka Interview Pro Tips

<details>
<summary>Core Concepts</summary>

- Topics, partitions, offsets  
- Replication & ISR (In-Sync Replicas)  
- Consumer groups & offset management  
- Retention policies (`log.retention.hours`, `log.retention.bytes`)  
- Zookeeper vs KRaft mode  
</details>

<details>
<summary>Producer & Consumer Configs</summary>

- Producer: `acks`, `batch.size`, `linger.ms`  
- Consumer: `auto.offset.reset`, `enable.auto.commit`  
- Understand how misconfiguration can cause duplicates or message loss  
</details>

<details>
<summary>CLI Knowledge</summary>

- List, create, describe, delete, alter topics  
- Produce & consume messages from console  
- Consumer group management (`--list`, `--describe`, `--reset-offsets`)  
- Check consumer lag & offsets  
</details>

<details>
<summary>Real-World Scenarios</summary>

- Message replay & offset resets  
- High-throughput topics, batching & compression  
- Failures: leader down, out-of-sync replicas, broker failures  
- Exactly-once semantics: idempotent & transactional producers  
</details>

<details>
<summary>Performance & Scaling</summary>

- Partitioning strategy affects throughput & ordering  
- Producer batching & compression reduce network overhead  
- Consumer parallelism depends on partition count  
- Adding partitions increases throughput but limits max consumers per group  
</details>

<details>
<summary>Troubleshooting</summary>

- Consumer not reading: check topic, partitions, offsets, lag  
- Messages lost: check retention, acks, replication factor  
- Broker down: check leader election & ISR  
- Step-by-step debugging demonstrates hands-on experience  
</details>

<details>
<summary>Bonus Topics</summary>

- Kafka Streams basics  
- Kafka Connect for ETL  
- Schema Registry & Avro for data compatibility  
- Monitoring metrics (`BytesInPerSec`, `UnderReplicatedPartitions`)  
</details>

<details>
<summary>Pro Tips</summary>

- Draw diagrams of topics, partitions, offsets, consumer groups  
- Use analogies: topic = channel, partition = file, offset = line, consumer group = team  
</details>




