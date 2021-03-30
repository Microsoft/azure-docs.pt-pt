---
title: 'Quickstart: Streaming de dados com Azure Event Hubs usando o protocolo Kafka'
description: 'Quickstart: Este artigo fornece informações sobre como transmitir para Azure Event Hubs usando o protocolo kafka e APIs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 2020534a3984453bcd6eff7ad0f5c02d9e7a29ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368354"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Quickstart: Streaming de dados com Centros de Eventos utilizando o protocolo Kafka
Este quickstart mostra como transmitir para Os Centros de Eventos sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. Aprende a usar os seus produtores e consumidores para falar com o Event Hubs com apenas uma alteração de configuração nas suas aplicações. 

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
Quando cria um espaço de nome de nível **padrão,** o ponto final de Kafka para o espaço de nome é automaticamente ativado. Você pode transmitir eventos a partir das suas aplicações que usam o protocolo Kafka em centros de eventos de nível padrão. Siga as instruções passo a passo no centro de eventos Criar um centro de eventos utilizando o [portal Azure](event-hubs-create.md) para criar um espaço de nomes **padrão** de centros de eventos de nível padrão. 

> [!NOTE]
> Os Centros de Eventos para Kafka estão disponíveis apenas em níveis **standard** e **dedicados.** O nível **básico** não suporta Kafka em Centros de Eventos.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Enviar e receber mensagens com Kafka em Hubs de Eventos

1. Clone o [repositório de Hubs de Eventos do Azure para Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navegue para `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Atualize os detalhes de configuração do produtor em `src/main/resources/producer.config`, da seguinte forma:

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
    > [!IMPORTANT]
    > `{YOUR.EVENTHUBS.CONNECTION.STRING}`Substitua-a pela cadeia de ligação para o seu espaço de nomes 'Centros de Eventos'. Para obter instruções sobre a obtenção da cadeia de ligação, consulte [obter uma cadeia de ligação Dos Centros de Eventos](event-hubs-get-connection-string.md). Aqui está uma configuração de exemplo: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Pode encontrar o código-fonte para a classe de manipulador de amostras CustomAuthenticateCallbackHandler no GitHub [aqui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Executar o código do produtor e transmitir eventos em Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navegue para `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Atualize os detalhes de configuração para o consumidor em `src/main/resources/consumer.config`, da seguinte forma:
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    > [!IMPORTANT]
    > `{YOUR.EVENTHUBS.CONNECTION.STRING}`Substitua-a pela cadeia de ligação para o seu espaço de nomes 'Centros de Eventos'. Para obter instruções sobre a obtenção da cadeia de ligação, consulte [obter uma cadeia de ligação Dos Centros de Eventos](event-hubs-get-connection-string.md). Aqui está uma configuração de exemplo: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Pode encontrar o código-fonte para a classe de manipulador de amostras CustomAuthenticateCallbackHandler no GitHub [aqui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Você pode encontrar todas as amostras de OAuth para Centros de Eventos para Kafka [aqui.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)
7. Executar o código de consumo e processar eventos a partir do centro de eventos usando os seus clientes Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se o cluster Kafka dos Hubs de Eventos tiver eventos, começará agora a recebê-los do consumidor.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a transmitir para os Centros de Eventos sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. Para saber mais, consulte [o guia de desenvolvimento apache Kafka para o Azure Event Hubs.](apache-kafka-developer-guide.md)