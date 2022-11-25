## Kafka Notes

### Getting Started

#### Installation

You can install Kafka using Brew as below:
`brew install kafka`

#### Navigation

Producers, Consumers and Topics can be started and used from any location.
Zookeeper and Kafka need to be started in the Kafka installation directory. From the initial terminal directory you can navigate there with the following commands:<br>
`cd..` Step back from personal drive
`cd..` Step back from Users drive
`cd usr/local/Cellar/kafka` Navigate to Kafka
`cd 3.3.1_1` Navigate to installation (_in this example the latest version was 3.1.1_1_).

#### Starting Zookeeper and Kafka Broker

Zookeeper and Kafka need to be started before doing anything.
_Note: Zookeeper may take a few seconds to load._
After navigating to the correct directory above start the following commands in order:<br>
**Start Zookeeper**
`bin/zookeeper-server-start libexec/config/zookeeper.properties`
**Start Kafka Broker**
`bin/kafka-server-start libexec/config/server.properties`

#### Starting Topics, Producers and Consumers

Topics, Producers and Consumers can be started from anywhere. The easiest way is to open 3 separate terminal tabs and run the commands below across each.<br>
**Start Topic**
`kafka-topics --bootstrap-server localhost:9092 --create --topic my_topic_name `
**Start Producer**
`kafka-console-producer --bootstrap-server localhost:9092 --topic my_topic_name `
**Start Consumer**
`kafka-console-consumer --bootstrap-server localhost:9092 --topic my_topic_name `<br>
You can then start writing to the topic using the producer terminal tab, with the output being written in the consumer terminal tab.<br>
**Write in Producer Tab**
`example data 1`
`example data 2`<br>
**Expected Output in Consumer Tab**
`example data 1`
`example data 2`<br>
When finished you can use 'ctrl+c' to close the producer and consumer.

### Topics in more detail

##### List topics

List topics running with the following command:
`kafka-topics --bootstrap-server localhost:9092 --list`
This will be useful throughout the tutorial if topics need to be deleted.

##### Partitions

Partitions are used in a topic to split the single log of data into multiple separate data logs.
Each partition contains an ordered log but there is no ordering across partitions.
Partitions are used for scalability as multiple brokers can hold partitions from the same topic.
Partitions are set on topic creation as below:
`kafka-topics --bootstrap-server localhost:9092 --create --topic my_topic_name --partitions 3`

##### Replication Factor

Replication is used to balance the load across multiple kafka brokers.
The number on the replication factor needs to be equal to or below the current running brokers.
The replication factor is specified on the topic creation as below:
`kafka-topics --bootstrap-server localhost:9092 --create --topic my_topic_name --partitions 3 --replication-factor 3`

##### Description

Topics can be described to find out information, like the number of partitions, with the following command:
`kafka-topics --bootstrap-server localhost:9092 --describe --topic my_topic_name`

##### Deletion

Topics can be deleted with the following command:
`kafka-topics --bootstrap-server localhost:9092 --delete --topic my_topic_name`

### Producers in more detail

##### Acknowledgment

Specify acknowledgment level for message delivery.
Use the property as below when setting up a producer:
`kafka-console-producer --bootstrap-server localhost:9092 --topic my_topic_name --producer-property acks=all`

##### Produce to non-existing topic

This is not recommended as best practice and is a useful note in case of a typo.
If a topic name that doesn't exist is used when creating a producer, the topic will be created.
An example command is:
`kafka-console-producer --bootstrap-server localhost:9092 --topic wrong_topic_name`

##### Producing with keys

Create a producer which requires a key-value rather than just a value.
This is often used with partitions to ensure data with the same key is in the same partition.
Specifying a key separator is required to ensure the structure of data inputted is correct.
This is done in the command below with the separator being a colon:
`kafka-console-producer --bootstrap-server localhost:9092 --topic my_topic_name --property parse.key=true --property key.separator=:`<br>
Example data to be written by the consumer:
`user_id_1234:username`
`score_everton:3-0`<br>
Not every producer requires a key, the value of the key will be null if one is not provided for a producer created without the key property.

### Consumers in more detail

