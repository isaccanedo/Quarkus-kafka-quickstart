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

#### Processador

O aplicativo _processor_ recebe solicitações de cotação do Kafka, processa-as e grava os resultados no tópico `quotes` do Kafka.
O aplicativo tem uma classe principal:

* `QuoteProcessor` consome ids de solicitação de cotação do tópico Kafka `quote-requests` e responde de volta ao tópico `quotes` com um objeto `Quote` contendo um preço aleatório.

A conexão com o Kafka é configurada no arquivo `src/main/resources/application.properties`.

## Executando o aplicativo no Docker

Para executar o aplicativo no Docker, primeiro verifique se os dois serviços foram criados:
```bash
mvn package
```

Em seguida, inicie o Docker Compose:

```bash
docker-compose up
```

Isso criará um cluster Kafka de nó único e iniciará os dois aplicativos.

## Executando em nativo

Você pode compilar o aplicativo em um binário nativo usando:

```bash
mvn package -Dnative
```

Como você está executando no modo _prod_, precisa de um cluster Kafka.

Se você tiver o Docker instalado, basta executar:

```bash
export QUARKUS_MODE=native
docker-compose up --build
```

Como alternativa, você pode seguir as instruções do [site do Apache Kafka](https://kafka.apache.org/quickstart).

Em seguida, execute os dois aplicativos, respectivamente, com:

```bash
./producer/target/kafka-quickstart-producer-1.0.0-SNAPSHOT-runner
```

e em outro terminal:

```bash
./processor/target/kafka-quickstart-processor-1.0.0-SNAPSHOT-runner
```
