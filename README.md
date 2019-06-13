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



// controller for String

@Service
//class
public class Producer {

    private static final Logger logger = LoggerFactory.getLogger(Producer.class);
    private static final String TOPIC = "users";

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void sendMessage(String message) {
        logger.info(String.format("#### -> Producing message -> %s", message));
        this.kafkaTemplate.send(TOPIC, message);
    }
}




@Service
//consumer class
public class Consumer {

    private final Logger logger = LoggerFactory.getLogger(Producer.class);

    @KafkaListener(topics = "users", groupId = "group_id")
    public void consume(String message) throws IOException {
        logger.info(String.format("#### -> Consumed message -> %s", message));
    }
}

#The above code uses the string format


#Concurrent error handler listner

https://www.confluent.io/blog/spring-for-apache-kafka-deep-dive-part-1-error-handling-message-conversion-transaction-support

#Spring cloud stream for kafka

https://www.confluent.io/blog/spring-for-apache-kafka-deep-dive-part-2-apache-kafka-spring-cloud-stream


#KAFKA Streams

https://www.baeldung.com/java-kafka-streams

----------------------------------------------------------------------------------------------------------------------------------------
#Stream Creation

public class WordCountApplication {
 
    public static void main(final String[] args) throws Exception {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-application");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "kafka-broker1:9092");
        props.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        props.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());
 
        StreamsBuilder builder = new StreamsBuilder();
        KStream<String, String> textLines = builder.stream("TextLinesTopic");
        KTable<String, Long> wordCounts = textLines
            .flatMapValues(textLine -> Arrays.asList(textLine.toLowerCase().split("\\W+")))
            .groupBy((key, word) -> word)
            .count(Materialized.<String, Long, KeyValueStore<Bytes, byte[]>>as("counts-store"));
        wordCounts.toStream().to("WordsWithCountsTopic", Produced.with(Serdes.String(), Serdes.Long()));
 
        KafkaStreams streams = new KafkaStreams(builder.build(), props);
        streams.start();
    }
 
}