##### Reading from beginning

There is an option to read the values already in a topic with the command below when creating a consumer:
`kafka-console-consumer --bootstrap-server localhost:9092 --topic my_topic_name --from-beginning`<br>
If the topic has multiple partitions, the messages may not be in written order.
This is because, as mentioned in before, partitions are ordered but there is no ordering across partitions.

##### Formatting

There is an option to format messages to receive more information than just the message value.
An example of formatting with the timestamp, key and value is shown below:
`kafka-console-consumer --bootstrap-server localhost:9092 --topic my_topic_name --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --from-beginning`

### Consumer Groups

Partitions read are divided by the consumers reading from a topic.
For this to work best, the number of consumers in a group must be equal to or less than the number of partitions.
Otherwise, consumers will be inactive until the number of consumers is equal to or less than the number of partitions.
Creating a consumer in a group is completed as below with the group name being the group key:
`kafka-console-consumer --bootstrap-server localhost:9092 --topic my_topic_name --group my_group_name`

##### Consumer Offsets

It is important to note that the position of each partition contains an offset (id marker) of the last position read.
This is used with consumer groups to ensure messages aren't read more than once.<br>
**Writen Example:**

<ul>
<li>A topic is created with 3 partitions.
<li>A producer is created.
<li>A consumer group (consumer_group_one) is created with 3 consumers.
<li>4 messages are written by a producer.
<li>The 4 messages are read by active consumers in consumer_group_one.
<li>The active consumers are closed.
<li>2 additional messages are written by a producer.
<li>A consumer in consumer_group_one is created and specified to read from beginning.
<li>Only the 2 additional messages are retrieved.
<li>A consumer in consumer_group_two is created and specified to read from beginning.
<li>All 10 messages in the topic are retrieved.
</ul>

##### Consumer Offset Example With Commands

