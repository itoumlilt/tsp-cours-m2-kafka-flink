
# First Kafka Cluster

## Connecting to lab environment

Open your browser and go to the link:
http://ec2-34-247-90-185.eu-west-1.compute.amazonaws.com:8080/guacamole/

* you need to change `ec2-34-247-90-185.eu-west-1.compute.amazonaws.com` with your host, this string will be **communicated**.
* username is: `student`
* your password will be **communicated**.

## Managing your server
Your server should be already started for you. But
you can manage kafka servers with systemctl,

to start:   `systemctl start kafka-1`
to stop:    `systemctl stop kafka-1`

kafka-1 here is the broker(you have three please don't forget).

You can access kafka binaries in
    ``/opt/kafka/bin``

kafka data files(log_files):
    ``/var/kafka-1`` , 
    ``/var/kafka-2`` ,
    ``/var/kafka-3``

Kafka log files you can check in case something goes wrong: ` /var/log/kafka-1/kafka.log`, `/var/log/kafka-2/kafka.log`,
` /var/log/kafka-3/kafka.log`
  

Our kafka instances: 

```
//kafka-instance-1: localhost:9092 
//kafka-instance-2: localhost:9093
//kafka-instance-3: localhost:9094 
```

## Producer Latency

### Let's create topic 

You can use the following command as template:

 ```
 /opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic test-one-replication-three-partitions --replication-factor 1 --partitions 3
 ```

* With the replication factor: 1 / partition 2
* With the replication factor: 3 / partition 2
* With the replication factor: 1 / partition 20
* With the replication factor: 3 / partition 20



You can check/verify if your topic is generated:

``` 
/opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe
```

A few questions for you think/search about it:
#### Q: What is retention period ? What are the possible two ways to set retention rules ?
#### Q: What are files generated in Kafka log dir(data path: /var/kafka-1) for each topic/partitions? What is the purpose for each ?
#### Q: Could we change the number of partitions for a topic that is  already created ?
#### Q: Could we change the number of replication factor for a topic that is  already created ?


## Let's do performance test
Please use the following number if you use high number you can full disk space, if such case happened follow points at the end of the tutorial.

* **dont forget to change topic name(i.e. itoumlilt-test) with your created topics**.
#### Q: How does the number of partitions impact performance ? Here you can target created topics one with replication factor:1 and other one with replication factor:3
Template command:
```
 /opt/kafka/bin/kafka-producer-perf-test.sh --num-records 10000 --record-size 1000 --topic itoumlilt-test --throughput 1000 --producer-props bootstrap.servers=localhost:9092
```
#### Q: How does batch.size impact producer performance ? You can change batch.size to 5/50/100/1000

Template command:
```
 /opt/kafka/bin/kafka-producer-perf-test.sh --num-records 1000 --record-size 1000 --topic itoumlilt-test --throughput 1000 --producer-props bootstrap.servers=localhost:9092 batch.size=5
```

We learnt producer acks important parameter. Let's test this out

#### Q: how does producers acks config change latency ? Please use topic with the replication factor 3. Compare 99th percentile latencies.

Template command:
```
 /opt/kafka/bin/kafka-producer-perf-test.sh --num-records 1000 --record-size 1000 --topic itoumlilt-test --throughput 1000 --producer-props bootstrap.servers=localhost:9092 batch.size=10 acks=all
 
 /opt/kafka/bin/kafka-producer-perf-test.sh --num-records 1000 --record-size 1000 --topic itoumlilt-test --throughput 1000 --producer-props bootstrap.servers=localhost:9092 batch.size=10 acks=0
 
 /opt/kafka/bin/kafka-producer-perf-test.sh --num-records 1000 --record-size 1000 --topic itoumlilt-test --throughput 1000 --producer-props bootstrap.servers=localhost:9092 batch.size=10 acks=1
```


 ## Test the End-To-End Latency

 End-to-end latency is the overall time passed starting from a message is first produced until the produced message consumed by consumer. This test represent real application as it includes both producer and consumer.
 

```
  /opt/kafka/bin/kafka-run-class.sh kafka.tools.EndToEndLatency 
localhost:9092,localhost:9093,localhost:9094 10000

```
 
