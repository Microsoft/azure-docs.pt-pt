---
title: Azure Event Hubs - Eventos De Processo Apache Kafka
description: 'Tutorial: Este artigo mostra como processar eventos de Kafka que são ingeridos através de centros de eventos usando o Azure Stream Analytics'
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 4cabd63dc39590a9bf728528f3e7b3aa5d3f6b04
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521764"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Tutorial: Processa Apache Kafka para eventos de Event Hubs usando análise de stream 
Este artigo mostra como transmitir dados para Os Hubs de Eventos e processá-lo com o Azure Stream Analytics. Ele acompanha-te pelos seguintes passos: 

1. Crie um espaço de nomes dos Hubs de Eventos.
2. Crie um cliente Kafka que envie mensagens para o centro do evento.
3. Crie um trabalho de Stream Analytics que copie dados do centro do evento para um armazenamento de blob Azure. 

Não precisa de alterar os seus clientes de protocolo ou executar os seus próprios clusters quando utiliza o ponto final de Kafka exposto por um centro de eventos. Os Hubs de Eventos do Azure suportam o [Apache Kafka versão 1.0](https://kafka.apache.org/10/documentation.html) e acima. 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem os seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven.
* [Git](https://www.git-scm.com/)
* Uma **conta de Armazenamento Azure.** Se não tem um, [crie um](../storage/common/storage-account-create.md) antes de prosseguir. O trabalho do Stream Analytics neste walkthrough armazena os dados de saída num armazenamento de blob Azure. 


## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
Quando cria **um** espaço de nome padrão do Event Hubs, o ponto final de Kafka para o espaço de nome está ativado automaticamente. Você pode transmitir eventos a partir das suas aplicações que usam o protocolo Kafka em centros de eventos de nível padrão. Siga instruções passo a passo no [Create a event hub utilizando o portal Azure](event-hubs-create.md) para criar um espaço de nome de hubs de evento de nível **padrão.** 

> [!NOTE]
> O Event Hubs para Kafka está disponível apenas em camadas **standard** e **dedicada.** O nível **básico** não suporta Kafka em Centros de Eventos.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Envie mensagens com Kafka em Centros de Eventos

1. Clone os Hubs de [Eventos Azure para o repositório Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) à sua máquina.
2. Navegue para `azure-event-hubs-for-kafka/quickstart/java/producer`a pasta: . 
4. Atualize os detalhes de `src/main/resources/producer.config`configuração para o produtor em . Especifique o **nome** e a cadeia de **ligação** para o espaço de nome do centro de **eventos**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navegue `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`para , e abra o ficheiro **TestDataReporter.java** num editor à sua escolha. 
6. Comentar a seguinte linha de código:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adicione a seguinte linha de código no lugar do código comentado: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Este código envia os dados do evento em formato **JSON.** Quando configura ruma a entrada para um trabalho de Stream Analytics, especifice o JSON como formato para os dados de entrada. 
7. **Execute o produtor** e flua para os Centros de Eventos. Numa máquina Windows, quando utilizar um pedido de comando `azure-event-hubs-for-kafka/quickstart/java/producer` **Node.js,** mude para a pasta antes de executar estes comandos. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Verifique se o centro de eventos recebe os dados

1. Selecione **Centros de Eventos** em **ENTIDADES**. Confirme que vê um centro de eventos chamado **teste.** 

    ![Centro de eventos - teste](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Confirme que vê mensagens a chegar ao centro do evento. 

    ![Centro de eventos - mensagens](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Processar dados de eventos usando um trabalho de Stream Analytics
Nesta secção, cria-se um trabalho de Azure Stream Analytics. O cliente Kafka envia eventos para o centro do evento. Cria-se um trabalho de Stream Analytics que leva os dados do evento como entrada e os produz para um armazenamento de blob Azure. Se não tiver uma **conta de Armazenamento Azure, crie** [uma](../storage/common/storage-account-create.md).

A consulta no trabalho do Stream Analytics passa pelos dados sem realizar qualquer análise. Pode criar uma consulta que transforme os dados de entrada para produzir dados de saída num formato diferente ou com insights adquiridos.  

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics 

1. Selecione **+ Crie um recurso** no portal [Azure](https://portal.azure.com).
2. Selecione **Analytics** no menu **Azure Marketplace** e selecione **o trabalho do Stream Analytics.** 
3. Na página **New Stream Analytics,** faça as seguintes ações: 
    1. Insira um **nome** para o trabalho. 
    2. Selecione a sua **subscrição**.
    3. Selecione **Criar novos** para o **grupo de recursos** e insira o nome. Também pode utilizar um grupo de recursos **existente.** 
    4. Selecione um **local** para o trabalho.
    5. Selecione **Criar** para criar o trabalho. 

        ![Novo trabalho de Analytics stream](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. Na mensagem de notificação, selecione **Ir a recurso** para ver a página de trabalho do Stream **Analytics.** 
2. Selecione **Inputs** na secção **JOB TOPOLOGY** no menu esquerdo.
3. **Selecione Adicionar entrada**de fluxo , e, em seguida, selecione Event **Hub**. 

    ![Adicione o centro de eventos como uma entrada](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na página de configuração de **entrada do Event Hub,** faça as seguintes ações: 

    1. Especifique um **pseudónimo** para a entrada. 
    2. Selecione a sua **subscrição Azure**.
    3. Selecione o espaço de nome do centro de **eventos** que criou anteriormente. 
    4. Selecione **o teste** para o centro de **eventos**. 
    5. Selecione **Guardar**. 

        ![Configuração de entrada do centro de eventos](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurar a saída da tarefa 

1. Selecione **Saídas** na secção **JOB TOPOLOGY** no menu. 
2. Selecione **+ Adicione** na barra de ferramentas e selecione **armazenamento Blob**
3. Na página de definições de saída de armazenamento Blob, faça as seguintes ações: 
    1. Especifique um **pseudónimo** para a saída. 
    2. Selecione a sua **subscrição** do Azure. 
    3. Selecione a sua **conta de Armazenamento Azure**. 
    4. Introduza um **nome para o recipiente** que armazena os dados de saída da consulta Stream Analytics.
    5. Selecione **Guardar**.

        ![Configuração de saída de armazenamento blob](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Defina uma consulta
Depois de ter uma tarefa do Stream Analytics configurada para ler um fluxo de dados de entrada, o passo seguinte consiste em criar uma transformação que analisa os dados em tempo real. Para definir a consulta de transformação, vai utilizar a [Linguagem de Consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Nesta passagem, define-se uma consulta que passa pelos dados sem realizar qualquer transformação.

1. **Selecione Consulta**.
2. Na janela de consulta, substitua-o `[YourOutputAlias]` pelo pseudónimo de saída que criou anteriormente.
3. Substitua-o `[YourInputAlias]` pelo pseudónimo de entrada que criou anteriormente. 
4. Selecione **Guardar** na barra de ferramentas. 

    ![Consulta](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Selecione **visão geral** no menu esquerdo. 
2. Selecione **Iniciar**. 

    ![Menu Iniciar](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na página iniciar a seleção de **trabalho,** selecione **Iniciar**. 

    ![Começar a página de trabalho](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Aguarde até que o estado do trabalho mude de **início** para **execução**. 

    ![Estado do trabalho - execução](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Teste o cenário
1. Gereno **produtor de Kafka** novamente para enviar eventos para o centro do evento. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Confirme que vê que **os dados** de saída são gerados no armazenamento **de blob Azure**. Você vê um ficheiro JSON no recipiente com 100 linhas que se parecem com as seguintes linhas de amostra: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    O trabalho da Azure Stream Analytics recebeu dados de entrada do centro do evento e guardou-os no armazenamento de blob Azure neste cenário. 



## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a transmitir para O Hubs de Eventos sem alterar os seus clientes protocolares ou executar os seus próprios clusters. Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [Criar um hub de eventos](event-hubs-create.md)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar kafka Connect com um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Ligue a Akka Streams a um centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub) 
