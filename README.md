# Scala Producer and Consumer for Confluent Cloud
## Configuring the Consumer for Failover (Timestamp Preservation)
You must configure an additional interceptor for consumers from initial deployment in the source cluster. During failover, the interceptor will allow consumers to resume when reading topics that are replicated to the secondary cluster.

# Documentation

You can find the documentation and instructions for running this Scala example at [https://docs.confluent.io/platform/current/tutorials/examples/clients/docs/scala.html](https://docs.confluent.io/platform/current/tutorials/examples/clients/docs/scala.html?utm_source=github&utm_medium=demo&utm_campaign=ch.examples_type.community_content.clients-ccloud)

# Prerequisites

## Client
- Scala `sbt` - https://www.scala-sbt.org/download.html.
- Kafka CLI `kafka-console-consumer` - https://www.confluent.io/installation/.

# Setup
1. Clone this repository.
   ```bash
   git clone https://github.com/hendrasutanto/Consumer-Offset-Replication-with-Scala.git
    ```
2. Change directory to the example of Scala.
   ```bash
   cd Consumer-Offset-Replication-with-Scala
    ```
3. Configure the `producer.config` and `consumer.config` files with configuration parameters to connect to your Kafka cluster, if necessary.

## Produce Records
1. Compile the Scala code:
   ```bash
   sbt clean compile
    ```
3. Run the producer, passing in arguments for:
   - the `producer.config` file with configuration parameters to connect to your Kafka cluster
   - the topic name i.e. `quickstart`
   ```bash
   sbt "runMain io.confluent.examples.clients.scala.Producer producer.config quickstart"
    ```
4. Verify that the producer sent all the messages. You should see:
   ```bash
   <snipped>
   Produced record at test1-0@120
   Produced record at test1-0@121
   Produced record at test1-0@122
   Produced record at test1-0@123
   Produced record at test1-0@124
   Produced record at test1-0@125
   Produced record at test1-0@126
   Produced record at test1-0@127
   Produced record at test1-0@128
   Produced record at test1-0@129
   Wrote ten records to test1
   [success] Total time: 6 s, completed 10-Dec-2018 16:50:13
    ```

## Consume Records
1. Run the consumer, passing in arguments for:
   - the `consumer.config` file with configuration parameters to connect to your Kafka cluster
   - the topic name you used earlier i.e. `quickstart`
   ```bash
   sbt "runMain io.confluent.examples.clients.scala.Consumer consumer.config quickstart"
    ```
2. Verify the consumer received all the messages. You should see:
   ```bash
   ...
   Consumed record with key alice and value {"count":1}, and updated total count to 1
   Consumed record with key alice and value {"count":2}, and updated total count to 3
   Consumed record with key alice and value {"count":3}, and updated total count to 6
   Consumed record with key alice and value {"count":4}, and updated total count to 10
   Consumed record with key alice and value {"count":5}, and updated total count to 15
   Consumed record with key alice and value {"count":6}, and updated total count to 21
   Consumed record with key alice and value {"count":7}, and updated total count to 28
   Consumed record with key alice and value {"count":8}, and updated total count to 36
   Consumed record with key alice and value {"count":9}, and updated total count to 45
   Consumed record with key alice and value {"count":10}, and updated total count to 55
   ...
    ```
3. The `ConsumerTimestampsInterceptor` will write to a new topic `__consumer_timestamps` to preserve the required timestamp information for the offset translation.
4. Consume messages in `__consumer_timestamps` topic with `kafka-console-consumer`
   ```bash
   bin/kafka-console-consumer.sh --topic __consumer_timestamps --from-beginning --bootstrap-server localhost:9092 --property print.timestamp=true --property print.key=true --property print.value=false
    ```
5. Verify the messages in `__consumer_timestamps` topic. You should see:
   ```bash
   ...
   CreateTime:1690336137541	scala_example_grouptest
   CreateTime:1690340976867	scala_example_grouptest
   ...
    ```
7. When you are done, press Ctrl-C.
