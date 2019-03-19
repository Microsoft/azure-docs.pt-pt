---
title: Como utilizar as filas do Service bus do Azure com Java | Documentos da Microsoft
description: Saiba como utilizar as filas do Service Bus no Azure. Exemplos de códigos escritos em Java.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/13/2018
ms.author: aschhab
ms.openlocfilehash: f226b9b802bca47cc6fd7b9cdec550b23c7c88d6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890827"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Como utilizar filas do Service Bus com Java
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar as filas do Service Bus. Os exemplos são escritos em Java e utilize o [Azure SDK para Java][Azure SDK for Java]. Os cenários abrangidos incluem **criando filas**, **enviar e receber mensagens**, e **eliminar filas**.

> [!NOTE]
> Pode encontrar exemplos de Java no GitHub no [repositório azure-service-bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Certifique-se de que instalou o [Azure SDK para Java] [ Azure SDK for Java] antes de criar este exemplo. Se estiver a utilizar Eclipse, pode instalar o [Azure Toolkit para Eclipse] [ Azure Toolkit for Eclipse] que inclui o Azure SDK para Java. Em seguida, pode adicionar os **bibliotecas do Microsoft Azure para Java** ao seu projeto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adicione o seguinte `import` declarações na parte superior do ficheiro Java:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar mensagens para uma fila do Service Bus, seu aplicativo cria uma instância de um **QueueClient** de objeto e envia mensagens de forma assíncrona. O código seguinte mostra como enviar uma mensagem para uma fila que foi criada através do portal.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

As mensagens enviadas para e recebidos do Service Bus filas são instâncias do [mensagem](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) classe. Objetos de mensagens têm um conjunto de propriedades padrão (como a etiqueta e TimeToLive), um dicionário utilizado para reter propriedades personalizadas de específico do aplicativo e um corpo de dados arbitrários da aplicação. Uma aplicação pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor da mensagem e, em seguida, será utilizado o serializador apropriado para serializar o objeto. Em alternativa, pode fornecer um **java. E/S. InputStream** objeto.


As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
A principal maneira de receber mensagens de uma fila é utilizar um **ServiceBusContract** objeto. As mensagens recebidas podem funcionar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao utilizar o **ReceiveAndDelete** modo, receber é uma operação única - ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa fila, marca a mensagem como consumida e devolve a mesma à aplicação. **ReceiveAndDelete** modo (que é o modo predefinido) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento.
Porque o Service Bus marcou a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, ele tem estado em falta a mensagem consumida antes da falha de sistema.

Na **PeekLock** modo, receber torna-se uma operação de duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois da aplicação concluir o processamento da mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao chamar **eliminar** à mensagem recebida. Quando o Service Bus vê a **eliminar** chamada, ela marca a mensagem como consumida e removê-lo da fila.

O exemplo seguinte demonstra como as mensagens podem ser recebidas e processados usando **PeekLock** modo (não o modo predefinido). O exemplo a seguir faz um loop infinito e processa mensagens à medida que chegam à nossa `TestQueue`:

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
        // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, então pode chamar o **unlockMessage** método à mensagem recebida (em vez do **deleteMessage** método). Tal faz com que o Service Bus desbloqueie a mensagem na fila e torna a mesma disponível para ser novamente recebida, quer pela mesma aplicação de consumo quer por outra aplicação de consumo.

Há também um tempo limite associado à mensagem bloqueada na fila e, se a aplicação conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus desbloqueia automaticamente a mensagem e torna disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes a **deleteMessage** solicitação é emitida, em seguida, a mensagem é reenviada para a aplicação quando esta reiniciar. Tal é, frequentemente, designado *Processar Pelo Menos Uma Vez*; ou seja, cada mensagem é processada pelo menos uma vez, contudo, em determinadas situações, a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido através da **getMessageId** método da mensagem, que permanece constante nas tentativas de entrega.

## <a name="next-steps"></a>Próximos Passos
Agora que aprendeu as noções básicas de filas do Service Bus, veja [filas, tópicos e subscrições] [ Queues, topics, and subscriptions] para obter mais informações.

Para obter mais informações, veja [Centro para Programadores do Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: https://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
