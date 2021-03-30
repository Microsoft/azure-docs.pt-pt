---
title: Conecte-se com a sua aplicação Apache Spark - Azure Event Hubs | Microsoft Docs
description: Este artigo fornece informações sobre como usar Apache Spark com Azure Event Hubs para Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84184ed3dffee97863b93c592d1cd577df313605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913743"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Ligue a sua aplicação Apache Spark com Azure Event Hubs
Este tutorial acompanha-o através da ligação da sua aplicação Spark aos Centros de Eventos para streaming em tempo real. Esta integração permite o streaming sem ter de alterar os seus clientes protocolares, ou executar os seus próprios clusters Kafka ou Zookeeper. Este tutorial requer Apache Spark v2.4+ e Apache Kafka v2.0+.

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
É necessário um espaço de nomes dos Hubs de Eventos para enviar e receber a partir de qualquer serviços dos Hubs de Eventos. Consulte [a criação de um centro de eventos](event-hubs-create.md) para obter instruções para criar um espaço de nome e um centro de eventos. Obtenha a cadeia de ligação dos Hubs de Eventos e o nome de domínio completamente qualificado (FQDN), para utilizar mais tarde. Para obter instruções, veja [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Obter uma cadeia de ligação dos Hubs de Eventos). 

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
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Escrever nos Hubs de Eventos para Kafka
Também pode escrever para o Event Hubs da mesma forma que escreve para Kafka. Não se esqueça de atualizar a configuração para alterar **BOOTSTRAP_SERVERS** e **EH_SASL** com as informações do espaço de nomes dos Hubs de Eventos.  Para obter o código de exemplo completo, veja o ficheiro sparkProducer.scala no GitHub. 

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
Para saber mais sobre Os Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integre kafka Connect com um centro de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
- [Ligar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia de desenvolvimento apache Kafka para hubs de eventos Azure](apache-kafka-developer-guide.md)

