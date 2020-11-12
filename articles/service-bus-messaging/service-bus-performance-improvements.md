---
title: Melhores práticas para melhorar o desempenho usando o Azure Service Bus
description: Descreve como usar o Service Bus para otimizar o desempenho ao trocar mensagens intermediadas.
ms.topic: article
ms.date: 11/11/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9104c5f4a01459c00327da1b60ad811787b7e22f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541271"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Melhores práticas para uma melhoria do desempenho com as Mensagens do Service Bus

Este artigo descreve como usar o Azure Service Bus para otimizar o desempenho ao trocar mensagens intermediadas. A primeira parte deste artigo descreve diferentes mecanismos para aumentar o desempenho. A segunda parte fornece orientações sobre a utilização do Service Bus de uma forma que pode oferecer o melhor desempenho num determinado cenário.

Ao longo deste artigo, o termo "cliente" refere-se a qualquer entidade que acede ao Service Bus. Um cliente pode assumir o papel de remetente ou recetor. O termo "remetente" é usado para um cliente de fila de Service Bus ou um cliente tópico que envia mensagens para uma fila de Autocarros de Serviço ou um tópico. O termo "recetor" refere-se a um cliente de fila de serviço ou cliente de subscrição que recebe mensagens de uma fila de Service Bus ou de uma subscrição.

## <a name="protocols"></a>Protocolos
A Service Bus permite que os clientes enviem e recebam mensagens através de um dos três protocolos:

1. Avançadas Message Queuing Protocol (AMQP)
2. Protocolo de mensagens de autocarro de serviço (SBMP)
3. Protocolo HTTP (Hypertext Transfer Protocol)

A AMQP é a mais eficiente, pois mantém a ligação ao Service Bus. Também implementa o loteamento e a prefetching. A menos que explicitamente mencionado, todo o conteúdo deste artigo assume a utilização de AMQP ou SBMP.

> [!IMPORTANT]
> O SBMP só está disponível para o Quadro .NET. AMQP é o padrão para .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Escolha do ônibus de serviço apropriado .NET SDK
Existem dois Azure Service Bus (NET SDKs) suportados. As apis são semelhantes, e pode ser confuso qual escolher. Consulte a seguinte tabela para ajudar a orientar a sua decisão. Sugerimos a utilização do Microsoft.Azure.ServiceBus SDK, uma vez que é mais moderno, performante e é compatível com plataformas cruzadas. Além disso, suporta AMQP sobre WebSockets e faz parte da coleção Azure .NET SDK de projetos de código aberto.

