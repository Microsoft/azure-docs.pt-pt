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
ms.openlocfilehash: 8047ac5d3db2db230a00583e888a4afea3b282b9
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969378"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Tutorial: Processa Apache Kafka para eventos de Event Hubs usando análise de stream 
Este artigo mostra como transmitir dados para os Hubs de eventos habilitados no Kafka e processá-lo com o Azure Stream Analytics. Ele explica os passos seguintes: 

1. Criar um Kafka ativado o espaço de nomes de Hubs de eventos.
2. Crie um cliente de Kafka que envia mensagens para o hub de eventos.
3. Crie uma tarefa de Stream Analytics que copia dados do hub de eventos para um armazenamento de Blobs do Azure. 

Não é necessário alterar seus clientes de protocolo ou execute seus próprios clusters ao utilizar o ponto de extremidade do Kafka exposto por um hub de eventos. Os Hubs de Eventos do Azure suportam o [Apache Kafka versão 1.0](https://kafka.apache.org/10/documentation.html) e acima. 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem os seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven.
* [Git](https://www.git-scm.com/)
* Uma **conta de Armazenamento Azure.** Se não tem um, [crie um](../storage/common/storage-account-create.md) antes de prosseguir. A tarefa de Stream Analytics nestas instruções armazena os dados de saída num armazenamento de Blobs do Azure. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos ativado para Kafka
Quando cria **um** espaço de nome padrão do Event Hubs, o ponto final de Kafka para o espaço de nome está ativado automaticamente. Você pode transmitir eventos a partir das suas aplicações que usam o protocolo Kafka em centros de eventos de nível padrão. Siga instruções passo a passo no [Create a event hub utilizando o portal Azure](event-hubs-create.md) para criar um espaço de nome de hubs de evento de nível **padrão.** 

> [!NOTE]
> O Event Hubs para Kafka está disponível apenas em camadas **standard** e **dedicada.** O nível **básico** não suporta Kafka em Centros de Eventos.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Enviar mensagens com o Kafka nos Hubs de eventos

1. Clone os Hubs de [Eventos Azure para o repositório Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) à sua máquina.
2. Navegue para a pasta: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Atualize os detalhes de configuração para o produtor em `src/main/resources/producer.config`. Especifique o **nome** e a cadeia de **ligação** para o espaço de nome do centro de **eventos**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navegue para `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`, e abra o ficheiro **TestDataReporter.java** num editor à sua escolha. 
6. Comente a seguinte linha de código:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adicione a seguinte linha de código em vez do código comentado: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Este código envia os dados do evento em formato **JSON.** Ao configurar a entrada para uma tarefa do Stream Analytics, especifique o JSON como formato para os dados de entrada. 
7. **Execute o produtor** e flua para Centros de Eventos ativados por Kafka. Numa máquina Windows, quando utilizar um pedido de **comando Node.js,** mude para a pasta `azure-event-hubs-for-kafka/quickstart/java/producer` antes de executar estes comandos. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Certifique-se de que esse hub de eventos recebe os dados

1. Selecione **Centros de Eventos** em **ENTIDADES**. Confirme que vê um centro de eventos chamado **teste.** 

    ![Hub de eventos - teste](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Confirme que vê mensagens recebidas para o hub de eventos. 

    ![Hub de eventos - as mensagens](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Processar dados de eventos através de uma tarefa do Stream Analytics
Nesta secção, vai criar uma tarefa do Azure Stream Analytics. O cliente do Kafka para enviar eventos para o hub de eventos. Criar uma tarefa de Stream Analytics que utiliza dados de eventos como entrada e os coloca num armazenamento de Blobs do Azure. Se não tiver uma **conta de Armazenamento Azure, crie** [uma](../storage/common/storage-account-create.md).

A consulta da tarefa do Stream Analytics passa através dos dados sem realizar qualquer análise. Pode criar uma consulta que transforma os dados de entrada para produzir os dados de saída num formato diferente ou com informações obtidas.  

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics 

1. Selecione **+ Crie um recurso** no portal [Azure](https://portal.azure.com).
2. Selecione **Analytics** no menu **Azure Marketplace** e selecione **o trabalho do Stream Analytics.** 
3. Na página **New Stream Analytics,** faça as seguintes ações: 
    1. Insira um **nome** para o trabalho. 
    2. Selecione a sua **subscrição**.
    3. Selecione **Criar novos** para o **grupo de recursos** e insira o nome. Também pode utilizar um grupo de recursos **existente.** 
    4. Selecione um **local** para o trabalho.
    5. Selecione **Criar** para criar o trabalho. 

        ![Nova tarefa do Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. Na mensagem de notificação, selecione **Ir a recurso** para ver a página de trabalho do Stream **Analytics.** 
2. Selecione **Inputs** na secção **JOB TOPOLOGY** no menu esquerdo.
3. **Selecione Adicionar entrada**de fluxo , e, em seguida, selecione Event **Hub**. 

    ![Adicionar hub de eventos como entrada](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na página de configuração de **entrada do Event Hub,** faça as seguintes ações: 

    1. Especifique um **pseudónimo** para a entrada. 
    2. Selecione a sua **subscrição** do Azure.
    3. Selecione o espaço de nome do centro de **eventos** que criou anteriormente. 
    4. Selecione **o teste** para o centro de **eventos**. 
    5. Selecione **Guardar**. 

        ![Configuração de entrada do hub de eventos](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurar a saída da tarefa 

1. Selecione **Saídas** na secção **JOB TOPOLOGY** no menu. 
2. Selecione **+ Adicione** na barra de ferramentas e selecione **armazenamento Blob**
3. Na página de definições de saída do armazenamento de BLOBs, efetue as seguintes ações: 
    1. Especifique um **pseudónimo** para a saída. 
    2. Selecione a sua **subscrição** do Azure. 
    3. Selecione a sua **conta de Armazenamento Azure**. 
    4. Introduza um **nome para o recipiente** que armazena os dados de saída da consulta Stream Analytics.
    5. Selecione **Guardar**.

        ![Configuração de saída de armazenamento de BLOBs](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definir uma consulta
Depois de ter uma tarefa do Stream Analytics configurada para ler um fluxo de dados de entrada, o passo seguinte consiste em criar uma transformação que analisa os dados em tempo real. Para definir a consulta de transformação, vai utilizar a [Linguagem de Consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Este passo a passo, vai definir uma consulta que passa os dados sem executar qualquer transformação.

1. **Selecione Consulta**.
2. Na janela de consulta, substitua `[YourOutputAlias]` pelo pseudónimo de saída que criou anteriormente.
3. Substitua `[YourInputAlias]` pelo pseudónimo de entrada que criou anteriormente. 
4. Selecione **Guardar** na barra de ferramentas. 

    ![Consulta](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Selecione **visão geral** no menu esquerdo. 
2. Selecione **Iniciar**. 

    ![Menu Iniciar](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na página iniciar a seleção de **trabalho,** selecione **Iniciar**. 

    ![Página de tarefa de início](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Aguarde até que o estado do trabalho mude de **início** para **execução**. 

    ![Estado da tarefa - em execução](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>O cenário de teste
1. Gereno **produtor de Kafka** novamente para enviar eventos para o centro do evento. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Confirme que vê que **os dados** de saída são gerados no armazenamento **de blob Azure**. Ver um ficheiro JSON no contentor com 100 linhas que se pareçam com as seguintes linhas de exemplo: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Tarefa do Azure Stream Analytics recebidos dados de entrada do hub de eventos e guardá-la no armazenamento de Blobs do Azure neste cenário. 



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
