---
title: Azure Event Hubs - Processa eventos Apache Kafka
description: 'Tutorial: Este artigo mostra como processar eventos kafka que são ingeridos através de centros de eventos usando Azure Stream Analytics'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 328537eebd05391fc4c8138395a9c10f0a5d072f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92319381"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Tutorial: Processa Apache Kafka para eventos de Centros de Eventos usando análise de stream 
Este artigo mostra como transmitir dados para os Centros de Eventos e processá-lo com a Azure Stream Analytics. Acompanha-o pelos seguintes passos: 

1. Crie um espaço de nomes dos Hubs de Eventos.
2. Crie um cliente Kafka que envie mensagens para o centro de eventos.
3. Crie um trabalho stream Analytics que copie dados do centro do evento para um armazenamento de bolhas Azure. 

Não precisa de alterar os seus clientes de protocolo ou executar os seus próprios clusters quando utilizar o ponto final kafka exposto por um centro de eventos. Os Hubs de Eventos do Azure suportam o [Apache Kafka versão 1.0](https://kafka.apache.org/10/documentation.html) e acima. 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem os seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven.
* [Git](https://www.git-scm.com/)
* Uma **conta de Armazenamento Azure.** Se não tiver um, [crie um](../storage/common/storage-account-create.md) antes de prosseguir. O trabalho stream Analytics neste walkthrough armazena os dados de saída num armazenamento de bolhas Azure. 


## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
Quando cria um espaço de nome de nível **padrão,** o ponto final de Kafka para o espaço de nome é automaticamente ativado. Você pode transmitir eventos a partir das suas aplicações que usam o protocolo Kafka em centros de eventos de nível padrão. Siga as instruções passo a passo no centro de eventos Criar um centro de eventos utilizando o [portal Azure](event-hubs-create.md) para criar um espaço de nomes **padrão** de centros de eventos de nível padrão. 

> [!NOTE]
> Os Centros de Eventos para Kafka estão disponíveis apenas em níveis **standard** e **dedicados.** O nível **básico** não suporta Kafka em Centros de Eventos.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Enviar mensagens com Kafka em Centros de Eventos

1. Clone os [Hubs de Eventos Azure para o repositório de Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) para a sua máquina.
2. Navegue para a pasta: `azure-event-hubs-for-kafka/quickstart/java/producer` . 
4. Atualize os detalhes de configuração para o produtor em `src/main/resources/producer.config` . Especifique o **nome** e **a cadeia de conexão** para o **espaço de nome do centro de eventos**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navegue para `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/` , e abra o ficheiro **TestDataReporter.java** num editor à sua escolha. 
6. Comentar a seguinte linha de código:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adicione a seguinte linha de código no lugar do código comentado: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Este código envia os dados do evento no formato **JSON.** Quando configurar a entrada para um trabalho stream Analytics, especifica o JSON como o formato para os dados de entrada. 
7. **Executar o produtor** e transmitir para o Event Hubs. Numa máquina Do Windows, quando utilizar um **pedido de comandoNode.js,** mude para a `azure-event-hubs-for-kafka/quickstart/java/producer` pasta antes de executar estes comandos. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Verifique se o centro de eventos recebe os dados

1. Selecione **Centros de Eventos** em **ENTIDADES**. Confirme que vê um centro de **eventos** chamado test . 

    ![Centro de eventos - teste](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Confirme que vê mensagens a chegar ao centro de eventos. 

    ![Centro de eventos - mensagens](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Processar dados de eventos usando um trabalho stream Analytics
Nesta secção, cria-se um trabalho Azure Stream Analytics. O cliente Kafka envia eventos para o centro de eventos. Você cria um trabalho stream Analytics que leva os dados do evento como entrada e o output para um armazenamento de bolhas Azure. Se não tiver uma **conta de Armazenamento Azure,** crie [uma](../storage/common/storage-account-create.md).

A consulta no trabalho stream Analytics passa através dos dados sem realizar qualquer análise. Pode criar uma consulta que transforme os dados de entrada para produzir dados de saída num formato diferente ou com insights obtidos.  

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics 

1. Selecione **+ Criar um recurso** no portal [Azure.](https://portal.azure.com)
2. Selecione **Analytics** no menu **Azure Marketplace** e selecione **o trabalho stream Analytics**. 
3. Na página **New Stream Analytics,** faça as seguintes ações: 
    1. Insira um **nome** para o trabalho. 
    2. Selecione a **subscrição**.
    3. Selecione **Criar novo** para o **grupo de recursos** e insira o nome. Também pode utilizar um grupo de recursos **existente.** 
    4. Selecione um **local** para o trabalho.
    5. Selecione **Criar** para criar o trabalho. 

        ![Novo trabalho de Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. Na mensagem de notificação, selecione **Ir ao recurso** para ver a página de trabalho stream **Analytics.** 
2. Selecione **Entradas** na secção **JOB TOPOLOGY** no menu esquerdo.
3. **Selecione Adicionar entrada de fluxo** e, em seguida, selecione Event **Hub**. 

    ![Adicione o centro de eventos como uma entrada](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na página de configuração de entrada do **Event Hub,** faça as seguintes ações: 

    1. Especifique **um pseudónimo** para a entrada. 
    2. Selecione a sua **subscrição Azure**.
    3. Selecione o **espaço de nome do centro de eventos** criado anteriormente. 
    4. Selecione **teste** para o centro de **eventos**. 
    5. Selecione **Guardar**. 

        ![Configuração de entrada de hub de evento](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurar a saída da tarefa 

1. Selecione **Saídas** na secção **JOB TOPOLOGY** no menu. 
2. **Selecione + Adicione** na barra de ferramentas e selecione o armazenamento **blob**
3. Na página de definições de saída de armazenamento Blob, faça as seguintes ações: 
    1. Especifique **um pseudónimo** para a saída. 
    2. Selecione a sua **subscrição Azure**. 
    3. Selecione a sua **conta de Armazenamento Azure**. 
    4. Introduza um **nome para o recipiente** que armazena os dados de saída da consulta Stream Analytics.
    5. Selecione **Guardar**.

        ![Configuração de saída de armazenamento de blob](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definir uma consulta
Depois de ter uma tarefa do Stream Analytics configurada para ler um fluxo de dados de entrada, o passo seguinte consiste em criar uma transformação que analisa os dados em tempo real. Para definir a consulta de transformação, vai utilizar a [Linguagem de Consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). Nesta passagem, define-se uma consulta que passa pelos dados sem realizar qualquer transformação.

1. Selecione **Consulta**.
2. Na janela de consulta, `[YourOutputAlias]` substitua-a pelo pseudónimo de saída que criou anteriormente.
3. `[YourInputAlias]`Substitua-o pelo pseudónimo de entrada que criou anteriormente. 
4. Selecione **Guardar** na barra de ferramentas. 

    ![A captura do ecrã mostra a janela de consulta com valores para variáveis de entrada e saída.](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Selecione **visão geral** no menu esquerdo. 
2. Selecione **Iniciar**. 

    ![Menu Iniciar](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na página **iniciar o trabalho,** selecione **Iniciar**. 

    ![Iniciar página de trabalho](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Aguarde até que o estado do trabalho mude de **início** para **o funcionamento**. 

    ![Estatuto de trabalho - execução](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Teste o cenário
1. Volte a dirigir o **produtor Kafka** para enviar eventos para o centro de eventos. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Confirme que vê que os **dados de saída** são gerados no armazenamento de **bolhas Azure**. Você vê um ficheiro JSON no recipiente com 100 linhas que se parecem com as seguintes linhas de amostra: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    O trabalho do Azure Stream Analytics recebeu dados de entrada do centro de eventos e armazenou-os no armazenamento de blob Azure neste cenário. 



## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a transmitir para os Centros de Eventos sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. Para saber mais sobre os Centros de Eventos da Apache Kafka, consulte o [guia de desenvolvimento apache Kafka para o Azure Event Hubs.](apache-kafka-developer-guide.md)