---
title: Boas práticas para melhorar o desempenho usando o Azure Service Bus
description: Descreve como usar o Service Bus para otimizar o desempenho ao trocar mensagens intermediadas.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 03/12/2020
ms.author: aschhab
ms.openlocfilehash: b864f433c67d47b4b92a1d4b98693ebd42806dd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259464"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Melhores práticas para uma melhoria do desempenho com as Mensagens do Service Bus

Este artigo descreve como usar o Azure Service Bus para otimizar o desempenho ao trocar mensagens intermediadas. A primeira parte deste artigo descreve os diferentes mecanismos que são oferecidos para ajudar a aumentar o desempenho. A segunda parte fornece orientações sobre como usar o Service Bus de uma forma que pode oferecer o melhor desempenho num determinado cenário.

Ao longo deste artigo, o termo "cliente" refere-se a qualquer entidade que aceda ao Service Bus. Um cliente pode assumir o papel de remetente ou recetor. O termo "remetente" é usado para uma fila de ônibus de serviço ou cliente tópico que envia mensagens para uma fila de ônibus de serviço ou subscrição de tópicos. O termo "recetor" refere-se a uma fila de ônibus de serviço ou cliente de subscrição que recebe mensagens de uma fila de ônibus de serviço ou subscrição.

Estas secções introduzem vários conceitos que o Service Bus usa para ajudar a impulsionar o desempenho.

## <a name="protocols"></a>Protocolos

O Service Bus permite que os clientes enviem e recebam mensagens através de um dos três protocolos:

1. Avançadas Message Queuing Protocol (AMQP)
2. Protocolo de Mensagens de Autocarro de Serviço (SBMP)
3. Protocolo HTTP (Hypertext Transfer Protocol)

A AMQP é a mais eficiente, porque mantém a ligação ao Service Bus. Também implementa lotação e prebuscação. Salvo indicação explícita, todos os conteúdos deste artigo assumem a utilização de AMQP ou SBMP.

> [!IMPORTANT]
> O SBMP só está disponível para .NET Framework. AMQP é o padrão padrão para .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Escolha o ônibus de serviço apropriado .NET SDK

Existem dois Azure Service Bus .NET SDKs suportados. As suas APIs são muito semelhantes, e pode ser confuso qual escolher. Consulte a tabela seguinte para ajudar a orientar a sua decisão. Sugerimos o Microsoft.Azure.ServiceBus SDK, uma vez que é mais moderno, performante e compatível com plataforma cruzada. Além disso, suporta amQP sobre WebSockets e faz parte da coleção Azure .NET SDK de projetos de código aberto.

| Pacote NuGet | Espaço de nome primário(s) | Plataforma Mínima(s) | Protocolo(s) |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.Quadro líquido 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Plataforma Universal Windows 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .Quadro líquido 4.6.1 | AMQP<br>SBMP<br>HTTP |

