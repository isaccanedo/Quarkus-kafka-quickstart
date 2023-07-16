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

and in another terminal:

```bash
mvn -f processor quarkus:dev
```

_NOTE_: Quarkus Dev Services starts a Kafka broker for you automatically.

Then, open your browser at `http://localhost:8080/quotes.html`.
You can send quote requests and observe received quotes.

## Anatomy

The application is composed of the following components:

#### Producer

The _producer_ application receive requests from the user (via HTTP) and sends _quote requests_ to the Kafka broker.
Two main components compose the application:

* `QuoteProducer` generates uniquely identified quote requests and sends them to the Kafka topic `quote-requests`.
It also consumes the Kafka topic `quotes` and relays received messages to the browser using Server-Sent Events.
* `quotes.html` sends quote requests to the previous endpoint and updates quotes with received prices.

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
