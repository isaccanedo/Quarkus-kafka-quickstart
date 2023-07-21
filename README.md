Início rápido do Quarkus Kafka
========================

Este projeto ilustra como os aplicativos Quarkus podem interagir com o Apache Kafka usando MicroProfile Reactive Messaging.

## Inicie o aplicativo

O aplicativo é composto por dois aplicativos que se comunicam por meio do Kafka.
As interações com o Kafka são gerenciadas pelo MicroProfile Reactive Messaging.

Eles podem ser iniciados no modo dev usando:

```bash
mvn -f producer quarkus:dev
```

e em outro terminal:

```bash
mvn -f processor quarkus:dev
```

_NOTA_: Quarkus Dev Services inicia um agente Kafka para você automaticamente.

Em seguida, abra seu navegador em `http://localhost:8080/quotes.html`.
Você pode enviar solicitações de cotação e observar as cotações recebidas.

## Anatomia

O aplicativo é composto pelos seguintes componentes:

#### Produtor

O aplicativo _produtor_ recebe solicitações do usuário (via HTTP) e envia _solicitações de cotações_ ao corretor Kafka.
Dois componentes principais compõem o aplicativo:

* `QuoteProducer` gera solicitações de cotação identificadas de forma exclusiva e as envia para o tópico Kafka `quote-requests`.
Ele também consome o tópico `quotes` do Kafka e retransmite as mensagens recebidas para o navegador usando eventos enviados pelo servidor.
* `quotes.html` envia pedidos de cotação para o terminal anterior e atualiza as cotações com os preços recebidos.

#### Processor

The _processor_ application receives quote requests from Kafka, processes them, and writes results into the `quotes` Kafka topic.
The application has one main class:

* `QuoteProcessor` consumes quote request ids from the `quote-requests` Kafka topic and responds back to the `quotes` topic with a `Quote` object containing a random price.

The connection to Kafka is configured in the `src/main/resources/application.properties` file.

## Running the application in Docker

To run the application in Docker, first make sure that both services are built:

```bash
mvn package
```

Then launch Docker Compose:

```bash
docker-compose up
```

This will create a single-node Kafka cluster and launch both applications.

## Running in native

You can compile the application into a native binary using:

```bash
mvn package -Dnative
```

As you are running in _prod_ mode, you need a Kafka cluster.

If you have Docker installed, you can simply run:

```bash
export QUARKUS_MODE=native
docker-compose up --build
```

Alternatively, you can follow the instructions from the [Apache Kafka web site](https://kafka.apache.org/quickstart).

Then run both applications respectively with:

```bash
./producer/target/kafka-quickstart-producer-1.0.0-SNAPSHOT-runner
```

and in another terminal:

```bash
./processor/target/kafka-quickstart-processor-1.0.0-SNAPSHOT-runner
```
