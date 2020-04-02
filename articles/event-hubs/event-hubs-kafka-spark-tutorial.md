---
title: Conecte-se com a sua app Apache Spark - Azure Event Hubs / Microsoft Docs
description: Este artigo fornece informações sobre como usar Apache Spark com Hubs de Eventos Azure para Kafka.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 70f55891874b474de4120d8b41594fb9bee74e8d
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521651"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Ligue a sua aplicação Apache Spark com hubs de eventos Azure
Este tutorial acompanha-o através da ligação da sua aplicação Spark aos Centros de Eventos para streaming em tempo real. Esta integração ativa a transmitir em fluxo sem que seja necessário alterar os clientes de protocolo ou executar os seus próprios clusters do Kafka ou do Zookeeper. Este tutorial requer o Apache Spark v2.4+ e o Apache Kafka v2.0+.

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar o Spark
> * Ler a partir Hubs de Eventos para Kafka
> * Escrever nos Hubs de Eventos para Kafka

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar o tutorial, confirme que tem:
-   Subscrição do Azure. Se não tiver uma subscrição, [crie uma conta gratuita](https://azure.microsoft.com/free/).
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> O adaptador Spark-Kafka foi atualizado para suportar o Kafka 2.0 a partir do Spark v2.4. Nas versões anteriores do Spark, o adaptador suportava o Kafka v0.10 e posteriores, mas dependia especificamente das APIs do Kafka v0.10. Uma vez que os Hubs de Eventos para Kafka não suportam o Kafka v0.10, os ecossistemas dos Hubs de Eventos para Kafka não suportam os adaptadores Spark-Kafka de versões do Spark anteriores à v2.4.


## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
É necessário um espaço de nomes dos Hubs de Eventos para enviar e receber a partir de qualquer serviços dos Hubs de Eventos. Consulte [a Criação de um centro](event-hubs-create.md) de eventos para instruções para criar um espaço de nome e um centro de eventos. Obtenha a cadeia de ligação dos Hubs de Eventos e o nome de domínio completamente qualificado (FQDN), para utilizar mais tarde. Para obter instruções, veja [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Obter uma cadeia de ligação dos Hubs de Eventos). 

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo
Clone o repositório dos Hubs de Eventos do Azure e navegue para a subpasta `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Ler a partir Hubs de Eventos para Kafka
Com poucas alterações à configuração, pode começar a ler a partir dos Hubs de Eventos para Kafka. Atualize **BOOTSTRAP_SERVERS** e **EH_SASL** com detalhes do espaço de nomes e pode começar a transmitir em fluxo com os Hubs de Eventos tal como faria com o Kafka. Para obter o código de exemplo completo, veja o ficheiro sparkConsumer.scala no GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Escrever nos Hubs de Eventos para Kafka
Também pode escrever para o Event Hubs da mesma forma que escreve a Kafka. Não se esqueça de atualizar a configuração para alterar **BOOTSTRAP_SERVERS** e **EH_SASL** com as informações do espaço de nomes dos Hubs de Eventos.  Para obter o código de exemplo completo, veja o ficheiro sparkProducer.scala no GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu utilizar o conector Spark-Kafka e os Hubs de Eventos para Kafka para transmitir em fluxo. Realizou os seguintes passos: 

> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar o Spark
> * Ler a partir Hubs de Eventos para Kafka
> * Escrever nos Hubs de Eventos para Kafka

Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [Criar um hub de eventos](event-hubs-create.md)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar kafka Connect com um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Ligue a Akka Streams a um centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
