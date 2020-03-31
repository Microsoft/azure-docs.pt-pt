---
title: Envie ou receba eventos de Azure Event Hubs usando Java (mais recente)
description: Este artigo fornece uma passagem pela criação de uma aplicação Java que envia/recebe eventos de/para O Azure Event Hubs usando o mais recente pacote de eventos de mensagens azure.eventos.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 44f57f52be512924e228d6488a786d117c6444e7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370598"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Use Java para enviar eventos ou receber eventos de Azure Event Hubs (azure-messaging-eventhubs)
Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando o pacote **de eventos de mensagens azure-eventhubs** Java.

> [!IMPORTANT]
> Este quickstart usa o novo pacote de **eventos de mensagens azure.** Para um arranque rápido que usa os **antigos hubs de eventos e** pacotes **azure-eventhubs-eph,** consulte [Enviar e receber eventos usando hubs de eventos e azure-eventhubs-eph](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte a visão geral do [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure.** Para utilizar os serviços Azure, incluindo o Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os seus benefícios de subscrição MSDN quando [criar uma conta](https://azure.microsoft.com).
- Um ambiente de desenvolvimento de Java. Este quickstart usa [Eclipse](https://www.eclipse.org/). É necessário o Java Development Kit (JDK) com a versão 8 ou superior. 
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo.](event-hubs-create.md) Em seguida, obtenha a cadeia de **ligação para o espaço** de nome do Event Hubs seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Usa a corda de ligação mais tarde neste arranque rápido.

## <a name="send-events"></a>Enviar eventos 
Esta secção mostra-lhe como criar uma aplicação Java para enviar eventos um centro de eventos. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Adicione referência à biblioteca Azure Event Hubs

A biblioteca de clientes Java para Centros de Eventos está disponível para uso em projetos [Maven do Repositório Central de Maven.](https://search.maven.org/search?q=a:azure-messaging-eventhubs) Pode fazer referência a esta biblioteca utilizando a seguinte declaração de dependência dentro do seu ficheiro de projeto Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. Adicione uma `SimpleSend`classe com o nome e adicione o seguinte código à classe:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Conexão string e centro de eventos
Este código utiliza a cadeia de ligação ao espaço de nome do Event Hubs e o nome do centro de eventos para construir um cliente do Event Hubs. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Criar um cliente produtor de Hubs de Eventos 
Este código cria um objeto cliente produtor que é usado para produzir/enviar eventos para o centro do evento. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Prepare um lote de eventos
Este código prepara um lote de eventos. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Envie o lote de eventos para o centro do evento
Este código envia o lote de eventos que preparou no passo anterior para o centro do evento. Os seguintes blocos de código na operação de envio. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Fechar e limpar
Este código fecha o produtor. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Código completo para enviar eventos
Aqui está o código completo para enviar eventos para o centro do evento. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Construa o programa e certifique-se de que não há erros. Executará este programa depois de executar o programa recetor. 

## <a name="receive-events"></a>Receber eventos
O código neste tutorial baseia-se na amostra do [EventProcessorClient no GitHub,](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java)que pode examinar para ver a aplicação de trabalho completa.

### <a name="create-a-java-project"></a>Criar um projeto Java

A biblioteca de clientes Java para Centros de Eventos está disponível para utilização em projetos Maven do [Repositório Central maven,](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)e pode ser referenciada usando a seguinte declaração de dependência dentro do seu ficheiro de projeto Maven: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Utilize o seguinte código para criar uma nova classe designada `Receiver`. Substitua os espaços reservados pelos valores utilizados quando criou o centro de eventos e a conta de armazenamento:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new InMemoryCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. Descarregue o ficheiro **InMemoryCheckpointStore.java** a partir do [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/InMemoryCheckpointStore.java)e adicione-o ao seu projeto. 
3. Construa o programa e certifique-se de que não há erros. 

## <a name="run-the-applications"></a>Executar as aplicações
1. Executar a aplicação **recetora** primeiro.
1. Em seguida, executar a aplicação de **remetente.** 
1. Na janela de aplicação do **recetor,** confirme se vê os eventos que foram publicados pela aplicação remetente.
1. Pressione **ENTER** na janela de aplicação do recetor para parar a aplicação. 

## <a name="next-steps"></a>Passos seguintes
Confira [as amostras de Java SDK no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