Para obter mais informações sobre o suporte mínimo da plataforma .NET Standard, consulte o suporte de [implementação .NET](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Reutilização de fábricas e clientes

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Os objetos do cliente do [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender]bus de serviço, tais como implementações de ou , devem ser registados para injeção de dependência como singletons (ou instantâneos uma vez e partilhados). Recomenda-se que não feche fábricas de mensagens ou clientes de fila, tópico e subscrição depois de enviar uma mensagem e, em seguida, recriá-las quando envia a próxima mensagem. O encerramento de uma fábrica de mensagens elimina a ligação ao serviço service bus, e uma nova ligação é estabelecida ao recriar a fábrica. Estabelecer uma ligação é uma operação dispendiosa que pode evitar reutilizando a mesma fábrica e objetos de cliente para múltiplas operações. Pode utilizar com segurança estes objetos de cliente para operações assíncronas simultâneas e a partir de vários fios.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Os objetos clientes `QueueClient` do `MessageSender`Service Bus, tais como ou, são criados através de um objeto de [MessagingFactory,][MessagingFactory] que também fornece gestão interna de ligações. Recomenda-se que não feche fábricas de mensagens ou clientes de fila, tópico e subscrição depois de enviar uma mensagem e, em seguida, recriá-las quando envia a próxima mensagem. O encerramento de uma fábrica de mensagens elimina a ligação ao serviço service bus, e uma nova ligação é estabelecida ao recriar a fábrica. Estabelecer uma ligação é uma operação dispendiosa que pode evitar reutilizando a mesma fábrica e objetos de cliente para múltiplas operações. Pode utilizar com segurança estes objetos de cliente para operações assíncronas simultâneas e a partir de vários fios.

---

## <a name="concurrent-operations"></a>Operações simultâneas

Realizar uma operação (enviar, receber, excluir, etc.) leva algum tempo. Desta vez inclui o processamento da operação pelo serviço de autocarros de serviço, para além da latência do pedido e da resposta. Para aumentar o número de operações por tempo, as operações devem ser executadas simultaneamente.

O cliente programa operações simultâneas através da realização de operações assíncronas. O próximo pedido é iniciado antes de o pedido anterior estar concluído. O seguinte código de corte é um exemplo de uma operação de envio assíncrono:

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

O código que se segue é um exemplo de uma operação de receção assíncrona.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Consulte o repositório GitHub para obter <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">exemplos completos <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>de código fonte:

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
        MaxConcurrentCalls = 1
    });
```

O `MessageReceiver` objeto é instantâneo com a corda de ligação, o nome da fila e um modo de receção de olhar espreito. Em seguida, a `receiver` instância é usada para registar o manipulador de mensagens.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Consulte o repositório GitHub para obter <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">exemplos completos <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>de código fonte:

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
        MaxConcurrentCalls = 1
    });
```

O `MessagingFactory` cria `factory` um objeto a partir da corda de ligação. Com `factory` o caso, um `MessageReceiver` é instantâneo. Em seguida, a `receiver` instância é usada para registar o manipulador de mensagens.

---

## <a name="receive-mode"></a>Receber modo

Ao criar uma fila ou cliente de subscrição, pode especificar um modo de receção: *Peek-lock* ou *Receive and Delete*. O modo de `PeekLock`receção predefinido é . Ao operar no modo predefinido, o cliente envia um pedido para receber uma mensagem do Service Bus. Depois de o cliente ter recebido a mensagem, envia um pedido para completar a mensagem.

Ao definir o `ReceiveAndDelete`modo de receção para , ambos os passos são combinados num único pedido. Estes passos reduzem o número total de operações e podem melhorar o resultado global da mensagem. Este ganho de desempenho surge com o risco de perder mensagens.

O Service Bus não suporta transações para operações de receção e eliminação. Além disso, são necessárias semânticas de bloqueio de espreitar para quaisquer cenários em que o cliente queira adiar ou enviar uma mensagem de [carta morta.](service-bus-dead-letter-queues.md)

## <a name="client-side-batching"></a>Lote do lado do cliente

O lote do lado do cliente permite que um cliente de fila ou tópico atrase o envio de uma mensagem por um determinado período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. O lote do lado do cliente também faz com que um cliente de fila ou subscrição emlota vários pedidos **completos** num único pedido. O lote só está disponível para operações assíncronas **de envio** e **completa.** As operações sincronizadas são imediatamente enviadas para o serviço de ônibus de serviço. O loteamento não ocorre para espreitar ou receber operações, nem o loting ocorre entre os clientes.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

A funcionalidade de lotação para o .NET Standard SDK, ainda não expõe uma propriedade para manipular.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Por padrão, um cliente usa um intervalo de lote de 20 ms. Pode alterar o intervalo do lote definindo a propriedade [BatchFlushInterval][BatchFlushInterval] antes de criar a fábrica de mensagens. Esta definição afeta todos os clientes que são criados por esta fábrica.

Para desativar o loteamento, detete a propriedade [BatchFlushInterval][BatchFlushInterval] para **TimeSpan.Zero**. Por exemplo:

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

