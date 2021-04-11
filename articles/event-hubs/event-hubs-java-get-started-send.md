---
title: Enviar ou receber eventos de Azure Event Hubs usando Java (mais recente)
description: Este artigo fornece uma caminhada para criar uma aplicação Java que envia/recebe eventos de/de Azure Event Hubs usando o mais recente pacote de eventos de mensagens azure..
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448445"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Use Java para enviar eventos ou receber eventos de Azure Event Hubs (azure-messaging-eventhubs)
Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando o pacote java **de mensagens azure-eventhubs.**

> [!IMPORTANT]
> Este quickstart usa o novo pacote **azure-messaging-eventhubs.** Para um arranque rápido que utiliza os **antigos pacotes azure-eventhubs-eph,** consulte [Enviar e receber eventos usando azure-eventhubs e azure-eventhubs-eph](event-hubs-java-get-started-send-legacy.md).  


## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte o [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- Um ambiente de desenvolvimento de Java. Este quickstart usa [Eclipse.](https://www.eclipse.org/) É necessário o Kit de Desenvolvimento de Java (JDK) com a versão 8 ou superior. 
- **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de ligação para o espaço de nomes Do Event Hubs** seguindo as instruções do artigo: Obter a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a cadeia de ligação mais tarde neste arranque rápido.

## <a name="send-events"></a>Enviar eventos 
Esta secção mostra como criar uma aplicação Java para enviar eventos um centro de eventos. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Adicionar referência à biblioteca Azure Event Hubs

A biblioteca de clientes Java para Centros de Eventos está disponível no [Repositório Central maven.](https://search.maven.org/search?q=a:azure-messaging-eventhubs) Pode fazer referência a esta biblioteca utilizando a seguinte declaração de dependência dentro do seu ficheiro de projeto Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> Atualize a versão para a versão mais recente publicada no repositório Maven. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. Adicione uma classe chamada `Sender` , e adicione o seguinte código à classe:

> [!IMPORTANT]
> Atualize `<Event Hubs namespace connection string>` com o fio de ligação ao seu espaço de nomes Desempaco. Atualize `<Event hub name>` com o nome do seu centro de eventos no espaço de nomes. 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>Adicione código para publicar eventos no centro de eventos
Adicione um método nomeado `publishEvents` à classe como mostrado `Sender` abaixo. 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

Construa o programa e certifique-se de que não há erros. Executará este programa depois de executar o programa recetor. 

## <a name="receive-events"></a>Receber eventos
O código deste tutorial baseia-se na [amostra EventProcessorClient no GitHub,](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java)que pode examinar para ver a aplicação completa de trabalho.

> [!WARNING]
> Se executar este código no Azure Stack Hub, sofrerá erros de tempo de execução a menos que tenha como alvo uma versão API de armazenamento específica. Isto porque o Event Hubs SDK utiliza a mais recente API de Armazenamento Azure disponível disponível no Azure que pode não estar disponível na sua plataforma Azure Stack Hub. O Azure Stack Hub pode suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Se estiver a utilizar o Azure Blog Storage como uma loja de checkpoint, verifique a [versão API suportada do Azure Storage para a sua construção do Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) e direcione essa versão no seu código. 
>
> Por exemplo, Se estiver a executar a versão Azure Stack Hub 2005, a versão mais alta disponível para o serviço de armazenamento é a versão 2019-02-02. Por padrão, a biblioteca de clientes Event Hubs SDK utiliza a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento API versão 2019-02-02. Para um exemplo sobre como direcionar uma versão específica da API de armazenamento, consulte [esta amostra no GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um armazenamento Azure e um recipiente blob
Neste arranque rápido, utilize o Azure Storage (especificamente, Blob Storage) como loja de pontos de verificação. O checkpointing é um processo pelo qual um processador de eventos marca ou compromete a posição do último evento processado com sucesso dentro de uma partição. A marcação de um ponto de verificação é normalmente feita dentro da função que processa os eventos. Para saber mais sobre o checkpoint, consulte [o processador Event.](event-processor-balance-partition-load.md)

Siga estes passos para criar uma conta de Armazenamento Azure. 

1. [Criar uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contentor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha o fio de ligação para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

    Observe o **fio de ligação** e o nome do **recipiente**. Vai usá-los no código de receção. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Adicione bibliotecas de Centros de Eventos ao seu projeto Java
Adicione as seguintes dependências no ficheiro pom.xml. 

- [azure-mensagens-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. Adicione as **seguintes** declarações de importação no topo do ficheiro Java. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. Crie uma classe chamada `Receiver` , e adicione as seguintes variáveis de corda à classe. Substitua os espaços reservados pelos valores corretos. 

    > [!IMPORTANT]
    > Substitua os espaços reservados pelos valores corretos.
    > - `<Event Hubs namespace connection string>` com a cadeia de ligação ao seu espaço de nomes Event Hubs. Atualizar 
    > - `<Event hub name>` com o nome do seu centro de eventos no espaço de nomes. 
    > - `<Storage connection string>` com o fio de ligação à sua conta de armazenamento Azure. 
    > - `<Storage container name>` com o nome do seu recipiente no seu armazenamento de bolhas Azure. 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. Adicione o seguinte `main` método à classe. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Adicione os dois métodos de ajuda `PARTITION_PROCESSOR` `ERROR_HANDLER` (e) que processam eventos e erros à `Receiver` classe. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Construa o programa e certifique-se de que não há erros. 

## <a name="run-the-applications"></a>Executar as aplicações
1. Executar primeiro a aplicação do **recetor.**
1. Em seguida, executar o pedido **de remetente.** 
1. Na janela de aplicação do **recetor,** confirme que vê os eventos que foram publicados pela aplicação remetente.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. Prima **ENTER** na janela de aplicação do recetor para parar a aplicação. 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>Passos seguintes
Veja as seguintes amostras no GitHub:

- [amostras azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [amostras azure-messaging-eventhubs-checkpointstore-blob](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
