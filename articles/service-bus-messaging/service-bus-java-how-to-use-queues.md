---
title: Usar filas do barramento de serviço do Azure com Java
description: Saiba como utilizar as filas do Service Bus no Azure. Exemplos de código escritos em Java.
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
ms.date: 04/10/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 84f035a55256ab22c823b2aaed9c9c2400eebf5b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173414"
---
# <a name="use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Usar filas do barramento de serviço do Azure com Java para enviar e receber mensagens
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Neste tutorial, você aprenderá a criar aplicativos Java para enviar e receber mensagens de uma fila do barramento de serviço. 

> [!NOTE]
> Você pode encontrar exemplos de Java no GitHub no [repositório Azure-Service-Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) de assinante do MSDN ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila.
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**.
    4. Criar uma **fila**do barramento de serviço.
3. Instale o [SDK do Azure para Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o barramento de serviço
Verifique se você instalou o [SDK do Azure para Java][Azure SDK for Java] antes de compilar este exemplo. Se você estiver usando o eclipse, poderá instalar o [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] que inclui o SDK do Azure para Java. Em seguida, você pode adicionar as **bibliotecas de Microsoft Azure para Java** ao seu projeto:

![Captura de tela da guia bibliotecas onde você pode adicionar bibliotecas](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adicione as seguintes `import` instruções à parte superior do arquivo Java:

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
Para enviar mensagens para uma fila do barramento de serviço, seu aplicativo instancia um objeto **QueueClient** e envia mensagens de forma assíncrona. O código a seguir mostra como enviar uma mensagem para uma fila que foi criada por meio do Portal.

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

As mensagens enviadas para e recebidas das filas do barramento de serviço são instâncias da classe de [mensagem](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) . Os objetos de mensagem têm um conjunto de propriedades padrão (como Label e TimeToLive), um dicionário que é usado para manter Propriedades personalizadas específicas do aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando qualquer objeto serializável para o construtor da mensagem, e o serializador apropriado será usado para serializar o objeto. Como alternativa, você pode fornecer um **Java. I. Objeto InputStream** .


As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
A maneira primária de receber mensagens de uma fila é usar um objeto **ServiceBusContract** . As mensagens recebidas podem funcionar em dois modos diferentes: **ReceiveAndDelete** e **Peeklock**.

Ao usar o modo **ReceiveAndDelete** , Receive é uma operação única, ou seja, quando o barramento de serviço recebe uma solicitação de leitura de uma mensagem em uma fila, ele marca a mensagem como sendo consumida e a retorna ao aplicativo. O modo **ReceiveAndDelete** (que é o modo padrão) é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento.
Como o barramento de serviço marcou a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele perdeu a mensagem que foi consumida antes da falha.

No modo **Peeklock** , Receive se torna uma operação de dois estágios, o que possibilita o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando **delete** na mensagem recebida. Quando o barramento de serviço vê a chamada de **exclusão** , ele marca a mensagem como sendo consumida e a remove da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **Peeklock** (não o modo padrão). O exemplo a seguir faz um loop infinito e processa as mensagens à medida que `TestQueue`elas chegam ao nosso:

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método **unlockMessage** na mensagem recebida (em vez do método **deleteMessage** ). Tal faz com que o Service Bus desbloqueie a mensagem na fila e torna a mesma disponível para ser novamente recebida, quer pela mesma aplicação de consumo quer por outra aplicação de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a tornará disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que a solicitação **deleteMessage** seja emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Tal é, frequentemente, designado *Processar Pelo Menos Uma Vez*; ou seja, cada mensagem é processada pelo menos uma vez, contudo, em determinadas situações, a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isso geralmente é obtido usando o método getmessageid da mensagem, que permanece constante nas tentativas de entrega.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximos Passos
Agora que você aprendeu os conceitos básicos das filas do barramento de serviço, consulte [filas, tópicos e assinaturas][Queues, topics, and subscriptions] para obter mais informações.

Para obter mais informações, veja [Centro para Programadores do Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: https://docs.microsoft.com/java/api/overview/azure/
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