O loteamento não afeta o número de operações de mensagens faturadas, e está disponível apenas para o protocolo de cliente do Service Bus utilizando a biblioteca [Microsoft.ServiceBus.Messaging.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) O protocolo HTTP não suporta o loteamento.

> [!NOTE]
> A `BatchFlushInterval` definição garante que o lote está implícito do ponto de vista da aplicação. ou é, e.; a aplicação faz `SendAsync` e `CompleteAsync` chama e não faz chamadas específicas do Lote.
>
> O lote explícito do lado do cliente pode ser implementado utilizando a chamada de método abaixo:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Neste caso, o tamanho combinado das mensagens deve ser inferior ao tamanho máximo suportado pelo nível de preços.

---

## <a name="batching-store-access"></a>Acesso à loja de loting

Para aumentar a produção de uma fila, tópico ou subscrição, o Service Bus emite várias mensagens quando escreve para a sua loja interna. Se ativado numa fila ou tópico, a escrita de mensagens na loja será lotada. Se ativado numa fila ou subscrição, a apagam-se as mensagens da loja. Se o acesso à loja em lotação estiver ativado para uma entidade, o Service Bus atrasa uma operação de escrita de loja relativamente a essa entidade até 20 ms.

> [!NOTE]
> Não existe o risco de perder mensagens com lotação, mesmo que haja uma falha no ônibus de serviço no final de um intervalo de loteamento de 20ms.

As operações adicionais de loja que ocorrem durante este intervalo são adicionadas ao lote. O acesso à loja lotada apenas afeta as operações **de envio** e **completa;** receber operações não são afetados. O acesso à loja lotada é uma propriedade de uma entidade. O lote ocorre em todas as entidades que permitem o acesso à loja em lotação.

Ao criar uma nova fila, tópico ou subscrição, o acesso à loja em lotação é ativado por padrão.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Para desativar o acesso à loja em `ManagementClient`lotação, necessitará de uma instância de . Crie uma fila a partir `EnableBatchedOperations` de `false`uma descrição da fila que define a propriedade para .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Para obter mais informações, consulte o seguinte:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Para desativar o acesso à loja em `NamespaceManager`lotação, necessitará de uma instância de . Crie uma fila a partir `EnableBatchedOperations` de `false`uma descrição da fila que define a propriedade para .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Para obter mais informações, consulte o seguinte:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

O acesso à loja em lotação não afeta o número de operações de mensagens faturadas, e é propriedade de uma fila, tópico ou subscrição. É independente do modo de receção e do protocolo que é utilizado entre um cliente e o serviço de ônibus de serviço.

## <a name="prefetching"></a>Prefetching

[A prebuscação](service-bus-prefetch.md) permite ao cliente de fila ou subscrição carregar mensagens adicionais do serviço quando executa uma operação de receção. O cliente guarda estas mensagens numa cache local. O tamanho da cache é `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` determinado pelas propriedades ou propriedades. Cada cliente que permite a pré-busca mantém a sua própria cache. Uma cache não é partilhada entre os clientes. Se o cliente iniciar uma operação de receção e a sua cache estiver vazia, o serviço transmite um lote de mensagens. O tamanho do lote é igual ao tamanho da cache ou 256 KB, o que for menor. Se o cliente iniciar uma operação de receção e a cache contiver uma mensagem, a mensagem é retirada da cache.

Quando uma mensagem é prefetchda, o serviço bloqueia a mensagem pré-rebuscada. Com o cadeado, a mensagem pré-rebuscada não pode ser recebida por um recetor diferente. Se o recetor não conseguir completar a mensagem antes de o bloqueio expirar, a mensagem fica disponível para outros recetores. A cópia prefetchda da mensagem permanece na cache. O recetor que consome a cópia em cache expirada receberá uma exceção quando tentar completar essa mensagem. Por predefinição, o bloqueio da mensagem expira após 60 segundos. Este valor pode ser alargado a 5 minutos. Para evitar o consumo de mensagens expiradas, o tamanho da cache deve ser sempre menor do que o número de mensagens que podem ser consumidas por um cliente dentro do intervalo de tempo de bloqueio.