It is assumed that [Zookeeper and a Kafka broker](#starting-zookeeper-and-kafka-broker) are running, the commands are at the top of the page for this.
In this example, the scenario is a score event stream where various matches have a score update.
A score contains a match ID key and a value.
The match ID key ensures that scores for the same match are always written to the same partition to maintain [ordering.](#partitions)<br>
**Create a Topic with Partitions**
`kafka-topics --bootstrap-server localhost:9092 --create --topic premierleague_matches --partitions 3`<br>
**Create a Producer with keys**
`kafka-console-producer --bootstrap-server localhost:9092 --topic premierleague_matches --property parse.key=true --property key.separator=:`<br>
**Create 3 Consumers in the same group**
_Repeat the command below in 3 separate terminal tabs_
`kafka-console-consumer --bootstrap-server localhost:9092 --topic premierleague_matches --group premierleague_group`<br>
**Begin writing in the producer tab**
`match_id_1:1-0`
`match_id_2:0-1`
`match_id_1:HT`
`match_id_3:0-1`<br>
**Example Output in Consumer tabs**
Consumer 1: `1-0`
Consumer 1: `HT`
Consumer 2: `0-1`
Consumer 3: `1-1`<br>
**Destroy Consumer Tabs**
_Close all tabs with consumers in._<br>
**Write more information in the producer tab**
`match_id_1:1-1`
`match_id_3:1-1`<br>
**Start a new consumer tab in the same group as before and specify read from beginning**
`kafka-console-consumer --bootstrap-server localhost:9092 --topic premierleague_matches --group premierleague_group --from-beginning`<br>
**Example Output in Consumer tab**
_The order may be different if written to different partitions._
`1-1`
`1-1`<br>
**Start a new consumer tab in a different group to before and specify read from beginning**
`kafka-console-consumer --bootstrap-server localhost:9092 --topic premierleague_matches --group premierleague_group_two --from-beginning`<br>
**Example Output in Consumer tab**
_All messages in the topic will be read._
`1-0`
`0-1`
`HT`
`...`<br>
**End**
The example above can also be used to represent how values with the same key will always be written to the same partition.
The easiest way to demonstrate is to have the number of consumers in the group matching the number of partitions.
When this occurs, each consumer reads from a single partition.
Test this by adding more match id's and writing several scores for each.

#### Consumer Group Description

Consumer groups can be described with the command below:
`kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group premierleague_group`<br>
The description contains information such as the partition offsets at each topic and how many members are active.
The description also contains the log end offset for each partition and specifies the lag - how many values have not been read in each partition.
When there are active members, the consumer id for each partition will also be shown in the description.

#### Resetting offsets

Offsets are committed once in a while, e.g. when closed, to resume from the same point when re-created.
To reset use the command below, in this example the earliest offset is used and reset on all topics:
`kafka-consumer-groups --bootstrap-server localhost:9092 --group premierleague_group --reset-offsets --to-earliest --execute --all-topics`<br>
To reset for a specific topic use:
`kafka-consumer-groups --bootstrap-server localhost:9092 --group premierleague_group --reset-offsets --to-earliest --execute --topic premierleague_matches`<br>

**Example**
_First, follow the above [example](#consumer-offset-example-with-commands) for Consumer Groups and score events._
This will ensure that the topic has values written and a consumer group has read events and therefore has offsets.<br>
**View description**
`kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group premierleague_group`<br>
**Expected output**
_There should be values in the current offset column._<br>
**Stop all running consumers**
_`Ctrl+C` or close the terminal tab._<br>
**Reset offsets**
`kafka-consumer-groups --bootstrap-server localhost:9092 --group premierleague_group --reset-offsets --to-earliest --execute --all-topics`<br>
**Start a new consumer tab in the same group as before and specify read from beginning**
`kafka-console-consumer --bootstrap-server localhost:9092 --topic premierleague_matches --group premierleague_group --from-beginning`<br>
**Expected output**
_All messages in the topic will be read._
`1-0`
`0-1`
`HT`
`...`<br>
**End**
_End of example._<br>

##### Shifting the offsets

Offsets can be shifted forward or back using positive or negative values respectively.
The command is as below for forward:
`kafka-consumer-groups --bootstrap-server localhost:9092 --group premierleague_group --reset-offsets --shift-by 2 --execute --all-topics`<br>
The command is as below for backward:
`kafka-consumer-groups --bootstrap-server localhost:9092 --group premierleague_group --reset-offsets --shift-by -2 --execute --all-topics`<br>
This can be tested by using the same example above, instead of resetting the offsets they can be shifted.

### Kafka in Java

Setting up is easiest following the below.

Intellij -> New Project -> Gradle -> Java 11.
In course example, module name is kafka-basics.
Find latest Kafka [dependency](https://mvnrepository.com/artifact/org.apache.kafka/kafka-clients) on Maven/Gradle.
Find latest [SLF4J-API](https://mvnrepository.com/artifact/org.slf4j/slf4j-api) dependency for logging.
Find latest [SLF4J-Simple](https://mvnrepository.com/artifact/org.slf4j/slf4j-simple) dependency for logging.
Ensure all dependencies are implementation not testimplementation, then pull them in using the IDE.
Settings -> Build, Execution, Deployment -> Build Tools -> Build and run using -> Intellij IDEA. Supposedly works better.

#### Java Producer

##### Create Producer Properties

Create the producer property and set properties using `setProperty(key, value)`

```java
Properties producerProperties = new Properties();
producerProperties.setProperty(ProducerConfig.BOOTSTRAP_SERVER_CONFIG, "localhost:9092");
producerProperties.setProperty(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName();
producerProperties.setProperty(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName();
```

##### Create Producer based on serializers

```java
KafkaProducer<String, String> producer = new KafkaProducer<>(producerProperties);
```

##### Send Data using Producer Record

**Create record with a key, value to be written to a topic:**

```java
ProducerRecord<String, String> producerRecord = new ProducerRecord<>("topic_name", "key", "value")
```

**Create record with a value to be written to a topic:**

```java
ProducerRecord<String, String> producerRecord = new ProducerRecord<>("topic_name", "value")
```

Send the data asynchronously using the Producer:

```java
producer.send(producerRecord);
```

**Flush**
Block until the data has been sent:

```java
producer.flush();
```

**Flush and Close**
Also blocks until data has been sent, then closes the producer:

```java
producer.close();
```

#### Test Example of Java Producer

Pre-Requisite: A [zookeeper and kafka-broker instance](#starting-zookeeper-and-kafka-broker) is running like before.<br>
**Create a Topic**
`kafka-topics --bootstrap-server localhost:9092 --create --topic testing`<br>
**Create a Consumer**
`kafka-console-consumer --bootstrap-server localhost:9092 --topic testing`<br>
**Create a Java producer for the relevant topic**
Follow the steps for creating a producer [above](#java-producer), replacing `topic_name` with `testing`.
For testing, this should all be in the main method in a created class.
Any key or value can be used for testing purposes.<br>
**Run Java code**
Run the main method containing the Producer code.<br>
**Consumer Output**
The data will be received by the consumer:
`value`<br>
**Testing further**
Do this several times, each time changing the value in the Producer Record.

#### Java API Callbacks

Create a separate class and duplicate the main method produced in this [example](#java-producer).<br>

##### Add a callback to the `Producer.send` function

The callback can be written like below.
A shortcut in intellij is to use `tab` when entering `new Callback()` as it will create the method `onCompletion` automatically.

```java
producer.send(record, new Callback() {
    @Override
    public void onCompletion(RecordMetadata metadata, Exception exception) {

    }
});
```

The method above will throw an exception in case of an unsuccessful request.

##### Example

The example below logs the topic name using the RecordMetadata, given there is no exception.

```java
producer.send(record, new Callback() {
    @Override
    public void onCompletion(RecordMetadata metadata, Exception exception) {

        if(exception == null) {
            //success
            log.info("Topic: " + metadata.topic());
        }
        else {
            //failure
            log.error("Error: ", exception);
        }
    }
});
```

##### Lambda Example

Intellij may grey out the `new Callback()` with a prompt to refactor to a lambda.

```java
producer.send(record, (metadata, exception) -> {

    if(exception == null) {
        //success
        log.info("Topic: " + metadata.topic());
    }
    else {
        //failure
        log.error("Error: ", exception);
    }
});
```

Try printing different parts of the metadata e.g. partition, offset and timestamp.

##### Looping

Also try looping the creation and sending of records.
The example below contains comments related to code snippets above which can be added.

```java
//Create producer properties
//Create producer with properties
for(int i = 0; i < 10; i ++) {
    //Create producer record
    //Send producer record
}
//Flush producer
//Close producer
```

If following this example with a topic with partitions, a consumer group and several consumers, the StickyPartitioner can be observed.
This assumes the example only sends values and not key-values as otherwise partition distribution is based on key hashing.
Data will be sent in batches if sent very quickly e.g. first 3 messages to partition 1, second 3 messages to partition 3 etc.
Sleeps: `thread.sleep();` can be added to look into this further.
[Reminder for creating a topic with partitions.](#partitions)
[Reminder for creating a consumer with group.](#consumer-groups)

#### Java Consumer

First step is to keep or [re-write](#java-producer) the class for the Producer and add a class for the Consumer.

##### Create Consumer Properties

Create the consumer property and set properties using `setProperty(key, value)`
Compared with the Producer, there is an additional property for the group ID.
There is also a property for offset resetting: _`AUTO_OFFSET_RESET_CONFIG`_ with options: `earliest`, `latest`, `none`.

<li>earliest = Read from first message in topic.
<li>latest = Read from latest only.
<li>none = No previous offset found, do nothing.

An example property set for a consumer is found below:

```java
Properties consumerProperties = new Properties();
consumerProperties.setProperty(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "127.0.0.1:9092");
consumerProperties.setProperty(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
consumerProperties.setProperty(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
consumerProperties.setProperty(ConsumerConfig.GROUP_ID_CONFIG, "scores");
consumerProperties.setProperty(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
```

##### Create Consumer

```java
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(properties);
```

##### Subscribe Consumer to topic

Subscribe to a single topic:

```java
consumer.subscribe(Collections.singletonList("topic_name"));
```

Subscribe to multiple topics:

```java
String[] topicArray = {"topic_1", "topic_2", "topic_3"};
consumer.subscribe(Arrays.asList(topicArray));
```

##### Poll for new data

The code below asks Kafka for any data, and if there is no response it waits for the set amount of ms.
The amount of ms below is 100.

```java
while(true) {
    ConsumerRecords<String, String> records =
        consumer.poll(Duration.ofMillis(100));
}
```

##### Read data from ConsumerRecord

Read data from a consumer record using functions like below

```java
record.key();       //Key
record.value();     //Value
record.partition(); //Partition storing the record
record.offset();    //Offset for the record
```

##### Graceful Shutdown

Get a reference to the current thread:

```java
final Thread mainThread = Thread.currentThread();
```

Add the shutdown hook:
The consumer wakeup will ensure that a WakeupException is thrown once the consumer polls for the next time.

```java
Runtime.getRuntime().addShutdownHook(new Thread() {
    public void run() {
        log.info("Detected a shutdown.");
        consumer.wakeup();
        try {
            mainThread.join();
        }
        catch(InterruptedException e) {
            e.printStackTrace();
        }
    }
});
```

Add a try catch block around the while loop for polling.
The code above will cause the WakeupException below.
Add a finally bracket to close the consumer and commit any offsets.

```java
try {
    while(true) {
        //consumer poll
        //Do something with retrieved records
    }
}
catch(WakeupException e) {
    log.info("Wake up exception");
}
catch(Exception e) {
    log.info("Unexpected, wakeup exception was expected");
}
finally {
    consumer.close();
}
```

##### Java Consumer Groups

To test partitioning we can launch multiple instances of the same Java consumer.
The setting in Intellij is: 'Edit _Run/Debug_ Configurations -> Build and Run -> Modify Options -> Operating System -> Allow multiple instances.
Enables the running of the same application multiple times.
Then launch the same number of instances as the number of partitions that the topic that the consumer subscribes to has.
If a topic needs to be setup with partitions use [this](#partitions).
Launching the topics and viewing logs can demonstrate the re-balancing of partitions as more consumers join.

#### Rebalancing

Rebalancing occurs when consumers leave and join a group.
Rebalancing also occurs when an administrator adds new partitions to a group.

##### Eager Rebalancing

All consumers leave group, losing any assigned partitions.
All consumers rejoin group, gaining new partition assignment.
Will be short period of time with no processing.
No guarantee of receiving same partition.

##### Cooperative Rebalance (Incremental)

Assign a small subset of partitions from one consumer to another
Consumers with no reassigned partitions process uninterrupted
May be several iterations to find a 'stable' assignment
Avoids stopping.
Strategies:

<ul>
<li>RangeAssignor -> Assign per-topic (leads to imbalance)
<li>RoundRobin: Assign across topics in round-robin (optimal balance)
<li>StickyAssignor: Minimise partition movement when joining/leaving. Otherwise round-robin.
<li>CooperativeStickyAssignor: Rebalance identical to Sticky but supports cooperative rebalance, can keep consuming from topic.
<li>Default = Consumer uses RangeAssignor but can upgrade to CooperativeStickyAssignor.
</ul>
##### Static Membership

By default, partitions revoked and re-assigned when a consumer leaves.
`group.instance.id` makes consumer a **static member.**
Upon leaving, consumer has 'ms' to join back before a rebalance.
Enables restarting without a large rebalance.
Avoids re-building a cache for example.

##### Competitive Rebalancing Setting

Add this property to the consumer to force the consumer rebalancing to use CooperativeStickyAssignor:

```java
consumerProperties.setProperty(ConsumerConfig.PARTITION_ASSIGNMENT_STRATEGGY_CONFIG, CooperativeStickyAssignor.class.getName());
```

##### Auto-offset Commit Behaviour

Consumer offsets are regularly commited.
E.g. when calling `poll()` and time elapses for auto commit interval.
Rule: Always ensure messages are successfully processed before `poll()` is called for the second time.
Ensure your code looks like this pseudocode:

```java
loop start
//Poll for records
//Do something with records
loop end
```

and not:

```java
loop start
//Poll for records
//Poll for records
//Do something with records
loop end
```
