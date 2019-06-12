# MyKafka
A try with confluent Kafka


# Start zookeeper
zookeeper-server-start.bat D:\softwares\confluent-5.2.1\etc\kafka\zookeeper.properties


#start kafka 
kafka-server-start.bat D:\softwares\confluent-5.2.1\etc\kafka\server.properties

#Schema Registry
schema-registry-start.bat D:\softwares\confluent-5.2.1\etc\schema-registry\schema-registry.properties

#start producer
kafka-console-producer.bat --broker-list localhost:9092 --topic text.test

#start consumer
kafka-console-consumer.bat --topic text.test --from-beginning --zookeeper localhost:2181
