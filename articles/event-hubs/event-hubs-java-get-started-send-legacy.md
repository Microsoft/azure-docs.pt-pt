---
title: Enviar ou receber eventos de Azure Event Hubs usando Java (legado)
description: Este artigo fornece uma caminhada para criar uma aplicação Java que envia/recebe eventos de/de Azure Event Hubs usando o antigo pacote azure-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: b9bbd5d4290261ec96755f02a6179a7cde267d82
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309412"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Use Java para enviar eventos ou receber eventos de Azure Event Hubs (azure-eventhubs)

Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando o pacote **Azure-eventhubs** Java.

> [!WARNING]
> Este quickstart usa os **antigos azure-eventhubs** e **pacotes azure-eventhubs-eph.** Para um arranque rápido que utilize o mais recente pacote  **de eventos de mensagens azure,consulte** [Enviar e receber eventos usando azure-messaging-eventhubs](event-hubs-java-get-started-send.md). Para mover a sua aplicação de usar o pacote antigo para um novo, consulte o [Guia para migrar de azure-eventhubs para azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 

## <a name="prerequisites"></a>Pré-requisitos

Se você é novo em Azure Event Hubs, consulte o [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- Um ambiente de desenvolvimento de Java. Este quickstart usa [Eclipse.](https://www.eclipse.org/)
- **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). Em seguida, obtenha o valor da chave de acesso para o centro do evento seguindo as instruções do artigo: Obtenha a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a chave de acesso no código que escreve mais tarde neste arranque rápido. O nome-chave predefinido é: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Enviar eventos 
Esta secção mostra como criar uma aplicação Java para enviar eventos um centro de eventos. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste arranque rápido para criar o seu próprio.

### <a name="add-reference-to-azure-event-hubs-library"></a>Adicionar referência à biblioteca Azure Event Hubs

A biblioteca de clientes Java para Centros de Eventos está disponível para uso em projetos Maven do [Repositório Central maven.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) Pode fazer referência a esta biblioteca utilizando a seguinte declaração de dependência dentro do seu ficheiro de projeto Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Para diferentes tipos de ambientes de construção, pode obter explicitamente os mais recentes ficheiros JAR lançados a partir do [Repositório Central de Maven.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)  

Para uma simples editora de eventos, importe o pacote *com.microsoft.azure.eventhubs* para as classes de clientes do Event Hubs e o pacote *com.microsoft.azure.servicebus* para classes de utilidades, tais como exceções comuns que são partilhadas com o cliente de mensagens Azure Service Bus. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. Adicione uma classe chamada `SimpleSend` , e adicione o seguinte código à classe:

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

Utilize a classe ConnectionStringBuilder para construir um valor de cadeia de ligação para passar para a instância do cliente do Event Hubs. Substitua os espaços reservados pelos valores obtidos quando criou o espaço de nome e o centro de eventos:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Escrever código para enviar eventos

Crie um evento singular transformando uma corda na sua codificação byte UTF-8. Em seguida, crie uma nova instância de cliente do Event Hubs a partir da cadeia de ligação e envie a mensagem:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
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

Construa e execute o programa, e certifique-se de que não há erros.

Parabéns! Enviou agora mensagens para um hub de eventos.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Apêndice: Como as mensagens são encaminhada para as divisórias EventHub

Antes de as mensagens serem recuperadas pelos consumidores, têm de ser publicadas nas divisórias primeiro pelos editores. Quando as mensagens são publicadas para o centro de eventos de forma sincronizada utilizando o método sendSync() no com.microsoft.azure.eventhubs.EventHubClient, a mensagem pode ser enviada para uma partição específica ou distribuída a todas as divisórias disponíveis de forma redonda, dependendo se a chave de partição é especificada ou não.

Quando uma corda que representa a chave de partição for especificada, a chave será hashed para determinar para que partição enviar o evento.

Quando a chave de partição não estiver definida, as mensagens serão arredondadas para todas as divisórias disponíveis

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
O código deste tutorial baseia-se no [código EventProcessorSample no GitHub,](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)que pode examinar para ver a aplicação completa de trabalho.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Receber mensagens com o EventProcessorHost em Java

**EventProcessorHost** é uma classe Java que simplifica a receção de eventos dos Centros de Eventos, gerindo pontos de verificação persistentes e receções paralelas desses Centros de Eventos. Utilizando o EventProcessorHost, pode dividir eventos em vários recetores, mesmo quando hospedados em diferentes nós. Este exemplo mostra como utilizar o EventProcessorHost para um recetor único.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para utilizar o EventProcessorHost, tem de ter uma conta [Azure Storage][Conta de Armazenamento Azure]:

1. Inscreva-se no [portal Azure](https://portal.azure.com)e selecione **Criar um recurso** no lado esquerdo do ecrã.
2. Selecione **Armazenamento** e, em seguida, **Conta de armazenamento**. Na janela da **conta de armazenamento Criar,** escreva um nome para a conta de armazenamento. Complete o resto dos campos, selecione a região desejada e, em seguida, **selecione Criar**.

    ![Criar uma conta de armazenamento no portal Azure](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Selecione a conta de armazenamento recém-criada e, em seguida, selecione **As Teclas de acesso**:

    ![Obtenha as suas chaves de acesso no portal Azure](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Copie o valor da chave1 para um local temporário. Vai utilizá-lo mais tarde no tutorial.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Criar um projeto Java com o EventProcessorHost

A biblioteca de clientes Java para Centros de Eventos está disponível para uso em projetos Maven a partir do [Repositório Central de Maven,](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)e pode ser referenciada usando a seguinte declaração de dependência dentro do seu arquivo de projeto Maven: 

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

Para diferentes tipos de ambientes de construção, pode obter explicitamente os mais recentes ficheiros JAR lançados a partir do [Repositório Central de Maven.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)

1. Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. A aula `ErrorNotificationHandler` chama-se.     

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
2. Utilize o seguinte código para criar uma nova classe designada `EventProcessorSample`. Substitua os espaços reservados pelos valores utilizados quando criou o centro de eventos e a conta de armazenamento:

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
   }
   ```
3. Criar mais uma classe chamada `EventProcessor` , utilizando o seguinte código:

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

Este tutorial utiliza uma única instância do EventProcessorHost. Para aumentar a produção, recomendamos que execute várias instâncias de EventProcessorHost, de preferência em máquinas separadas.  Também proporciona redundância. Nesses casos, as várias instâncias coordenam-se automaticamente entre si para equilibrarem a carga dos eventos recebidos. Se pretender várias recetores para que cada um processe *todos* os eventos, terá de utilizar o conceito **ConsumerGroup**. Se receber eventos de vários computadores, poderá ser útil especificar os nomes das instâncias do EventProcessorHost com base nos computadores (ou funções) em que estão implementadas.

### <a name="publishing-messages-to-eventhub"></a>Publicar mensagens para o EventHub

Antes de as mensagens serem recuperadas pelos consumidores, têm de ser publicadas nas divisórias primeiro pelos editores. Vale a pena notar que quando as mensagens são publicadas para o centro de eventos sincronizadamente usando o método sendSync() no com.microsoft.azure.eventhubs.EventHubClient, a mensagem pode ser enviada para uma partição específica ou distribuída a todas as divisórias disponíveis de forma redonda, dependendo se a chave de partição é especificada ou não.

Quando uma corda que representa a chave de partição é especificada, a chave é hashed para determinar para que partição enviar o evento.

Quando a chave de partição não está definida, então as mensagens são arredondadas para todas as divisórias disponíveis

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

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementação de um CheckpointManager personalizado para EventProcessorHost (EPH)

A API fornece um mecanismo para implementar o seu gestor de checkpoint personalizado para cenários em que a implementação padrão não seja compatível com o seu caso de utilização.

O gestor de checkpoint padrão utiliza o armazenamento de blob, mas se substituir o gestor de checkpoint utilizado pela EPH com a sua própria implementação, pode utilizar qualquer loja que pretenda apoiar a implementação do seu gestor de checkpoint.

Criar uma classe que implemente a interface com.microsoft.azure.eventprocessorhost.ICheckpointManager

Utilize a sua implementação personalizada do gestor de checkpoint (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Dentro da sua implementação, pode substituir o mecanismo de verificação padrão e implementar os nossos próprios pontos de verificação com base na sua própria loja de dados (como SQL Server, CosmosDB e Azure Cache para Redis). Recomendamos que a loja utilizada para apoiar a implementação do seu gestor de controlo seja acessível a todas as instâncias EPH que estão a processar eventos para o grupo de consumidores.

Pode utilizar qualquer datastore que esteja disponível no seu ambiente.

A classe com.microsoft.azure.eventprocessorhost.EventProcessorHost fornece-lhe dois construtores que lhe permitem substituir o gestor de checkpoint para o seu EventProcessorHost.

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.yml)
