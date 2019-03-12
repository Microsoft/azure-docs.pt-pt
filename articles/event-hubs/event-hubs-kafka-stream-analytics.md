---
title: Processar eventos de Apache Kafka com o Stream analytics - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo mostra como processar eventos de Kafka, que são ingeridos através dos hubs de eventos com o Azure Stream Analytics
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: b6bba4ed45530ba66a1adde274022a80091cd199
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539162"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Processar a Apache Kafka para eventos dos Hubs de Eventos através do Stream Analytics 
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
* Uma **conta de armazenamento do Azure**. Se não tiver uma, [criá-lo](../storage/common/storage-quickstart-create-account.md) antes de prosseguir. A tarefa de Stream Analytics nestas instruções armazena os dados de saída num armazenamento de Blobs do Azure. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos ativado para Kafka

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e clique em **criar um recurso** na parte superior esquerda do ecrã.
2. Procure **os Hubs de eventos** e selecione as opções mostradas aqui:
    
    ![Procurar por Hubs de Eventos no portal](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Sobre o **os Hubs de eventos** página, selecione **criar**.
4. Sobre o **criar o espaço de nomes** página, efetue as seguintes ações: 
    1. Fornecer um exclusivo **nome** para o espaço de nomes. 
    2. Selecione um **escalão de preço**. 
    3. Selecione **ativar Kafka**. Este passo é um **importante** passo. 
    4. Selecione seu **subscrição** no qual pretende o espaço de nomes do hub de eventos a ser criada. 
    5. Criar uma nova **grupo de recursos** ou selecione um grupo de recursos existente. 
    6. Selecione um **localização**. 
    7. Clique em **Criar**.
    
        ![Criar um espaço de nomes](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. Na **mensagem de notificação**, selecione a **nome do grupo de recursos**. 

    ![Criar um espaço de nomes](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Selecione o **espaço de nomes de hub de eventos** no grupo de recursos. 
2. Depois de criar o espaço de nomes, selecione **políticas de acesso partilhado** sob **definições**.

    ![Clique em Políticas de acesso partilhado](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Pode escolher **RootManageSharedAccessKey** predefinida ou adicionar uma política nova. Clique no nome da política e a cópia a **cadeia de ligação**. Utilize a cadeia de ligação para configurar o cliente do Kafka. 
    
    ![Selecionar uma política](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Agora, já pode transmitir em fluxo eventos das suas aplicações que utilizam o protocolo Kafka para os Hubs de eventos.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Enviar mensagens com o Kafka nos Hubs de eventos

1. Clone o [os Hubs de eventos do Azure para o repositório de Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) para o seu computador.
2. Navegue para a pasta: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Atualizar os detalhes de configuração para o produtor no `src/main/resources/producer.config`. Especifique a **name** e **cadeia de ligação** para o **espaço de nomes de hub de eventos**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navegue para `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`e abra **TestDataReporter.java** ficheiro num editor à sua escolha. 
6. Comente a seguinte linha de código:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adicione a seguinte linha de código em vez do código comentado: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Este código envia os dados do evento **JSON** formato. Ao configurar a entrada para uma tarefa do Stream Analytics, especifique o JSON como formato para os dados de entrada. 
7. **Execute o produtor** e fluxo nos Hubs de eventos habilitados no Kafka. Num computador Windows, ao utilizar um **linha de comandos node. js**, mude para o `azure-event-hubs-for-kafka/quickstart/java/producer` pasta antes de executar estes comandos. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Certifique-se de que esse hub de eventos recebe os dados

1. Selecione **os Hubs de eventos** sob **entidades**. Confirme que vê um hub de eventos com o nome **testar**. 

    ![Hub de eventos - teste](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Confirme que vê mensagens recebidas para o hub de eventos. 

    ![Hub de eventos - as mensagens](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Processar dados de eventos através de uma tarefa do Stream Analytics
Nesta secção, vai criar uma tarefa do Azure Stream Analytics. O cliente do Kafka para enviar eventos para o hub de eventos. Criar uma tarefa de Stream Analytics que utiliza dados de eventos como entrada e os coloca num armazenamento de Blobs do Azure. Se não tiver uma **conta de armazenamento do Azure**, [criar um](../storage/common/storage-quickstart-create-account.md).

A consulta da tarefa do Stream Analytics passa através dos dados sem realizar qualquer análise. Pode criar uma consulta que transforma os dados de entrada para produzir os dados de saída num formato diferente ou com informações obtidas.  

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics 

1. Selecione **+ criar um recurso** no [portal do Azure](https://portal.azure.com).
2. Selecione **Analytics** no **Azure Marketplace** menu e selecione **tarefa do Stream Analytics**. 
3. Sobre o **novo Stream Analytics** página, efetue as seguintes ações: 
    1. Introduza um **nome** para a tarefa. 
    2. Selecione seu **subscrição**.
    3. Selecione **criar novo** para o **grupo de recursos** e introduza o nome. Também pode **utilizar um existente** grupo de recursos. 
    4. Selecione um **localização** para a tarefa.
    5. Selecione **criar** para criar a tarefa. 

        ![Nova tarefa do Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. Na mensagem de notificação, selecione **Ir para recurso** para ver a **tarefa do Stream Analytics** página. 
2. Selecione **entradas** no **TOPOLOGIA da tarefa** secção no menu da esquerda.
3. Selecione **Adicionar entrada de fluxo**e, em seguida, selecione **Hub de eventos**. 

    ![Adicionar hub de eventos como entrada](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Sobre o **entrada do Hub de eventos** configuração página, efetue as seguintes ações: 

    1. Especifique um **alias** para a entrada. 
    2. Selecione a sua **subscrição do Azure**.
    3. Selecione o **espaço de nomes de hub de eventos** sua criado anteriormente. 
    4. Selecione **testar** para o **hub de eventos**. 
    5. Selecione **Guardar**. 

        ![Configuração de entrada do hub de eventos](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurar a saída da tarefa 

1. Selecione **saídas** no **TOPOLOGIA da tarefa** secção no menu. 
2. Selecione **+ adicionar** na barra de ferramentas e selecione **armazenamento de BLOBs**
3. Na página de definições de saída do armazenamento de BLOBs, efetue as seguintes ações: 
    1. Especifique um **alias** para a saída. 
    2. Selecione a sua **subscrição** do Azure. 
    3. Selecione seu **conta de armazenamento do Azure**. 
    4. Introduza um **nome do contentor** que armazena os dados de saída da consulta do Stream Analytics.
    5. Selecione **Guardar**.

        ![Configuração de saída de armazenamento de BLOBs](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definir uma consulta
Depois de ter uma tarefa do Stream Analytics configurada para ler um fluxo de dados de entrada, o passo seguinte consiste em criar uma transformação que analisa os dados em tempo real. Para definir a consulta de transformação, vai utilizar a [Linguagem de Consulta do Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Este passo a passo, vai definir uma consulta que passa os dados sem executar qualquer transformação.

1. Selecione **consulta**.
2. Na janela da consulta, substitua `[YourOutputAlias]` com o alias de saída que criou anteriormente.
3. Substitua `[YourInputAlias]` com o alias de entrada que criou anteriormente. 
4. Selecione **Guardar** na barra de ferramentas. 

    ![Consulta](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Selecione **descrição geral** no menu da esquerda. 
2. Selecione **iniciar**. 

    ![Menu Iniciar](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Sobre o **tarefa de início** página, selecione **iniciar**. 

    ![Página de tarefa de início](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Aguarde até que o estado da tarefa é alterado de **inicial** ao **em execução**. 

    ![Estado da tarefa - em execução](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>O cenário de teste
1. Executar o **produtor Kafka** novamente para enviar eventos para o hub de eventos. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Confirme que vê **dados de saída** é gerada na **armazenamento de Blobs do Azure**. Ver um ficheiro JSON no contentor com 100 linhas que se pareçam com as seguintes linhas de exemplo: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Tarefa do Azure Stream Analytics recebidos dados de entrada do hub de eventos e guardá-la no armazenamento de Blobs do Azure neste cenário. 



## <a name="next-steps"></a>Passos Seguintes
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
