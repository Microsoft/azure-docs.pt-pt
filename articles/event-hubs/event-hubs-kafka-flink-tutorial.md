---
title: Utilizar Apache Flink com os Hubs de eventos do Azure para o Apache Kafka | Documentos da Microsoft
description: Ligação Apache Flink para um Kafka ativado para o hub de eventos
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 08/06/2018
ms.author: bahariri
ms.openlocfilehash: 28ebcd0fe10492b730d73e19f9206545084b4030
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277346"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Utilizar Apache Flink com os Hubs de eventos do Azure para o Apache Kafka
Este tutorial mostra-lhe como ligar Apache Flink aos hubs de eventos de Kafka-ativado sem alterar os seus clientes de protocolo ou os seus próprios clusters em execução. Os Hubs de eventos do Azure suporta [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html).

Uma das principais vantagens da utilização do Apache Kafka é o ecossistema das estruturas que pode estabelecer ligação ao. Kafka ativada combina os Hubs de eventos a flexibilidade do Kafka com a escalabilidade, consistência e suporte do ecossistema do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Execute o produtor Flink 
> * Execução de Flink de consumidor

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, certifique-se de que tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Baixe](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) num arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nomes de Hubs de eventos é necessário para enviar ou receber a partir de qualquer serviço de Hubs de eventos. Ver [criar Kafka ativado os Hubs de eventos](event-hubs-create-kafka-enabled.md) para obter informações sobre como obter um ponto de extremidade do Kafka de Hubs de eventos. Certifique-se copiar a cadeia de ligação de Hubs de eventos para utilização posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que tem uma cadeia de ligação de Hubs de eventos habilitados no Kafka, clonar os Hubs de eventos do Azure para o repositório do Kafka e navegue para o `flink` subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Execute o produtor Flink

Usando o exemplo de produtor Flink fornecido, envie mensagens para o serviço de Hubs de eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade do Kafka de Hubs de eventos

#### <a name="producerconfig"></a>Producer.config

Atualização do `bootstrap.servers` e `sasl.jaas.config` valores em `producer/src/main/resources/producer.config` para direcionar o produtor para o ponto de final do Kafka de Hubs de eventos com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Execute o produtor a partir da linha de comandos

Para executar o produtor da linha de comando, gerar JAR e, em seguida, execute a partir de Maven (ou gerar JAR com o Maven, em seguida, executar em Java, adicionando o JAR(s) de Kafka necessário para o caminho da classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

O produtor começará agora a enviar eventos para o Kafka ativado para o Hub de eventos no tópico `test` e imprimir os eventos para stdout.

## <a name="run-flink-consumer"></a>Execução de Flink de consumidor

Usando o exemplo de consumidor fornecido, receber mensagens dos Hubs de eventos de ativado o Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade do Kafka de Hubs de eventos

#### <a name="consumerconfig"></a>Consumer.config

Atualização do `bootstrap.servers` e `sasl.jaas.config` valores em `consumer/src/main/resources/consumer.config` para direcionar o consumidor para o ponto de final do Kafka de Hubs de eventos com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Execute o consumidor da linha de comando

Para executar o consumidor da linha de comando, gerar JAR e, em seguida, execute a partir de Maven (ou gerar JAR com o Maven, em seguida, executar em Java, adicionando o JAR(s) de Kafka necessário para o caminho da classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Se o hub de eventos habilitados no Kafka tem eventos (por exemplo, se o produtor também estiver em execução), em seguida, o consumidor começa agora a receção de eventos do tópico `test`.

Confira [guia de conector do Kafka do Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) para obter mais informações sobre a ligação Flink ao Kafka.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, a aprender como ligar Apache Flink aos hubs de eventos de Kafka-ativado sem alterar os seus clientes de protocolo ou os seus próprios clusters em execução. Efetuar os passos seguintes como parte deste Tutorial: 

> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Execute o produtor Flink 
> * Execução de Flink de consumidor

Para saber mais sobre os Hubs de eventos e os Hubs de eventos para o Kafka, consulte o tópico seguinte:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Hubs de eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Como criar Kafka ativado os Hubs de eventos](event-hubs-create-kafka-enabled.md)
- [Stream nos Hubs de eventos a partir das suas aplicações de Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Espelhar um mediador Kafka um hub de eventos de Kafka-ativada](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Spark para um hub de eventos de Kafka-ativada](event-hubs-kafka-spark-tutorial.md)
- [Integrar o Kafka estabelecer ligação com um hub de eventos de Kafka-ativada](event-hubs-kafka-connect-tutorial.md)
- [Ligar Akka fluxos para um hub de eventos de Kafka-ativada](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore exemplos no nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
