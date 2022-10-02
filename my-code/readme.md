# My Code - Apache Kafka Series - Learn Apache Kafka for Beginners v3

## Resources
### Confluent
* [Confluent Developer](https://developer.confluent.io/):
    * [Confluent CLI Overview](https://docs.confluent.io/confluent-cli/current/overview.html#confluent-cli-overview)
        * [Confluent CLI Command Reference](https://docs.confluent.io/confluent-cli/current/command-reference-index.html)
            * [confluent local](https://docs.confluent.io/confluent-cli/current/command-reference/local/index.html#confluent-local)
    * [Kafka Programming Tutorials](https://www.conduktor.io/kafka/kafka-programming-tutorials)
        * [Kafka Client Libraries SDK List](https://www.conduktor.io/kafka/kafka-sdk-list)
### Jetbrains
    * Intellij IDEA  
        * [Windows Download](https://www.jetbrains.com/idea/download/#section=windows)

## Kafka
### Start Kafka
* Docker:
    * navigate to directory that contains the docker-compose.yml file
    * start Kafka:
        ```bash
        $ docker compose up -d
        $ docker compose status
        $ docker compose down
        ```
* Confluent CLI:
    ```bash
    $ confluent local version
    $ confluent local current
    $ confluent local destroy

    $ confluent local services list
    $ confluent local services start
    $ confluent local services status
    $ confluent local services top
    ```
### Kafka CLI
#### Topics
* Topics: See course download file '0-kafka-topics.sh'
    ```bash
    $ kafka-topics --help

    # list topics
    $ kafka-topics --bootstrap-server localhost:9092 --exclude-internal --list

    # create topic
    $ kafka-topics --bootstrap-server localhost:9092 --create --topic first_topic --partitions 2 --replication-factor 1  --if-not-exists

    # describe topic
    $ kafka-topics --bootstrap-server localhost:9092 --describe --topic first_topic 

    # delete topic
    $ kafka-topics --bootstrap-server localhost:9092 --delete --topic first_topic
    ```
#### Console Producers
* Console Producer: See course download file '1-kafka-console-producer.sh'
    ```bash
    $ kafka-console-producer --help

    # start producer: no key
    $ kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic
    > hello kafka
    > how are you?
    > kafka rocks
    > cntl-c

    # produce without interactive mode
    $ echo "another message to first_topic" | kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic $1

    # produce with property
    $ kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic --producer-property acks=all
      
    # write to a non-existing topic: will create the topic with partition = 1 and replication factory = 1
    $ kafka-console-producer --bootstrap-server localhost:9092 --topic new_topic

    # producer with message keys
    $ kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic --property parse.key=true --property key.separator=:
    >abc:message 1 with a key
    >def:message 2 with a key
    >cntl-c
    ```
#### Console Consumer
* Console consumer: see course download file '2-kafka-console-consumer.sh'
    * Basic consumer
        ```bash
        $ kafka-console-consumer --help

        # read new messages from topic
        $ kafka-console-consumer --bootstrap-server localhost:9092 --topic first_topic

        # read topic from beginning
        $ kafka-console-consumer --bootstrap-server localhost:9092 --topic first_topic --from-beginning

        # consume and include timestamp, partition, offset and key in output
        $ kafka-console-consumer --bootstrap-server localhost:9092 --topic first_topic --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --property print.partition=true --property print.offset=true --from-beginning
        ```
#### Consumer Groups
* Consumer in a Group: see course download file '3-kafka-console-consumer-in-groups.sh'
    ```bash
    # terminal 1: producer
    $ kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic --property parse.key=true --property key.separator=:

    # terminal 2: consumer 1
    $ kafka-console-consumer --bootstrap-server localhost:9092 --topic first_topic --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --property print.partition=true --property print.offset=true --group first-consumer-group 

    # terminal 3: consumer 2
    $ kafka-console-consumer --bootstrap-server localhost:9092 --topic first_topic --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --property print.partition=true --property print.offset=true --group first-consumer-group 

    # terminal 43: consumer in a new group - will get all messages
    $ kafka-console-consumer --bootstrap-server localhost:9092 --topic first_topic --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --property print.partition=true --property print.offset=true --group second-consumer-group 
    ```
* Consumer Groups: see course download file '4-kafka-consumer-groups.sh'
    * Can reset offsets in number of different ways: --to-earliest, --to-latest, --shift-by, --by-duration, --from-file, --to-current, --to-datetime, --to-offset
    ```bash
    $ kafka-consumer-groups --help

    # List groups
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --list

    # Describe a group
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group first-consumer-group
    ```
* Consumer Groups - Reset Offsets: see course download file '5-reset-offsets.sh'
    ```bash
    # Reset group offets - to earliest
    #   dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic --reset-offsets --to-earliest --dry-run
    #   execute
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic --reset-offsets --to-earliest --execute

    # Reset group offets - shift-by
    #   dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic --reset-offsets --shift-by 5 --dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic --reset-offsets --shift-by -2 --dry-run

    # Reset group offets - shift-by for specific partitions only
    #   dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic:1 --reset-offsets --shift-by 5 --dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic:0 --reset-offsets --shift-by -1 --dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic:0,1 --reset-offsets --shift-by 1 --dry-run

    # Reset group offets - to-offset for specific partitions only
    #   dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic:1 --reset-offsets --to-offset 7 --dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic:0 --reset-offsets --to-offset 12 --dry-run
    $ kafka-consumer-groups --bootstrap-server localhost:9092 --group first-consumer-group --topic first_topic:0,1 --reset-offsets --to-offset 11 --dry-run
    ```
### Kafka Programming