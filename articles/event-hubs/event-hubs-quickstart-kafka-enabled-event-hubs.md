---
title: Transmitir em fluxo para os Hubs de Eventos do Azure para o Apache Kafka | Microsoft Docs
description: Transmita para os Hubs de Eventos, com o protocol Kafka e APIs.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: 4b78cef29c64c5c4c522ad5c751c10bbf6a7057c
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363423"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Transmitir em fluxo para os Hubs de Eventos para o Apache Kafka
Este início rápido mostra como transmitir para Hubs de Eventos ativados pelo Kafka, sem alterar os clientes de protocolo nem a execução dos seus próprios clusters. Vai aprender a utilizar os seus produtores e consumidores para comunicar com Hubs de Eventos ativados pelo Kafka, com apenas uma alteração de configuração nas aplicações. Os Hubs de Eventos do Azure suportam o [Apache Kafka versão 1.0](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Transferir](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um arquivo binário Maven.
* [Git](https://www.git-scm.com/)
* [Um espaço de nomes de Hubs de Eventos ativado pelo Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos ativado para Kafka

1. Inicie sessão no [portal do Azure][Azure portal] e clique em **Criar um recurso**, no canto superior esquerdo do ecrã.

2. Procure por Hubs de Eventos e selecione as opções mostradas aqui:
    
    ![Procurar por Hubs de Eventos no portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Indique um nome exclusivo e ative o Kafka no espaço de nomes. Clique em **Criar**.
    
    ![Criar um espaço de nomes](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
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

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a transmitir para os Hubs de Eventos ativados para Kafka, sem alterar os clientes de protocolo nem executar os seus próprios clusters. Para obter mais informações, avance para o próximo tutorial:

* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos sobre os Hubs de Eventos do GitHub do Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Utilize o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos do Kafka no local para Hubs de Eventos ativados pelo Kafka na cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir para os Hubs de Eventos ativados pelo Kafka através do [Apache Flink](event-hubs-kafka-flink-tutorial.md) ou [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
