---
title: 'Tutorial: processar eventos de Apache Kafka usando o Stream Analytics-hubs de eventos do Azure'
description: 'Tutorial: Este artigo mostra como processar eventos Kafka que são ingeridos por meio de hubs de eventos usando Azure Stream Analytics'
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 7801b3252ab13df1f92e7aa5e0eba071195cb76c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720611"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Tutorial: processar Apache Kafka para eventos de hubs de eventos usando o Stream Analytics 
Este artigo mostra como transmitir dados em hubs de eventos habilitados para Kafka e processá-los com Azure Stream Analytics. Ele orienta você pelas seguintes etapas: 

1. Crie um namespace de hubs de eventos habilitados para Kafka.
2. Crie um cliente Kafka que envia mensagens para o Hub de eventos.
3. Crie um trabalho de Stream Analytics que copia dados do hub de eventos para um armazenamento de BLOBs do Azure. 

Você não precisa alterar seus clientes de protocolo nem executar seus próprios clusters ao usar o ponto de extremidade Kafka exposto por um hub de eventos. Os Hubs de Eventos do Azure suportam o [Apache Kafka versão 1.0](https://kafka.apache.org/10/documentation.html) e acima. 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem os seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven.
* [Git](https://www.git-scm.com/)
* Uma **conta de armazenamento do Azure**. Se você não tiver uma, [crie uma](../storage/common/storage-quickstart-create-account.md) antes de continuar. O trabalho de Stream Analytics neste passo a passos armazena os dados de saída em um armazenamento de BLOBs do Azure. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos ativado para Kafka

1. Entre no [portal do Azure](https://portal.azure.com)e clique em **criar um recurso** na parte superior esquerda da tela.
2. Procure por **hubs de eventos** e selecione as opções mostradas aqui:
    
    ![Procurar por Hubs de Eventos no portal](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Na página **hubs de eventos** , selecione **criar**.
4. Na página **criar namespace** , execute as seguintes ações: 
    1. Forneça um **nome** exclusivo para o namespace. 
    2. Selecione um **tipo de preço**. 
    3. Selecione **habilitar Kafka**. Esta etapa é uma etapa **importante** . 
    4. Selecione sua **assinatura** na qual você deseja que o namespace do hub de eventos seja criado. 
    5. Crie um novo **grupo de recursos** ou selecione um grupo de recursos existente. 
    6. Selecione um **local**. 
    7. Clique em **Criar**.
    
        ![Criar um espaço de nomes](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. Na **mensagem de notificação**, selecione o **nome do grupo de recursos**. 

    ![Criar um espaço de nomes](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Selecione o **namespace do hub de eventos** no grupo de recursos. 
2. Depois que o namespace for criado, selecione **políticas de acesso compartilhado** em **configurações**.

    ![Clique em Políticas de acesso partilhado](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Pode escolher **RootManageSharedAccessKey** predefinida ou adicionar uma política nova. Clique no nome da política e copie a **cadeia de conexão**. Você usa a cadeia de conexão para configurar o cliente Kafka. 
    
    ![Selecionar uma política](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Agora, já pode transmitir em fluxo eventos das suas aplicações que utilizam o protocolo Kafka para os Hubs de eventos.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Enviar mensagens com Kafka nos hubs de eventos

1. Clone os [hubs de eventos do Azure para o repositório Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) em seu computador.
2. Navegue até a pasta: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Atualize os detalhes de configuração para o produtor no `src/main/resources/producer.config`. Especifique o **nome** e a **cadeia de conexão** para o namespace do hub de **eventos**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navegue até `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`e abra o arquivo **TestDataReporter. java** em um editor de sua escolha. 
6. Comente a seguinte linha de código:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adicione a seguinte linha de código no lugar do código comentado: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Esse código envia os dados de evento no formato **JSON** . Ao configurar a entrada para um trabalho Stream Analytics, você especifica JSON como o formato dos dados de entrada. 
7. **Execute o produtor e o** Stream em hubs de eventos habilitados para Kafka. Em um computador Windows, ao usar um **prompt de comando do node. js**, alterne para a pasta `azure-event-hubs-for-kafka/quickstart/java/producer` antes de executar esses comandos. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Verificar se o Hub de eventos recebe os dados

1. Selecione os **hubs de eventos** em **entidades**. Confirme que você vê um hub de eventos chamado **teste**. 

    ![Hub de eventos-teste](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Confirme que você vê as mensagens que chegam ao Hub de eventos. 

    ![Hub de eventos-mensagens](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Processar dados de evento usando um trabalho de Stream Analytics
Nesta seção, você cria um trabalho de Azure Stream Analytics. O cliente Kafka envia eventos para o Hub de eventos. Você cria um trabalho de Stream Analytics que usa dados de evento como entrada e os gera em um armazenamento de BLOBs do Azure. Se você não tiver uma **conta de armazenamento do Azure**, [crie uma](../storage/common/storage-quickstart-create-account.md).

A consulta no trabalho de Stream Analytics passa pelos dados sem executar nenhuma análise. Você pode criar uma consulta que transforma os dados de entrada para produzir dados de saída em um formato diferente ou com informações obtidas.  

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics 

1. Selecione **+ criar um recurso** no [portal do Azure](https://portal.azure.com).
2. Selecione **análise** no menu **do Azure Marketplace** e selecione **Stream Analytics trabalho**. 
3. Na página **novo Stream Analytics** , execute as seguintes ações: 
    1. Insira um **nome** para o trabalho. 
    2. Selecione sua **assinatura**.
    3. Selecione **criar novo** para o **grupo de recursos** e insira o nome. Você também pode **usar um grupo de recursos existente** . 
    4. Selecione um **local** para o trabalho.
    5. Selecione **criar** para criar o trabalho. 

        ![Novo trabalho de Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. Na mensagem de notificação, selecione **ir para o recurso** para ver a página **Stream Analytics trabalho** . 
2. Selecione **entradas** na seção **topologia de trabalho** no menu à esquerda.
3. Selecione **Adicionar entrada de fluxo**e, em seguida, selecione **Hub de eventos**. 

    ![Adicionar o Hub de eventos como uma entrada](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na página configuração de **entrada do hub de eventos** , execute as seguintes ações: 

    1. Especifique um **alias** para a entrada. 
    2. Selecione a sua **subscrição do Azure**.
    3. Selecione o **namespace do hub de eventos** criado anteriormente. 
    4. Selecione **teste** para o **Hub de eventos**. 
    5. Selecione **Guardar**. 

        ![Configuração de entrada do hub de eventos](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurar a saída da tarefa 

1. Selecione **saídas** na seção **topologia de trabalho** no menu. 
2. Selecione **+ Adicionar** na barra de ferramentas e selecione **armazenamento de BLOBs**
3. Na página Configurações de saída do armazenamento de BLOBs, execute as seguintes ações: 
    1. Especifique um **alias** para a saída. 
    2. Selecione a sua **subscrição** do Azure. 
    3. Selecione sua **conta de armazenamento do Azure**. 
    4. Insira um **nome para o contêiner** que armazena os dados de saída da consulta Stream Analytics.
    5. Selecione **Guardar**.

        ![Configuração de saída do armazenamento de BLOBs](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definir uma consulta
Depois de ter uma tarefa do Stream Analytics configurada para ler um fluxo de dados de entrada, o passo seguinte consiste em criar uma transformação que analisa os dados em tempo real. Para definir a consulta de transformação, vai utilizar a [Linguagem de Consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Neste tutorial, você define uma consulta que passa pelos dados sem executar nenhuma transformação.

1. Selecione **consulta**.
2. Na janela de consulta, substitua `[YourOutputAlias]` pelo alias de saída que você criou anteriormente.
3. Substitua `[YourInputAlias]` pelo alias de entrada que você criou anteriormente. 
4. Selecione **Guardar** na barra de ferramentas. 

    ![Consulta](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Selecione **visão geral** no menu à esquerda. 
2. Selecione **Iniciar**. 

    ![Menu iniciar](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na página **Iniciar trabalho** , selecione **Iniciar**. 

    ![Página Iniciar trabalho](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Aguarde até que o status do trabalho seja alterado de **iniciando** para **em execução**. 

    ![Status do trabalho-em execução](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testar o cenário
1. Execute o **produtor do Kafka** novamente para enviar eventos para o Hub de eventos. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Confirme que você vê que os **dados de saída** são gerados no **armazenamento de BLOBs do Azure**. Você verá um arquivo JSON no contêiner com 100 linhas que se parecem com as seguintes linhas de exemplo: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    O trabalho de Azure Stream Analytics recebeu dados de entrada do hub de eventos e os armazenou no armazenamento de BLOBs do Azure neste cenário. 



## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a transmitir para os Hubs de Eventos ativados para Kafka, sem alterar os clientes de protocolo nem executar os seus próprios clusters. Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Como criar os Hubs de Eventos ativados para Kafka)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mirror a Kafka broker in a Kafka-enabled event hub](event-hubs-kafka-mirror-maker-tutorial.md) (Espelhar um mediador de Kafka num hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Ligar o Apache Spark a um hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-flink-tutorial.md) (Ligar o Apache Flink a um hub de eventos com Kafka ativado)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Integrar o Kafka Connect num hub de eventos com Kafka ativado)
- [Connect Akka Streams to a Kafka-enabled event hub](event-hubs-kafka-akka-streams-tutorial.md) (Ligar o Akka Streams a um hub de eventos com Kafka ativado)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub) 
