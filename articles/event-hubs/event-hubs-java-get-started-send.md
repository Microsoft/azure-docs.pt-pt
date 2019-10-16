---
title: Usar o Java para enviar dados de e para os hubs de eventos do Azure
description: Este artigo fornece uma explicação da criação de um aplicativo Java que envia eventos para os hubs de eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18, seo-java-august2019, seo-java-september2019
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: be9919950f24dbee7fb8a3f901767c298105bf53
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325460"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs"></a>Usar o Java para enviar eventos ou receber eventos dos hubs de eventos do Azure

Este tutorial mostra como criar aplicativos Java para enviar eventos ou receber eventos de hubs de eventos do Azure.

Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuídos. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análises em tempo real ou adaptadores de armazenamento/criação de batches. Para obter uma visão geral detalhada dos hubs de eventos, consulte Visão geral dos hubs de eventos e recursos dos hubs de eventos.

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Um ambiente de desenvolvimento Java. Este tutorial usa o [Eclipse](https://www.eclipse.org/).
- **Crie um namespace de hubs de eventos e um hub de eventos**. O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento neste [artigo](event-hubs-create.md). Em seguida, obtenha o valor da chave de acesso para o Hub de eventos seguindo as instruções do artigo: [obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a chave de acesso no código que escreve posteriormente neste tutorial. O nome da chave padrão é: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Enviar eventos 
Esta seção mostra como criar um aplicativo Java para enviar eventos para um hub de eventos. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Adicionar referência à biblioteca de hubs de eventos do Azure

A biblioteca de cliente Java para os hubs de eventos está disponível para uso em projetos Maven do [repositório central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Você pode fazer referência a essa biblioteca usando a seguinte declaração de dependência dentro de seu arquivo de projeto Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Para diferentes tipos de ambientes de compilação, você pode obter explicitamente os arquivos JAR liberados mais recentemente do [repositório central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Para um editor de eventos simples, importe o pacote *com. Microsoft. Azure. Eventhubs* para as classes de cliente dos hubs de eventos e o pacote *com. Microsoft. Azure. ServiceBus* para classes de utilitário, como exceções comuns que são compartilhadas com o Azure Cliente de mensagens do barramento de serviço. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. Adicione uma classe chamada `SimpleSend` e adicione o seguinte código à classe:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Construir cadeia de conexão

Use a classe ConnectionStringBuilder para construir um valor de cadeia de conexão para passar para a instância de cliente dos hubs de eventos. Substitua os espaços reservados pelos valores obtidos quando você criou o namespace e o Hub de eventos:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Escrever código para enviar eventos

Crie um evento singular transformando uma cadeia de caracteres em sua codificação de bytes UTF-8. Em seguida, crie uma nova instância de cliente dos hubs de eventos a partir da cadeia de conexão e envie a mensagem:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Compile e execute o programa e verifique se não há erros.

Parabéns! Enviou agora mensagens para um hub de eventos.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Apêndice: como as mensagens são roteadas para as partições do EventHub

Antes que as mensagens sejam recuperadas pelos consumidores, elas precisam ser publicadas primeiro nas partições pelos Publicadores. Quando as mensagens são publicadas no Hub de eventos de forma síncrona usando o método sendSync () no objeto com. Microsoft. Azure. Eventhubs. EventHubClient, a mensagem pode ser enviada para uma partição específica ou distribuída para todas as partições disponíveis de maneira Round Robin dependendo se a chave de partição é especificada ou não.

Quando uma cadeia de caracteres que representa a chave de partição for especificada, a chave será configurada em hash para determinar a qual partição enviar o evento.

Quando a chave de partição não for definida, as mensagens passarão por rodízio para todas as partições disponíveis

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Receber eventos
O código neste tutorial se baseia no [código EventProcessorSample no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), que você pode examinar para ver o aplicativo de trabalho completo.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Receber mensagens com o EventProcessorHost em Java

**EventProcessorHost** é uma classe Java que simplifica o recebimento de eventos de hubs de eventos, gerenciando pontos de verificação persistentes e recebimentos paralelos desses hubs de eventos. Usando o EventProcessorHost, você pode dividir eventos entre vários destinatários, mesmo quando hospedados em nós diferentes. Este exemplo mostra como utilizar o EventProcessorHost para um recetor único.

### <a name="create-a-storage-account"></a>Create a storage account

Para usar o EventProcessorHost, você deve ter uma [conta de armazenamento do Azure] [conta de armazenamento do Azure]:

1. Conecte-se à [portal do Azure](https://portal.azure.com)e selecione **criar um recurso** no lado esquerdo da tela.
2. Selecione **armazenamento**e, em seguida, selecione **conta de armazenamento**. Na janela **criar conta de armazenamento** , digite um nome para a conta de armazenamento. Preencha o restante dos campos, selecione a região desejada e, em seguida, selecione **criar**.
   
    ![Criar uma conta de armazenamento no portal do Azure](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Selecione a conta de armazenamento recém-criada e, em seguida, selecione **chaves de acesso**:
   
    ![Obter suas chaves de acesso no portal do Azure](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Copie o valor de key1 para um local temporário. Vai utilizá-lo mais tarde no tutorial.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Criar um projeto Java com o EventProcessorHost

A biblioteca de cliente Java para os hubs de eventos está disponível para uso em projetos Maven do [repositório central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)e pode ser referenciada usando a seguinte declaração de dependência dentro de seu arquivo de projeto Maven: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Para diferentes tipos de ambientes de compilação, você pode obter explicitamente os arquivos JAR liberados mais recentemente do [repositório central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. A classe é chamada `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Utilize o seguinte código para criar uma nova classe designada `EventProcessorSample`. Substitua os espaços reservados pelos valores usados quando você criou o Hub de eventos e a conta de armazenamento:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Crie mais uma classe chamada `EventProcessor`, usando o seguinte código:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Este tutorial utiliza uma única instância do EventProcessorHost. Para aumentar a taxa de transferência, recomendamos que você execute várias instâncias do EventProcessorHost, preferencialmente em computadores separados.  Ele também fornece redundância. Nesses casos, as várias instâncias coordenam-se automaticamente entre si para equilibrarem a carga dos eventos recebidos. Se pretender várias recetores para que cada um processe *todos* os eventos, terá de utilizar o conceito **ConsumerGroup**. Se receber eventos de vários computadores, poderá ser útil especificar os nomes das instâncias do EventProcessorHost com base nos computadores (ou funções) em que estão implementadas.

### <a name="publishing-messages-to-eventhub"></a>Publicando mensagens no EventHub

Antes que as mensagens sejam recuperadas pelos consumidores, elas precisam ser publicadas primeiro nas partições pelos Publicadores. Vale a pena observar que quando as mensagens são publicadas no Hub de eventos de forma síncrona usando o método sendSync () no objeto com. Microsoft. Azure. Eventhubs. EventHubClient, a mensagem pode ser enviada para uma partição específica ou distribuída a todas as partições disponíveis de forma Round Robin, dependendo se a chave de partição é especificada ou não.

Quando uma cadeia de caracteres que representa a chave de partição é especificada, a chave é codificada em hash para determinar para qual partição o evento será enviado.

Quando a chave de partição não é definida, as mensagens são Round Robin para todas as partições disponíveis

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementando um Checkpointmanager personalizado para EventProcessorHost (EPH)

A API fornece um mecanismo para implementar o Gerenciador de ponto de verificação personalizado para cenários em que a implementação padrão não é compatível com seu caso de uso.

O Gerenciador de ponto de verificação padrão usa o armazenamento de BLOBs, mas se você substituir o Gerenciador de pontos de verificação usado pelo EPH com sua própria implementação, você poderá usar qualquer repositório que desejar de volta à sua implementação do Gerenciador de pontos de verificação.

Crie uma classe que implemente a interface com. Microsoft. Azure. eventprocessorhost. ICheckpointManager

Use sua implementação personalizada do Gerenciador de pontos de verificação (com. Microsoft. Azure. eventprocessorhost. ICheckpointManager)

Em sua implementação, você pode substituir o mecanismo de ponto de verificação padrão e implementar nossos próprios pontos de verificação com base em seu próprio armazenamento de dados (como SQL Server, CosmosDB e cache do Azure para Redis). É recomendável que o repositório usado para fazer sua implementação do Gerenciador de pontos de verificação seja acessível a todas as instâncias de EPH que estão processando eventos para o grupo de consumidores.

Você pode usar qualquer repositório de armazenamento que esteja disponível em seu ambiente.

A classe com. Microsoft. Azure. eventprocessorhost. EventProcessorHost fornece dois construtores que permitem que você substitua o Gerenciador de ponto de verificação para seu EventProcessorHost.


## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)

