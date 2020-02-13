---
title: Use as filas de ônibus de serviço Azure com Java
description: Neste tutorial, aprende-se a criar aplicações Java para enviar mensagens e receber mensagens de uma fila de ônibus de serviço azure.
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
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: d819d4f7b3049a5c034ec8ac5170175f3ad3e9bb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190837"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Quickstart: Use as filas de ônibus do Azure Service com a Java para enviar e receber mensagens

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Neste tutorial, aprende-se a criar aplicações Java para enviar mensagens e receber mensagens de uma fila de ônibus de serviço azure. 

> [!NOTE]
> Você pode encontrar amostras de Java no GitHub no [repositório de ônibus azure-service](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do [seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de ônibus de serviço para criar uma fila.
    1. Leia a **visão geral** rápida das filas de **ônibus**de serviço . 
    2. Crie um espaço de **nome**de ônibus de serviço . 
    3. Pegue a corda de **ligação**.
    4. Crie uma **fila**de ônibus de serviço.
3. Instale [o Azure SDK para Java.][Azure SDK for Java] 


## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Certifique-se de que instalou o [SDK Azure para Java][Azure SDK for Java] antes de construir esta amostra. Se estiver a utilizar o Eclipse, pode instalar o [Kit de Ferramentas Azure para eclipse][Azure Toolkit for Eclipse] que inclui o Azure SDK para Java. Em seguida, pode adicionar as **Bibliotecas Microsoft Azure para Java** ao seu projeto:

![Adicione bibliotecas Microsoft Azure para Java ao seu projeto Eclipse](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)

Adicione as seguintes declarações `import` ao topo do ficheiro Java:

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
Para enviar mensagens para uma fila de ônibus de serviço, a sua aplicação instantaneamente um objeto **QueueClient** e envia mensagens sincronicamente. O código seguinte mostra como enviar uma mensagem para uma Fila que foi criada através do portal.

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

As mensagens enviadas e recebidas das filas de ônibus de serviço são exemplos da classe [Mensagem.](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) Os objetos de mensagem têm um conjunto de propriedades padrão (como Label e TimeToLive), um dicionário que é usado para conter propriedades personalizadas específicas da aplicação, e um corpo de dados de aplicação arbitrárias. Uma aplicação pode definir o corpo da mensagem passando qualquer objeto serializável no construtor da Mensagem, e o serializador apropriado será então usado para serializar o objeto. Em alternativa, pode fornecer um **java. Io. Objeto InputStream.**


As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
A principal forma de receber mensagens de uma fila é usar um objeto **ServiceBusContract.** As mensagens recebidas podem funcionar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao utilizar o modo **ReceiveAndDelete,** receber é uma operação de tiro único - isto é, quando o Service Bus recebe um pedido de leitura de uma mensagem numa fila, marca a mensagem como sendo consumida e devolve-a à aplicação. O modo **ReceiveAndDelete** (que é o modo predefinido) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento.
Como o Service Bus marcou a mensagem como sendo consumida, então quando a aplicação recomeça e começa a consumir mensagens novamente, perdeu a mensagem que foi consumida antes do acidente.

No modo **PeekLock,** o receive torna-se uma operação de duas fases, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação terminar de processar a mensagem (ou armazená-la de forma fiável para processamento futuro), completa a segunda fase do processo de receção, ligando **completa()** à mensagem recebida. Quando o Ônibus de Serviço vê a chamada **completa,** marca a mensagem como sendo consumida e retira-a da fila. 

O exemplo que se segue demonstra como as mensagens podem ser recebidas e processadas utilizando o modo **PeekLock** (não o modo predefinido). O exemplo abaixo utiliza o modelo de callback com um manipulador de mensagens registado e processa mensagens à medida que chegam ao nosso `TestQueue`. Este modo **chama-se completo()** automaticamente à medida que o retorno de chamada regressa normalmente e chama **de abandono()** se a chamada for uma exceção. 

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem por alguma razão, então pode ligar para o método de **abandono()** sobre o objeto cliente com o sinal de bloqueio da mensagem recebida obtido através do **getLockToken()** . Tal faz com que o Service Bus desbloqueie a mensagem na fila e torna a mesma disponível para ser novamente recebida, quer pela mesma aplicação de consumo quer por outra aplicação de consumo.

Há também um intervalo associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia a mensagem automaticamente e fá-la disponível para ser recebido novamente.

No caso de a aplicação se falhar após o processamento da mensagem, mas antes de ser emitido o pedido **completo,** a mensagem é retransmitida à aplicação quando recomeçar. Tal é, frequentemente, designado *Processar Pelo Menos Uma Vez*; ou seja, cada mensagem é processada pelo menos uma vez, contudo, em determinadas situações, a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é muitas vezes conseguido usando o método **getMessageId** da mensagem, que permanece constante através de tentativas de entrega.

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu o básico das filas de ônibus de serviço, consulte [filas, tópicos e subscrições][Queues, topics, and subscriptions] para mais informações.

Para obter mais informações, veja [Centro para Programadores do Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: https://docs.microsoft.com/java/api/overview/azure/
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