Ao utilizar a expiração do bloqueio predefinido `PrefetchCount` de 60 segundos, um bom valor para é 20 vezes superior às taxas máximas de processamento de todos os recetores da fábrica. Por exemplo, uma fábrica cria três recetores, e cada recetor pode processar até 10 mensagens por segundo. A contagem de pré-fetch não deve exceder 20 X 3 X 10 = 600. Por predefinição, `PrefetchCount` está definido para 0, o que significa que não são recolhidas mensagens adicionais do serviço.

As mensagens prefetching aumentam o serviço global de uma fila ou subscrição porque reduz o número total de operações de mensagens, ou viagens de ida e volta. Buscar a primeira mensagem, no entanto, demorará mais tempo (devido ao aumento do tamanho da mensagem). Receber mensagens pré-rebuscadas será mais rápido porque estas mensagens já foram descarregadas pelo cliente.

A propriedade time-to-live (TTL) de uma mensagem é verificada pelo servidor no momento em que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem pode ser recebida mesmo que a mensagem tenha passado enquanto a mensagem foi emcachepelo cliente.

A prebuscação não afeta o número de operações de mensagens faturadas, e está disponível apenas para o protocolo de cliente do Service Bus. O protocolo HTTP não suporta a prebuscação. A prefetching está disponível para operações de receção sincronizadas e assíncronas.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Para mais informações, `PrefetchCount` consulte as seguintes propriedades:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Para mais informações, `PrefetchCount` consulte as seguintes propriedades:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Prefetching e ReceiveBatch

> [!NOTE]
> Esta secção aplica-se apenas ao WindowsAzure.ServiceBus SDK, uma vez que o Microsoft.Azure.ServiceBus SDK não expõe as funções do lote.

Embora os conceitos de pré-rebuscamento de várias mensagens`ReceiveBatch`em conjunto tenham semântica semelhante ao processamento de mensagens num lote , existem algumas pequenas diferenças que devem ser mantidas em mente ao alavancar em conjunto.

Prefetch é uma configuração (ou`QueueClient` modo) no cliente ( e `SubscriptionClient`) e `ReceiveBatch` é uma operação (que tem semântica de resposta a pedido).

Ao usá-los juntos, considere os seguintes casos -

* A prefetch deve ser maior ou igual ao número `ReceiveBatch`de mensagens que espera receber de .
* A prefetch pode ser até n/3 vezes o número de mensagens processadas por segundo, onde n é a duração padrão do bloqueio.

Existem alguns desafios em ter uma abordagem gananciosa (isto é, manter a contagem de pré-fetch muito alta), porque implica que a mensagem está bloqueada a um determinado recetor. A recomendação é experimentar valores de prefetch entre os limiares acima mencionados e identificar empiricamente o que se encaixa.

## <a name="multiple-queues"></a>Múltiplas filas

Se a carga esperada não puder ser tratada por uma única fila ou tópico, deve utilizar várias entidades de mensagens. Ao utilizar várias entidades, crie um cliente dedicado para cada entidade, em vez de utilizar o mesmo cliente para todas as entidades.

## <a name="development-and-testing-features"></a>Funcionalidades de desenvolvimento e teste

> [!NOTE]
> Esta secção aplica-se apenas ao WindowsAzure.ServiceBus SDK, uma vez que o Microsoft.Azure.ServiceBus SDK não expõe esta funcionalidade.

O Service Bus tem uma funcionalidade, utilizada especificamente para o desenvolvimento, que **nunca deve ser utilizada em configurações**de produção: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering].

Quando forem adicionadas novas regras ou filtros ao tópico, pode utilizar [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] para verificar se a nova expressão do filtro está a funcionar como esperado.

## <a name="scenarios"></a>Cenários

