---
title: Stream para hub de eventos habilitados no Kafka - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como stream, nos Hubs de eventos do Azure utilizando o protocolo de Kafka e APIs.
services: event-hubs
author: basilhariri
ms.author: bahariri
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.openlocfilehash: 4fec60cdf3fcf24378567ea97dd7922c805cde18
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542935"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Transmitir em fluxo para os Hubs de Eventos para o Apache Kafka
Este início rápido mostra como transmitir para Hubs de Eventos ativados pelo Kafka, sem alterar os clientes de protocolo nem a execução dos seus próprios clusters. Vai aprender a utilizar os seus produtores e consumidores para comunicar com Hubs de Eventos ativados pelo Kafka, com apenas uma alteração de configuração nas aplicações. Os Hubs de Eventos do Azure suportam o [Apache Kafka versão 1.0](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven.
* [Git](https://www.git-scm.com/)
* [Um espaço de nomes de Hubs de Eventos ativado pelo Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos ativado para Kafka

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e clique em **criar um recurso** na parte superior esquerda do ecrã.

2. Procure por Hubs de Eventos e selecione as opções mostradas aqui:
    
    ![Procurar por Hubs de Eventos no portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Indique um nome exclusivo e ative o Kafka no espaço de nomes. Clique em **Criar**. Nota: Os Hubs de eventos para o Kafka é apenas suportada pelo Standard e escalão dos Hubs de eventos dedicado. Escalão básico dos Hubs de eventos irá devolver um erro de autorização de tópico em resposta a todas as operações de Kafka.
    
    ![Criar um espaço de nomes](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Quando o espaço de nomes for criado, no separador **Definições**, clique em **Políticas de acesso partilhado** para obter a cadeia de ligação.

    ![Clique em Políticas de acesso partilhado](./media/event-hubs-create/create-event-hub7.png)

5. Pode escolher **RootManageSharedAccessKey** predefinida ou adicionar uma política nova. Clique no nome da política e copie a cadeia de ligação. 
    
    ![Selecionar uma política](./media/event-hubs-create/create-event-hub8.png)
 
6. Adicione essa cadeia de ligação à sua configuração do Kafka.

Agora, já pode transmitir em fluxo eventos das suas aplicações que utilizam o protocolo Kafka para os Hubs de eventos.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Enviar e receber mensagens com Kafka em Hubs de Eventos

1. Clone o [repositório de Hubs de Eventos do Azure para Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navegue para `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Atualize os detalhes de configuração do produtor em `src/main/resources/producer.config`, da seguinte forma:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
4. Execute o código de produtor e transmita em fluxo para os Hubs de Eventos ativados pelo Kafka:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navegue para `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Atualize os detalhes de configuração para o consumidor em `src/main/resources/consumer.config`, da seguinte forma:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Execute o código e processo de consumidor, a partir de Hubs de Eventos ativados pelo Kafka, com os seus clientes do Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se o cluster Kafka dos Hubs de Eventos tiver eventos, começará agora a recebê-los do consumidor.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a transmitir para os Hubs de Eventos ativados para Kafka, sem alterar os clientes de protocolo nem executar os seus próprios clusters. Para obter mais informações, avance para o próximo tutorial:

* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos sobre os Hubs de Eventos do GitHub do Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Uso [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [eventos de fluxo do Kafka no local para o Kafka ativado os Hubs de eventos na cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir para os Hubs de Eventos ativados pelo Kafka através do [Apache Flink](event-hubs-kafka-flink-tutorial.md) ou [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