| Pacote NuGet | Espaço de nome primário | Plataformas mínimas | Protocolo(s) |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.Net Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Plataforma Universal Windows 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .Net Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Para obter mais informações sobre o suporte mínimo da plataforma .NET Standard, consulte [o suporte de implementação .NET](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Reutilização de fábricas e clientes

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Os objetos clientes do Service Bus, tais como implementações de [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender] ou, devem ser registados para injeção de dependência como singletons (ou instantâneos uma vez e partilhados). Recomendamos que não feche as fábricas de mensagens, filas, tópicos ou clientes por subscrição depois de enviar uma mensagem e, em seguida, recorá-las quando enviar a próxima mensagem. O encerramento de uma fábrica de mensagens elimina a ligação ao serviço Service Bus. Uma nova ligação é estabelecida na recriação da fábrica. Estabelecer uma ligação é uma operação dispendiosa que pode evitar reutilizando a mesma fábrica e objetos clientes para múltiplas operações. Pode utilizar estes objetos clientes com segurança para operações assíncronos simultâneas e a partir de vários fios.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Os objetos clientes do Service Bus, tais como `QueueClient` `MessageSender` ou, são criados através de um objeto [MessagingFactory,][MessagingFactory] que também fornece uma gestão interna de ligações. Recomendamos que não feche as fábricas de mensagens, filas, tópicos ou clientes por subscrição depois de enviar uma mensagem e, em seguida, recorá-las quando enviar a próxima mensagem. O encerramento de uma fábrica de mensagens elimina a ligação ao serviço Service Bus e é estabelecida uma nova ligação ao recriar a fábrica. Estabelecer uma ligação é uma operação dispendiosa que pode evitar reutilizando a mesma fábrica e objetos clientes para múltiplas operações. Pode utilizar estes objetos clientes com segurança para operações assíncronos simultâneas e a partir de vários fios.

---

## <a name="concurrent-operations"></a>Operações simultâneas
Operações como enviar, receber, apagar, e assim por diante, levar algum tempo. Desta vez inclui o tempo que o serviço service bus leva para processar a operação e a latência do pedido e a resposta. Para aumentar o número de operações por tempo, as operações devem ser executadas simultaneamente.

O cliente agenda operações simultâneas através da realização de operações **assíncronos.** O próximo pedido é iniciado antes do pedido anterior ser concluído. O seguinte corte de código é um exemplo de uma operação de envio assíncronos:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

O seguinte código é um exemplo de uma operação de receção assíncronea.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Consulte o repositório GitHub para obter <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">exemplos completos <span class="docon docon-navigate-external x-hidden-focus"></span> de códigos de origem: </a>

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

O `MessageReceiver` objeto é instantâneo com a cadeia de ligação, nome de fila e um modo de receção de olhar de espreitar. Em seguida, o `receiver` caso é usado para registar o manipulador de mensagens.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Consulte o repositório GitHub para obter <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">exemplos completos <span class="docon docon-navigate-external x-hidden-focus"></span> de códigos de origem: </a>

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

O `MessagingFactory` cria um objeto a partir da cadeia de `factory` ligação. Com o `factory` exemplo, um `MessageReceiver` é instantâneo. Em seguida, o `receiver` caso é usado para registar o manipulador de mensagens.

---

## <a name="receive-mode"></a>Modo de receção

Ao criar uma fila ou cliente de subscrição, pode especificar um modo de receção: *Peek-lock* ou *Receive and Delete*. O modo de receção predefinido é `PeekLock` . Ao operar no modo predefinido, o cliente envia um pedido para receber uma mensagem da Service Bus. Depois de o cliente ter recebido a mensagem, envia um pedido para completar a mensagem.

Ao ajustar o modo de receção para `ReceiveAndDelete` , ambos os passos são combinados num único pedido. Estes passos reduzem o número total de operações e podem melhorar o rendimento global da mensagem. Este ganho de desempenho corre o risco de perder mensagens.

A Service Bus não suporta transações para operações de receção e eliminação. Além disso, são necessárias semânticas para quaisquer cenários em que o cliente queira adiar ou [escrever](service-bus-dead-letter-queues.md) uma mensagem.

## <a name="client-side-batching"></a>Loteamento do lado do cliente

O lote do lado do cliente permite que uma fila ou cliente tópico atrase o envio de uma mensagem por um certo período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. O lote do lado do cliente também causa uma fila ou cliente de subscrição para lotear vários pedidos **Completos** num único pedido. O loteamento só está disponível para operações assíncronos **de envio** e **completa.** As operações sincronizadas são imediatamente enviadas para o serviço service bus. O lote não ocorre para espreitar ou receber operações, nem o lote ocorre entre os clientes.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

A funcionalidade de loteamento para o .NET Standard SDK, ainda não expõe uma propriedade a manipular.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Por predefinição, um cliente usa um intervalo de lote de 20 ms. Pode alterar o intervalo de lote definindo a propriedade [BatchFlushInterval][BatchFlushInterval] antes de criar a fábrica de mensagens. Esta configuração afeta todos os clientes que são criados por esta fábrica.

Para desativar o lote, desloque a propriedade [BatchFlushInterval][BatchFlushInterval] para **timeSpan.Zero**. Por exemplo:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

O lote não afeta o número de operações de mensagens faturadas e está disponível apenas para o protocolo do cliente Service Bus utilizando a biblioteca [Microsoft.ServiceBus.Messaging.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) O protocolo HTTP não suporta lotes.

> [!NOTE]
> A definição `BatchFlushInterval` garante que o lote está implícito na perspetiva da aplicação. ou seja, isto é, a aplicação faz `SendAsync` e liga e não faz chamadas `CompleteAsync` específicas do Batch.
>
> O lote lateral explícito do cliente pode ser implementado utilizando a chamada de método abaixo:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Aqui, o tamanho combinado das mensagens deve ser inferior ao tamanho máximo suportado pelo nível de preços.

---

## <a name="batching-store-access"></a>Acesso à loja de loting

Para aumentar a produção de uma fila, tópico ou subscrição, o Service Bus em lotes de várias mensagens quando escreve para a sua loja interna. 

- Quando ativar o lote numa fila, escrever mensagens na loja e eliminar mensagens da loja será em lotado. 
- Quando ativa o lote de um tópico, escrever mensagens na loja é lotado. 
- Quando ativa o loteamento numa subscrição, a eliminação de mensagens da loja são em lotadas. 
- Quando o acesso a uma loja em lote é ativado para uma entidade, o Service Bus atrasa uma operação de escrita de loja para essa entidade até 20 ms.

> [!NOTE]
> Não existe o risco de perder mensagens com loting, mesmo que exista uma falha no Service Bus no final de um intervalo de loteamento de 20ms.

As operações adicionais de loja que ocorram durante este intervalo são adicionadas ao lote. O acesso ao armazém em lote apenas afeta as operações **de envio** e **completa;** as operações de receção não são afetadas. O acesso a lojas em loted é uma propriedade de uma entidade. O lote ocorre em todas as entidades que permitem o acesso à loja em lote.

Ao criar uma nova fila, tópico ou subscrição, o acesso à loja em lote é ativado por padrão.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Para desativar o acesso à loja em lotado, necessitará de um exemplo de `ManagementClient` . Crie uma fila a partir de uma descrição de fila que define a `EnableBatchedOperations` propriedade para `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Para obter mais informações, veja os seguintes artigos:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Para desativar o acesso à loja em lotado, necessitará de um exemplo de `NamespaceManager` . Crie uma fila a partir de uma descrição de fila que define a `EnableBatchedOperations` propriedade para `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Para obter mais informações, veja os seguintes artigos:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

O acesso a lojas lotadas não afeta o número de operações de mensagens faturadas. É uma propriedade de uma fila, tópico ou subscrição. É independente do modo de receção e do protocolo que é usado entre um cliente e o serviço Service Bus.

## <a name="prefetching"></a>Pré-defetching

[A prefetching](service-bus-prefetch.md) permite que o cliente da fila ou subscrição carregue mensagens adicionais do serviço quando recebe mensagens. O cliente armazena estas mensagens numa cache local. O tamanho da cache é determinado pelas `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` propriedades ou propriedades. Cada cliente que permite a pré-recatada mantém a sua própria cache. Uma cache não é partilhada entre clientes. Se o cliente iniciar uma operação de receção e a cache estiver vazia, o serviço transmite um lote de mensagens. O tamanho do lote é igual ao tamanho da cache ou 256 KB, o que for menor. Se o cliente iniciar uma operação de receção e a cache contiver uma mensagem, a mensagem é retirada da cache.

Quando uma mensagem é prefecção, o serviço bloqueia a mensagem pré-gravada. Com a fechadura, a mensagem pré-gravada não pode ser recebida por um recetor diferente. Se o recetor não conseguir completar a mensagem antes de o bloqueio expirar, a mensagem fica disponível para outros recetores. A cópia prefetchda da mensagem permanece na cache. O recetor que consome a cópia em cache expirada receberá uma exceção quando tentar completar essa mensagem. Por predefinição, o bloqueio da mensagem expira após 60 segundos. Este valor pode ser estendido para 5 minutos. Para evitar o consumo de mensagens vencidas, desa ajuste o tamanho da cache inferior ao número de mensagens que um cliente pode consumir dentro do intervalo de tempo de bloqueio.

Ao utilizar o bloqueio predefinido de 60 segundos, um bom valor `PrefetchCount` é 20 vezes superior às taxas máximas de processamento de todos os recetores da fábrica. Por exemplo, uma fábrica cria três recetores, e cada recetor pode processar até 10 mensagens por segundo. A contagem de prefetch não deve exceder 20 X 3 X 10 = 600. Por predefinição, `PrefetchCount` está definido para 0, o que significa que não são recolhidas mensagens adicionais do serviço.

As mensagens de prefetching aumentam a produção geral para uma fila ou subscrição porque reduz o número total de operações de mensagens ou viagens de ida e volta. No entanto, a procura da primeira mensagem demorará mais tempo (devido ao aumento do tamanho da mensagem). Receber mensagens pré-gravadas da cache será mais rápido porque estas mensagens já foram descarregadas pelo cliente.

A propriedade time-to-live (TTL) de uma mensagem é verificada pelo servidor no momento em que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem pode ser recebida mesmo que o TTL da mensagem tenha passado enquanto a mensagem foi em cache pelo cliente.

A prefetching não afeta o número de operações de mensagens faturadas, e está disponível apenas para o protocolo do cliente do Service Bus. O protocolo HTTP não suporta a pré-fetching. A prefetching está disponível para operações de receção sincronizadas e assíncronos.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Para mais informações, consulte as seguintes `PrefetchCount` propriedades:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Para mais informações, consulte as seguintes `PrefetchCount` propriedades:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Pré-fetching e ReceiveBatch

> [!NOTE]
> Esta secção aplica-se apenas ao WindowsAzure.ServiceBus SDK, uma vez que o Microsoft.Azure.ServiceBus SDK não expõe as funções do lote.

Embora os conceitos de prefôr várias mensagens em conjunto tenham semântica semelhante ao processamento de mensagens num lote `ReceiveBatch` (), existem algumas pequenas diferenças que devem ser mantidas em mente ao usar estas abordagens em conjunto.

Prefetch é uma configuração (ou modo) no cliente ( `QueueClient` e ) e é uma `SubscriptionClient` `ReceiveBatch` operação (que tem semântica de resposta de pedido).

Ao utilizar estas abordagens em conjunto, considere os seguintes casos -

* A prefetch deve ser maior ou igual ao número de mensagens de que espera receber `ReceiveBatch` .
* A prefetch pode ser até n/3 vezes o número de mensagens processadas por segundo, onde n é a duração de bloqueio predefinido.

Há alguns desafios em ter uma abordagem gananciosa, isto é, manter a contagem de prefetch alta, porque implica que a mensagem está bloqueada a um determinado recetor. A recomendação é experimentar valores prefetch entre os limiares acima mencionados e identificar empiricamente o que se adequa.

## <a name="multiple-queues"></a>Múltiplas filas

Se uma única fila ou tópico não conseguir lidar com o esperado, utilize várias entidades de mensagens. Ao utilizar várias entidades, crie um cliente dedicado para cada entidade, em vez de utilizar o mesmo cliente para todas as entidades.

## <a name="development-and-testing-features"></a>Características de desenvolvimento e teste

> [!NOTE]
> Esta secção aplica-se apenas ao WindowsAzure.ServiceBus SDK, uma vez que o Microsoft.Azure.ServiceBus SDK não expõe esta funcionalidade.

O Service Bus tem uma característica, utilizada especificamente para o desenvolvimento, que **nunca deve ser utilizada nas configurações de produção:** [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

Quando novas regras ou filtros são adicionados ao tópico, pode usar [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] para verificar se a nova expressão do filtro está a funcionar como esperado.

## <a name="scenarios"></a>Cenários

As secções seguintes descrevem cenários típicos de mensagens e delineiam as definições preferidas do Service Bus. As taxas de produção são classificadas como pequenas (menos de 1 mensagem/segundo), moderadas (1 mensagem/segunda ou superior, mas menos de 100 mensagens/segundo) e altas (100 mensagens/segundo ou superior). O número de clientes é classificado como pequeno (5 ou menos), moderado (mais de 5 mas menos ou igual a 20) e grande (mais de 20).

### <a name="high-throughput-queue"></a>Fila de alta produção

Objetivo: Maximizar a produção de uma única fila. O número de remetentes e recetores é pequeno.

* Para aumentar a taxa de envio geral para a fila, utilize várias fábricas de mensagens para criar remetentes. Para cada remetente, utilize operações assíncronos ou múltiplos fios.
* Para aumentar a taxa de receção global da fila, utilize várias fábricas de mensagens para criar recetores.
* Utilize operações assíncronos para tirar partido do lote do lado do cliente.
* Desa cosar o intervalo de loteamento para 50 ms para reduzir o número de transmissões de protocolo de cliente do Service Bus. Se forem utilizados vários remetentes, aumente o intervalo de lote para 100 ms.
* Deixe o acesso ao armazém ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas na fila.
* Desaprote a contagem de prefetch para 20 vezes as taxas máximas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolo de clientes do Service Bus.

### <a name="multiple-high-throughput-queues"></a>Múltiplas filas de alta produção

Objetivo: Maximizar a produção geral de várias filas. A produção de uma fila individual é moderada ou alta.

Para obter a máxima produção através de várias filas, utilize as definições delineadas para maximizar a produção de uma única fila. Além disso, utilize diferentes fábricas para criar clientes que enviam ou recebem de diferentes filas.

### <a name="low-latency-queue"></a>Fila de baixa latência

Objetivo: Minimizar a latência de uma fila ou tópico. O número de remetentes e recetores é pequeno. A produção da fila é pequena ou moderada.

* Desative o lote do lado do cliente. O cliente envia imediatamente uma mensagem.
* Desative o acesso à loja em lotes. O serviço escreve imediatamente a mensagem para a loja.
* Se utilizar um único cliente, desaprote a contagem de prefetch para 20 vezes a taxa de processamento do recetor. Se várias mensagens chegarem à fila ao mesmo tempo, o protocolo do cliente do Service Bus transmite-as todas ao mesmo tempo. Quando o cliente recebe a próxima mensagem, essa mensagem já está na cache local. A cache deve ser pequena.
* Se utilizar vários clientes, deslofe a contagem de prefetch para 0. Ao definir a contagem, o segundo cliente pode receber a segunda mensagem enquanto o primeiro cliente ainda está a processar a primeira mensagem.

### <a name="queue-with-a-large-number-of-senders"></a>Fila com um grande número de remetentes

Objetivo: Maximizar a produção de uma fila ou tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa moderada. O número de recetores é pequeno.

O Service Bus permite até 1000 ligações simultâneas a uma entidade de mensagens. Esta limit é aplicada ao nível do espaço de nome, e as filas, tópicos ou subscrições são limitados pelo limite de ligações simultâneas por espaço de nome. Para as filas, este número é partilhado entre remetentes e recetores. Se todas as 1000 ligações forem necessárias para os remetentes, substitua a fila por um tópico e uma única subscrição. Um tópico aceita até 1000 ligações simultâneas de remetentes. A subscrição aceita mais 1000 ligações simultâneas de recetores. Se forem necessários mais de 1000 remetentes simultâneos, os remetentes devem enviar mensagens para o protocolo de Service Bus via HTTP.

Para maximizar a produção, siga estes passos:

* Se cada remetente estiver num processo diferente, utilize apenas uma única fábrica por processo.
* Utilize operações assíncronos para tirar partido do lote do lado do cliente.
* Utilize o intervalo de lote predefinido de 20 ms para reduzir o número de transmissões de protocolo de cliente do Service Bus.
* Deixe o acesso ao armazém ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas na fila ou tópico.
* Desaprote a contagem de prefetch para 20 vezes as taxas máximas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolo de clientes do Service Bus.

### <a name="queue-with-a-large-number-of-receivers"></a>Fila com um grande número de recetores

Objetivo: Maximizar a taxa de receção de uma fila ou subscrição com um grande número de recetores. Cada recetor recebe mensagens a um ritmo moderado. O número de remetentes é pequeno.

O Service Bus permite até 1000 ligações simultâneas a uma entidade. Se uma fila necessitar de mais de 1000 recetores, substitua a fila por um tópico e várias subscrições. Cada subscrição pode suportar até 1000 ligações simultâneas. Em alternativa, os recetores podem aceder à fila através do protocolo HTTP.

Para maximizar o rendimento, siga estas orientações:

* Se cada recetor estiver num processo diferente, utilize apenas uma única fábrica por processo.
* Os recetores podem utilizar operações sincronizadas ou assíncronos. Dada a taxa de receção moderada de um recetor individual, o loteamento do lado do cliente de um pedido completo não afeta a produção do recetor.
* Deixe o acesso ao armazém ativado. Este acesso reduz a carga global da entidade. Também reduz a taxa global a que as mensagens podem ser escritas na fila ou no tópico.
* Desaprova a contagem de prefetchs a um pequeno valor (por exemplo, PrefetchCount = 10). Esta contagem impede que os recetores sejam inativos enquanto outros recetores têm um grande número de mensagens em cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tópico com um pequeno número de subscrições

Objetivo: Maximizar a produção de um tópico com um pequeno número de subscrições. Uma mensagem é recebida por muitas subscrições, o que significa que a taxa de receção combinada sobre todas as subscrições é maior do que a taxa de envio. O número de remetentes é pequeno. O número de recetores por subscrição é pequeno.

Para maximizar o rendimento, siga estas orientações:

* Para aumentar a taxa de envio global para o tópico, utilize várias fábricas de mensagens para criar remetentes. Para cada remetente, utilize operações assíncronos ou múltiplos fios.
* Para aumentar a taxa de receção global a partir de uma subscrição, utilize várias fábricas de mensagens para criar recetores. Para cada recetor, utilize operações assíncronos ou múltiplos fios.
* Utilize operações assíncronos para tirar partido do lote do lado do cliente.
* Utilize o intervalo de lote predefinido de 20 ms para reduzir o número de transmissões de protocolo de cliente do Service Bus.
* Deixe o acesso ao armazém ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas no tópico.
* Desaprote a contagem de prefetch para 20 vezes as taxas máximas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolo de clientes do Service Bus.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tópico com um grande número de subscrições

Objetivo: Maximizar a produção de um tópico com um grande número de subscrições. Uma mensagem é recebida por muitas subscrições, o que significa que a taxa de receção combinada sobre todas as subscrições é muito maior do que a taxa de envio. O número de remetentes é pequeno. O número de recetores por subscrição é pequeno.

Os tópicos com um grande número de subscrições normalmente expõem uma baixa produção geral se todas as mensagens forem encaminhadas para todas as subscrições. É porque cada mensagem é recebida muitas vezes, e todas as mensagens num tópico e todas as suas subscrições são armazenadas na mesma loja. O pressuposto aqui é que o número de remetentes e o número de recetores por subscrição é pequeno. A Service Bus suporta até 2.000 subscrições por tópico.

Para maximizar a produção, experimente os seguintes passos:

* Utilize operações assíncronos para tirar partido do lote do lado do cliente.
* Utilize o intervalo de lote predefinido de 20 ms para reduzir o número de transmissões de protocolo de cliente do Service Bus.
* Deixe o acesso ao armazém ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas no tópico.
* Desaprote a contagem de pré-câmbios para 20 vezes a taxa de receção esperada em segundos. Esta contagem reduz o número de transmissões de protocolo de clientes do Service Bus.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md