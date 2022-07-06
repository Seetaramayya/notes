# Upgrade AlpAkka Kafka Connector Issue

### Problem

  After upgrading alpakka to [3.0.0](https://github.com/akka/alpakka-kafka/releases/tag/v3.0.0) from 
  [2.1.1](https://github.com/akka/alpakka-kafka/releases/tag/v2.1.1) producer performance is dropped 30% 


### Conclusions (or) Take aways

- Initial suspension was AlpAkka has a bug but that's not the case
- From Kafka 3.0, the producer enables by default (acks=all, enable.idempotence=true). That means every write needs to 
  wait till it gets confirmation from all the brokers (which takes time than single broker)
- AlpAkka handles this with `akka.kafka.producer.parallelism` configuration

### Debugging Steps

- Tested with local kafka broker in docker container, could not observe noticeable differences
- Published 10,000 sample messages with different versions


With (Kafka client=2.8.1, alpakka = 2.1.1)

| Attempt | Number Of Messages | Time Taken Millis |
|:-------:|:------------------:|:-----------------:|
|    1    |       10000        |       9721        |
|    2    |       10000        |       16010       |
|    3    |       10000        |       10097       |
|    4    |       10000        |       10401       |
|    5    |       10000        |       10216       |

With (Kafka client=3.1.0, alpakka = 3.0.0)

| Attempt  | Number Of Messages | Time Taken Millis | Time Taken Millis (acks = 1) |
|:--------:|:------------------:|:-----------------:|:----------------------------:|
|    1     |       10000        |       20247       |             9844             |
|    2     |       10000        |       24075       |            10136             |
|    3     |       10000        |       22249       |             9190             |
|    4     |       10000        |       20213       |            12681             |
|    5     |       10000        |       20418       |            10039             |

- Try to reproduce with alaakka kafka connector sample code but I could not. Everything is fast 
- At this point I noticed our `refrence.conf` have `akka.kafka.producer.parallelism=100` but that is changed to `10000` in 
  alpakka kafka connector