As seguintes secções descrevem cenários típicos de mensagens e delineiam as definições de ônibus de serviço preferidos. As taxas de entrada são classificadas como pequenas (menos de 1 mensagem/segundo), moderadas (1 mensagem/segundo ou superior, mas inferiores a 100 mensagens/segundo) e elevadas (100 mensagens/segundo ou superior). O número de clientes é classificado como pequeno (5 ou menos), moderado (mais de 5 mas menos ou igual a 20) e grande (mais de 20).

### <a name="high-throughput-queue"></a>Fila de alta-adesão

Objetivo: Maximizar a entrada de uma única fila. O número de remetentes e recetores é pequeno.

* Para aumentar a taxa de envio global para a fila, utilize várias fábricas de mensagens para criar remetentes. Para cada remetente, utilize operações assíncronas ou fios múltiplos.
* Para aumentar a taxa de receção global da fila, utilize várias fábricas de mensagens para criar recetores.
* Utilize operações assíncronas para aproveitar o lote do lado do cliente.
* Detete o intervalo de loteamento para 50 ms para reduzir o número de transmissões de protocolo sinuosos do cliente do Service Bus. Se forem utilizados vários remetentes, aumente o intervalo de loteamento para 100 ms.
* Deixe o acesso à loja em lote ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas na fila.
* Detete a contagem de pré-fetch para 20 vezes as taxas máximas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolos de clientes do Serviço Bus.

### <a name="multiple-high-throughput-queues"></a>Múltiplas filas de alta-utilização

Objetivo: Maximizar a entrada geral de várias filas. A entrada de uma fila individual é moderada ou alta.

Para obter a máxima entrada em várias filas, utilize as definições descritas para maximizar a entrada de uma única fila. Além disso, utilize diferentes fábricas para criar clientes que enviem ou recebam de diferentes filas.

### <a name="low-latency-queue"></a>Fila de baixa latência

Objetivo: Minimizar a latência de ponta a ponta de uma fila ou tópico. O número de remetentes e recetores é pequeno. A entrada da fila é pequena ou moderada.

* Desative o lote do lado do cliente. O cliente envia imediatamente uma mensagem.
* Desativar o acesso à loja em lotação. O serviço escreve imediatamente a mensagem para a loja.
* Se utilizar um único cliente, detete a contagem de pré-fetch para 20 vezes a taxa de processamento do recetor. Se várias mensagens chegarem à fila ao mesmo tempo, o protocolo do cliente do Service Bus transmite-as todas ao mesmo tempo. Quando o cliente recebe a próxima mensagem, essa mensagem já está na cache local. A cache deve ser pequena.
* Se utilizar vários clientes, detete a contagem de pré-fetch para 0. Ao definir a contagem, o segundo cliente pode receber a segunda mensagem enquanto o primeiro cliente ainda está a processar a primeira mensagem.

### <a name="queue-with-a-large-number-of-senders"></a>Fila com um grande número de remetentes

Objetivo: Maximize a entrada de uma fila ou tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa moderada. O número de recetores é pequeno.

O Ônibus de Serviço permite até 1000 ligações simultâneas a uma entidade de mensagens (ou 5000 usando AMQP). Este limite é aplicado ao nível do espaço de nome, e as filas/tópicos/subscrições são limitadas pelo limite de ligações simultâneas por espaço de nome. Para as filas, este número é partilhado entre remetentes e recetores. Se todas as 1000 ligações forem necessárias para os remetentes, substitua a fila por um tópico e uma única subscrição. Um tópico aceita até 1000 ligações simultâneas de remetentes, enquanto a subscrição aceita mais 1000 ligações simultâneas dos recetores. Se forem necessários mais de 1000 remetentes simultâneos, os remetentes devem enviar mensagens para o protocolo service bus via HTTP.

Para maximizar a entrada, execute os seguintes passos:

* Se cada remetente residir num processo diferente, utilize apenas uma única fábrica por processo.
* Utilize operações assíncronas para aproveitar o lote do lado do cliente.
* Utilize o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo sinuosos do cliente do Service Bus.
* Deixe o acesso à loja em lote ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas na fila ou tópico.
* Detete a contagem de pré-fetch para 20 vezes as taxas máximas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolos de clientes do Serviço Bus.

### <a name="queue-with-a-large-number-of-receivers"></a>Fila com um grande número de recetores

Objetivo: Maximizar a taxa de receção de uma fila ou subscrição com um grande número de recetores. Cada recetor recebe mensagens a um ritmo moderado. O número de remetentes é pequeno.

O Service Bus permite até 1000 ligações simultâneas a uma entidade. Se uma fila necessitar de mais de 1000 recetores, substitua a fila por um tópico e várias subscrições. Cada subscrição pode suportar até 1000 ligações simultâneas. Em alternativa, os recetores podem aceder à fila através do protocolo HTTP.

Para maximizar a entrada, faça o seguinte:

* Se cada recetor residir num processo diferente, utilize apenas uma única fábrica por processo.
* Os recetores podem utilizar operações sincronizadas ou assíncronas. Dada a taxa moderada de receção de um recetor individual, o loteamento do lado do cliente de um pedido Completo não afeta a produção do recetor.
* Deixe o acesso à loja em lote ativado. Este acesso reduz a carga global da entidade. Também reduz a taxa global a que as mensagens podem ser escritas na fila ou no tópico.
* Defino a contagem de pré-fetch para um pequeno valor (por exemplo, PrefetchCount = 10). Esta contagem impede que os recetores não se otenham enquanto outros recetores têm um grande número de mensagens em cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tópico com um pequeno número de assinaturas

Objetivo: Maximizar a entrada de um tópico com um pequeno número de subscrições. Uma mensagem é recebida por muitas subscrições, o que significa que a taxa de receção combinada sobre todas as subscrições é maior do que a taxa de envio. O número de remetentes é pequeno. O número de recetores por subscrição é pequeno.

Para maximizar a entrada, faça o seguinte:

* Para aumentar a taxa de envio global para o tópico, use várias fábricas de mensagens para criar remetentes. Para cada remetente, utilize operações assíncronas ou fios múltiplos.
* Para aumentar a taxa de receção global de uma subscrição, utilize várias fábricas de mensagens para criar recetores. Para cada recetor, utilize operações assíncronas ou fios múltiplos.
* Utilize operações assíncronas para aproveitar o lote do lado do cliente.
* Utilize o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo sinuosos do cliente do Service Bus.
* Deixe o acesso à loja em lote ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas no tópico.
* Detete a contagem de pré-fetch para 20 vezes as taxas máximas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolos de clientes do Serviço Bus.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tópico com um grande número de subscrições

Objetivo: Maximizar a entrada de um tópico com um grande número de subscrições. Uma mensagem é recebida por muitas subscrições, o que significa que a taxa de receção combinada sobre todas as subscrições é muito maior do que a taxa de envio. O número de remetentes é pequeno. O número de recetores por subscrição é pequeno.

Tópicos com um grande número de subscrições normalmente expõem um baixo resultado geral se todas as mensagens forem encaminhadas para todas as subscrições. Esta baixa produção é causada pelo facto de cada mensagem ser recebida muitas vezes, e todas as mensagens que estão contidas num tópico e todas as suas subscrições são armazenadas na mesma loja. Presume-se que o número de remetentes e o número de recetores por subscrição são reduzidos. O Service Bus suporta até 2.000 subscrições por tópico.

Para maximizar a entrada, experimente os seguintes passos:

* Utilize operações assíncronas para aproveitar o lote do lado do cliente.
* Utilize o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo sinuosos do cliente do Service Bus.
* Deixe o acesso à loja em lote ativado. Este acesso aumenta a taxa global a que as mensagens podem ser escritas no tópico.
* Detete a contagem de pré-fetch para 20 vezes a taxa de receção esperada em segundos. Esta contagem reduz o número de transmissões de protocolos de clientes do Serviço Bus.

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