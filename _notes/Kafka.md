# Kafka

source system → kafka connect source → kafka cluster(core) → kafka connect sink → target sysstem

# KAFKA 테스트

** windows 는 $KAFKA_HOME\bin\windows\**

Zookeeper 및 kafka 구동

- $KAFKA_HOME/bin/zookeeper-server-start.sh $KAFKA_HOME/config/zookeeper.properties
- $KAFKA_HOME/bin/kafka-server-start.sh $KAFKA_HOME/config/server.properties

topic 생성

- $KAFKA_HOME/bin/kafka-topics.sh —create —topic sample-events —bootstrap-server [localhost:9092](http://localhost:9092) —partitions 1

topic 삭제

- $KAFKA_HOME/bin/kafka-topics.sh —delete —topic sample-events

topic 목록 확인

- $KAFKA_HOME/bin/kafka-topics.sh —bootstrap-server [localhost:9092](http://localhost:9092) —list

topic 정보 확인

- $KAFKA_HOME/bin/kafka-topics.sh —describe —topic sample-events —bootsrap-server localhost:9092

topic 구독 테스트 (하기 정보 등록후 producer쪽에 텍스트 입력)

- $KAFKA_HOME/bin/kafka-console-producer.sh —topic sample-events —bootstrap-server localhost:9092
- $KAFKA_HOME/bin/kafka-console-consumer.sh —topic sample-events —bootstrap-server localhost:9092

# Maria DB 설치

** windows의 경우

maria db 다운로드 및 압축해제 후 관리자 모드로 CMD 실행 및 데이터베이스 초기화

- .\bin\mariadb-install-db.exe —datadir=C:\<path-to-save-data> —service=<service-name> —port=<port> —password=<root-password>

```jsx
create table users(
	id int auto_increment primary key,
	user_id varchar(20),
	pwd varchar(20),
	name varchar(20),
	created_at datetime default NOW()
);
```

# Kafka Connector 설치

- curl -O  http://packages.confluent.io/archive/6.1/confluent-community-6.1.0.tar.gz
- Kafka Connect 실행
./bin/connect-distributed ./etc/kafka/connect-distributed.properties

# JDBC Connector 설치

- confluentinc-kafka-connect-jdbc 다운로드
- etc/kafka/connect-distributed.propertes 파일에 plugin추가
** windows의 경우 \c:\\<path>\\<path> 형식으로 기입

```jsx
plugin.path=<confluentinc-kafka-connect-jdbc-path>/lib
```

- mariadb-java-client-xxx.jar 파일을 <kafka-connector>/share/java/kafka/로 복사
- connector plugins 확인

```jsx
http://localhost:8083/connector-plugins
```

# Source Connector 설정

```jsx
echo '
{
"name" : "my-source-connect",
"config" : {
	"connector.class" : "io.confluent.connect.jdbc.JdbcSourceConnector",
	"connection.url":"jdbc:mysql://localhost:3306/mydb",
	"connection.user":"root",
	"connection.password":"root",
	"mode": "incrementing",
	"incrementing.column.name" : "id",
	"table.whitelist":"users",
	"topic.prefix" : "my_topic_",
	"tasks.max" : "1"
	}
}
' | curl -X POST -d @- http://localhost:8083/connectors --header "content-Type:application/json"
```

users 테이블에 1개 데이터 insert해보면 해당 토픽이 생성된 것을 확인할 수 있음

connector 확인

- localhost:8083/connectors

# Sink Connector 설정

```jsx
echo '
{
"name":"my-sink-connect",
"config":{
	"connector.class":"io.confluent.connect.jdbc.JdbcSinkConnector",
	"connection.url":"jdbc:mysql://localhost:3306/mydb",
	"connection.user":"root",
	"connection.password":"root",
	"auto.create":"true", // 이미 저장되어 있던 테이블 값을 기준으로 타겟 테이블 생성
	"auto.evolve":"true",
	"delete.enabled":"false",
	"tasks.max":"1",
	"topics":"my_topic_users"
	}
}
'| curl -X POST -d @- http://localhost:8083/connectors --header "content-Type:application/json"
```

connector 설정

- $KAFKA_HOME/bin/kafka-console-producer.sh —broker-list [localhost:9092](http://localhost:9092) —topic my_topic_users

# Spring Kafka

consumer와 producer 모듈 양쪽에 dependency 추가

```jsx
implementation 'org.springframework.kafka:spring-kafka'
```

## Consumer

```jsx
@Slf4j
@Service
@RequiredArgsConstructor
public class KafkaConsumer {

    private final CatalogRepository catalogRepository;

    @KafkaListener(topics = "catalog-topic")
    public void updateQuantity(String kafkaMsg) {
        log.info("kafka msg : " + kafkaMsg);

        Map<String, Object> map = new HashMap<>();
        ObjectMapper mapper = new ObjectMapper();
        try{
            map = mapper.readValue(kafkaMsg, new TypeReference<Map<String, Object>>() {
            });
        } catch (JsonProcessingException e ){
            e.printStackTrace();
        }

        CatalogEntity catalogEntity = catalogRepository.findByProductId((String) map.get("productId"));
        if (catalogEntity != null) {
            catalogEntity.setStock(catalogEntity.getStock() - (Integer) map.get("quantity"));
            catalogRepository.save(catalogEntity);
        }
    }
}
```

```jsx
@EnableKafka
@Configuration
public class KafkaConsumerConfig {
    @Bean
    public ConsumerFactory<String, String> consumerFactory(){
        Map<String, Object> properties = new HashMap<>();
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "127.0.0.1:9092");
        properties.put(ConsumerConfig.GROUP_ID_CONFIG, "consumerGroupId");
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);

        return new DefaultKafkaConsumerFactory<>(properties);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory(){
        ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory
                = new ConcurrentKafkaListenerContainerFactory<>();

        kafkaListenerContainerFactory.setConsumerFactory(consumerFactory());
        return kafkaListenerContainerFactory;
    }
}
```

## Producer

```jsx

@EnableKafka
@Configuration
public class KafkaProducerConfig {
    @Bean
    public ProducerFactory<String, String> producerFactory(){
        Map<String, Object> properties = new HashMap<>();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "127.0.0.1:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);

        return new DefaultKafkaProducerFactory<>(properties);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate(){
        return new KafkaTemplate<>(producerFactory());
    }
}
```

```jsx
@Slf4j
@Service
@RequiredArgsConstructor
public class KafkaProducer {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public OrderDto send(String topic, OrderDto orderDto) {
        ObjectMapper mapper = new ObjectMapper();
        String jsonInString = "";
        try {
            jsonInString = mapper.writeValueAsString(orderDto);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        kafkaTemplate.send(topic, jsonInString);
        log.info("kafka sent this dto:"+orderDto);
        return orderDto;
    }
}
```

```jsx
@PostMapping("/{userId}/orders")
    public ResponseEntity<ResponseOrder> createOrder(
            @PathVariable("userId")String userId,
            @RequestBody RequestOrder orderDetail) {
        ModelMapper modelMapper = new ModelMapper();
        modelMapper.getConfiguration().setMatchingStrategy(MatchingStrategies.STRICT);

        OrderDto orderDto = modelMapper.map(orderDetail, OrderDto.class);
        orderDto.setUserId(userId);

        /* jpa
        OrderDto createdOrder = orderService.createOrder(orderDto);
        ResponseOrder responseOrder = modelMapper.map(createdOrder, ResponseOrder.class);
        */

        // send by kafka

        orderDto.setOrderId(UUID.randomUUID().toString());
        orderDto.setTotalPrice(orderDetail.getUnitPrice() * orderDetail.getQuantity());
        ResponseOrder responseOrder = modelMapper.map(orderDto, ResponseOrder.class);

        kafkaProducer.send("catalog-topic", orderDto);
        orderProducer.send("orders", orderDto);

        return ResponseEntity.status(HttpStatus.CREATED).body(responseOrder);
```

```jsx

// order 서비스 인스턴스들이 1개 DB를 사용하도록 
// 주문 생성시 DB에 insert하는게 아니라 kafka로 보낸 후, kafka에서 DB로 insert
public class OrderProducer {

    private final KafkaTemplate<String, String> kafkaTemplate;

    List<Field> fields = Arrays.asList(
            new Field("string", true, "order_id"),
            new Field("string", true, "user_id"),
            new Field("string", true, "product_id"),
            new Field("int32", true, "quantity"),
            new Field("int32", true, "unit_price"),
            new Field("int32", true, "total_price")
    );

    Schema schema = Schema.builder()
            .type("struct")
            .fields(fields)
            .optional(false)
            .name("orders")
            .build();

    public OrderDto send(String topic, OrderDto orderDto) {

        Payload payload = Payload.builder()
                .order_id(orderDto.getOrderId())
                .product_id(orderDto.getProductId())
                .user_id(orderDto.getUserId())
                .quantity(orderDto.getQuantity())
                .total_price(orderDto.getTotalPrice())
                .unit_price(orderDto.getUnitPrice())
                .build();

        KafkaOrderDto kafkaOrderDto = KafkaOrderDto.builder()
                .schema(schema)
                .payload(payload)
                .build();

        ObjectMapper mapper = new ObjectMapper();
        String jsonInString = "";
        try {
            jsonInString = mapper.writeValueAsString(kafkaOrderDto);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        kafkaTemplate.send(topic, jsonInString);
        log.info("kafka sent this dto:"+kafkaOrderDto);
        return orderDto;
    }

}
```