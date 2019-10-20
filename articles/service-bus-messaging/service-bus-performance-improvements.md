---
title: Práticas recomendadas para melhorar o desempenho usando o barramento de serviço do Azure | Microsoft Docs
description: Descreve como usar o barramento de serviço para otimizar o desempenho ao trocar mensagens orientadas.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 3d2d26e8cb8a3b1ee7720424aea701ca063ecc9f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596464"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens do barramento de serviço

Este artigo descreve como usar o barramento de serviço do Azure para otimizar o desempenho ao trocar mensagens orientadas. A primeira parte deste artigo descreve os diferentes mecanismos que são oferecidos para ajudar a aumentar o desempenho. A segunda parte fornece orientação sobre como usar o barramento de serviço de uma forma que possa oferecer o melhor desempenho em um determinado cenário.

Ao longo deste artigo, o termo "cliente" refere-se a qualquer entidade que acessa o barramento de serviço. Um cliente pode assumir a função de um remetente ou de um destinatário. O termo "remetente" é usado para um cliente de tópico ou fila do barramento de serviço que envia mensagens para uma assinatura de tópico ou fila do barramento de serviço. O termo "receptor" refere-se a um cliente de assinatura ou fila do barramento de serviço que recebe mensagens de uma fila ou assinatura do barramento de serviço.

Essas seções introduzem vários conceitos que o barramento de serviço usa para ajudar a melhorar o desempenho.

## <a name="protocols"></a>Protocolos

O barramento de serviço permite que os clientes enviem e recebam mensagens por meio de um dos três protocolos:

1. Advanced Message Queuing Protocol (AMQP)
2. Protocolo de mensagens do barramento de serviço (SBMP)
3. HTTP

AMQP e SBMP são mais eficientes, pois mantêm a conexão com o barramento de serviço, desde que a fábrica de mensagens exista. Ele também implementa o envio em lote e a pré-busca. A menos que seja explicitamente mencionado, todo o conteúdo deste artigo pressupõe o uso de AMQP ou SBMP.

## <a name="reusing-factories-and-clients"></a>Reutilizando fábricas e clientes

Os objetos de cliente do barramento de serviço, como [QueueClient][QueueClient] ou [MessageSender][MessageSender], são criados por meio de um objeto [MessagingFactory][MessagingFactory] , que também fornece gerenciamento interno de conexões. É recomendável que você não feche fábricas de mensagens ou de fila, tópico e clientes de assinatura depois de enviar uma mensagem e, em seguida, recriá-las ao enviar a próxima mensagem. Fechar uma fábrica de mensagens exclui a conexão com o serviço do barramento de serviço e uma nova conexão é estabelecida ao recriar a fábrica. Estabelecer uma conexão é uma operação cara que você pode evitar reutilizando a mesma fábrica e objetos de cliente para várias operações. Você pode usar esses objetos de cliente com segurança para operações assíncronas simultâneas e de vários threads. 

## <a name="concurrent-operations"></a>Operações simultâneas

Executar uma operação (enviar, receber, excluir, etc.) leva algum tempo. Esse tempo inclui o processamento da operação pelo serviço do barramento de serviço, além da latência da solicitação e da resposta. Para aumentar o número de operações por tempo, as operações devem ser executadas simultaneamente. 

O cliente agenda operações simultâneas executando operações assíncronas. A próxima solicitação é iniciada antes de a solicitação anterior ser concluída. O trecho de código a seguir é um exemplo de uma operação de envio assíncrona:
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  O código a seguir é um exemplo de uma operação de recebimento assíncrono. Consulte o programa completo [aqui](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Modo de recebimento

Ao criar um cliente de fila ou de assinatura, você pode especificar um modo de recebimento: *Peek-Lock* ou *recebimento e exclusão*. O modo de recebimento padrão é [Peeklock][PeekLock]. Ao operar nesse modo, o cliente envia uma solicitação para receber uma mensagem do barramento de serviço. Depois que o cliente receber a mensagem, ele enviará uma solicitação para concluir a mensagem.

Ao definir o modo de recebimento como [ReceiveAndDelete][ReceiveAndDelete], ambas as etapas são combinadas em uma única solicitação. Essas etapas reduzem o número total de operações e podem melhorar a taxa de transferência geral da mensagem. Esse lucro de desempenho é o risco de perder mensagens.

O barramento de serviço não oferece suporte a transações para operações de recebimento e exclusão. Além disso, as semânticas de bloqueio de inspeção são necessárias para qualquer cenário no qual o cliente queira adiar ou [uma mensagem](service-bus-dead-letter-queues.md) .

## <a name="client-side-batching"></a>Envio em lote no lado do cliente

O envio em lote do lado do cliente permite que um cliente de fila ou de tópico adie a enviar uma mensagem por um determinado período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. O envio em lote do lado do cliente também faz com que um cliente de fila ou de assinatura execute em lote várias solicitações **completas** em uma única solicitação. O envio em lote só está disponível para operações **Send** e **Complete** assíncronas. As operações síncronas são enviadas imediatamente para o serviço do barramento de serviço. O envio em lote não ocorre para operações de Peek ou recebimento, nem o processamento em lote ocorre entre clientes.

Por padrão, um cliente usa um intervalo de lote de 20 ms. Você pode alterar o intervalo de lote definindo a propriedade [BatchFlushInterval][BatchFlushInterval] antes de criar a fábrica de mensagens. Essa configuração afeta todos os clientes criados por essa fábrica.

Para desabilitar o envio em lote, defina a propriedade [BatchFlushInterval][BatchFlushInterval] como **TimeSpan. zero**. Por exemplo:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

O envio em lote não afeta o número de operações de mensagens faturáveis e está disponível somente para o protocolo de cliente do barramento de serviço usando a biblioteca [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . O protocolo HTTP não oferece suporte ao envio em lote.

> [!NOTE]
> A definição de BatchFlushInterval garante que o envio em lote seja implícito da perspectiva do aplicativo. ou seja, o aplicativo faz chamadas SendAsync () e CompleteAsync () e não faz chamadas de lote específicas.
>
> O envio em lote explícito do cliente pode ser implementado utilizando a chamada de método abaixo- 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Aqui, o tamanho combinado das mensagens deve ser menor que o tamanho máximo suportado pelo tipo de preço.

## <a name="batching-store-access"></a>Acesso ao armazenamento em lotes

Para aumentar a taxa de transferência de uma fila, tópico ou assinatura, o barramento de serviço coloca várias mensagens em lotes ao gravar em seu repositório interno. Se habilitada em uma fila ou tópico, a gravação de mensagens no repositório será colocada em lote. Se habilitada em uma fila ou assinatura, a exclusão de mensagens do repositório será colocada em lote. Se o acesso ao repositório em lote estiver habilitado para uma entidade, o barramento de serviço atrasará uma operação de gravação de armazenamento em relação a essa entidade em até 20 ms. 

> [!NOTE]
> Não há risco de perder mensagens com o envio em lote, mesmo se houver uma falha do barramento de serviço ao final de um intervalo de envio em lote de 20 ms. 

Operações de armazenamento adicionais que ocorrem durante esse intervalo são adicionadas ao lote. O acesso ao repositório em lote afeta apenas as operações **Enviar** e **concluir** ; as operações de recebimento não são afetadas. O acesso ao repositório em lote é uma propriedade em uma entidade. O envio em lote ocorre em todas as entidades que habilitam o acesso ao repositório em lote.

Ao criar uma nova fila, tópico ou assinatura, o acesso ao repositório em lote é habilitado por padrão. Para desabilitar o acesso ao repositório em lote, defina a propriedade [EnableBatchedOperations][EnableBatchedOperations] como **false** antes de criar a entidade. Por exemplo:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

O acesso ao repositório em lote não afeta o número de operações de mensagens faturáveis e é uma propriedade de uma fila, tópico ou assinatura. Ele é independente do modo de recebimento e do protocolo usado entre um cliente e o serviço do barramento de serviço.

## <a name="prefetching"></a>Pré-busca

A [pré-busca](service-bus-prefetch.md) permite que o cliente de fila ou assinatura carregue mensagens adicionais do serviço ao executar uma operação de recebimento. O cliente armazena essas mensagens em um cache local. O tamanho do cache é determinado pelas propriedades [QueueClient. PrefetchCount][QueueClient.PrefetchCount] ou [SubscriptionClient. PrefetchCount][SubscriptionClient.PrefetchCount] . Cada cliente que permite a pré-busca mantém seu próprio cache. Um cache não é compartilhado entre clientes. Se o cliente iniciar uma operação de recebimento e seu cache estiver vazio, o serviço transmitirá um lote de mensagens. O tamanho do lote é igual ao tamanho do cache ou 256 KB, o que for menor. Se o cliente iniciar uma operação de recebimento e o cache contiver uma mensagem, a mensagem será retirada do cache.

Quando uma mensagem é pré-busca, o serviço bloqueia a mensagem de pré-busca. Com o bloqueio, a mensagem de pré-busca não pode ser recebida por um destinatário diferente. Se o receptor não puder concluir a mensagem antes de o bloqueio expirar, a mensagem ficará disponível para outros destinatários. A cópia de pré-busca da mensagem permanece no cache. O receptor que consome a cópia armazenada em cache expirada receberá uma exceção quando tentar concluir essa mensagem. Por padrão, o bloqueio de mensagem expira após 60 segundos. Esse valor pode ser estendido para 5 minutos. Para evitar o consumo de mensagens expiradas, o tamanho do cache sempre deve ser menor do que o número de mensagens que podem ser consumidas por um cliente dentro do intervalo de tempo limite de bloqueio.

Ao usar a expiração de bloqueio padrão de 60 segundos, um bom valor para [PrefetchCount][SubscriptionClient.PrefetchCount] é de 20 vezes as taxas máximas de processamento de todos os receptores da fábrica. Por exemplo, uma fábrica cria três receptores, e cada receptor pode processar até 10 mensagens por segundo. A contagem de pré-busca não deve exceder 20 X 3 X 10 = 600. Por padrão, [PrefetchCount][QueueClient.PrefetchCount] é definido como 0, o que significa que nenhuma mensagem adicional é buscada do serviço.

A pré-busca de mensagens aumenta a taxa de transferência geral de uma fila ou assinatura, pois reduz o número total de operações de mensagens ou viagens de ida e volta. No entanto, buscar a primeira mensagem levará mais tempo (devido ao maior tamanho da mensagem). O recebimento de mensagens de pré-busca será mais rápido porque essas mensagens já foram baixadas pelo cliente.

A propriedade TTL (vida útil) de uma mensagem é verificada pelo servidor no momento em que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem pode ser recebida mesmo que a TTL da mensagem tenha passado enquanto a mensagem fosse armazenada em cache pelo cliente.

A pré-busca não afeta o número de operações de mensagens faturáveis e está disponível somente para o protocolo de cliente do barramento de serviço. O protocolo HTTP não oferece suporte à pré-busca. A pré-busca está disponível para operações de recebimento síncronas e assíncronas.

## <a name="prefetching-and-receivebatch"></a>Pré-busca e ReceiveBatch

Embora os conceitos de pré-busca de várias mensagens tenham uma semântica semelhante ao processamento de mensagens em um lote (ReceiveBatch), há algumas pequenas diferenças que devem ser mantidas em mente ao aproveitá-las juntas.

Pré-busca é uma configuração (ou modo) no cliente (QueueClient e SubscriptionClient) e ReceiveBatch é uma operação (que tem semântica de solicitação-resposta).

Ao usá-los juntos, considere os seguintes casos:

* A pré-busca deve ser maior ou igual ao número de mensagens que você espera receber de ReceiveBatch.
* A pré-busca pode ser até n/3 vezes o número de mensagens processadas por segundo, em que n é a duração de bloqueio padrão.

Há alguns desafios com uma abordagem aproximada (ou seja, manter a contagem de pré-busca muito alta), porque isso implica que a mensagem está bloqueada para um receptor específico. A recomendação é testar os valores de prefetch entre os limites mencionados acima e empiricamente identificar o que se ajusta.

## <a name="multiple-queues"></a>Várias filas

Se a carga esperada não puder ser tratada por uma única fila ou tópico, você deverá usar várias entidades de mensagens. Ao usar várias entidades, crie um cliente dedicado para cada entidade, em vez de usar o mesmo cliente para todas as entidades.

## <a name="development-and-testing-features"></a>Recursos de desenvolvimento e teste

O barramento de serviço tem um recurso, usado especificamente para desenvolvimento, que **nunca deve ser usado em configurações de produção**: [TopicDescription. EnableFilteringMessagesBeforePublishing][].

Quando novas regras ou filtros são adicionados ao tópico, você pode usar [TopicDescription. EnableFilteringMessagesBeforePublishing][] para verificar se a nova expressão de filtro está funcionando conforme o esperado.

## <a name="scenarios"></a>Cenários

As seções a seguir descrevem cenários típicos de mensagens e descrevem as configurações de barramento de serviço preferenciais. As taxas de taxa de transferência são classificadas como pequenas (menos de 1 mensagem/segundo), moderadas (1 mensagem/segundo ou maior, mas menos de 100 mensagens/segundo) e alta (100 mensagens/segundo ou superior). O número de clientes é classificado como pequeno (5 ou menos), moderado (mais de 5, mas menor ou igual a 20) e grande (mais de 20).

### <a name="high-throughput-queue"></a>Fila de alta taxa de transferência

Meta: maximize a taxa de transferência de uma única fila. O número de remetentes e receptores é pequeno.

* Para aumentar a taxa geral de envio para a fila, use várias fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.
* Para aumentar a taxa geral de recebimento da fila, use várias fábricas de mensagens para criar destinatários.
* Use operações assíncronas para aproveitar o envio em lote do lado do cliente.
* Defina o intervalo de envio em lote para 50 ms para reduzir o número de transmissões de protocolo de cliente do barramento de serviço. Se vários remetentes forem usados, aumente o intervalo de envio em lote para 100 ms.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas na fila.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do barramento de serviço.

### <a name="multiple-high-throughput-queues"></a>Várias filas de alta taxa de transferência

Meta: maximizar a taxa de transferência geral de várias filas. A taxa de transferência de uma fila individual é moderada ou alta.

Para obter a taxa de transferência máxima em várias filas, use as configurações descritas para maximizar a taxa de transferência de uma única fila. Além disso, use fábricas diferentes para criar clientes que enviam ou recebem de filas diferentes.

### <a name="low-latency-queue"></a>Fila de baixa latência

Meta: minimizar a latência de ponta a ponta de uma fila ou um tópico. O número de remetentes e receptores é pequeno. A taxa de transferência da fila é pequena ou moderada.

* Desabilite o envio em lote no lado do cliente. O cliente envia imediatamente uma mensagem.
* Desabilite o acesso ao repositório em lote. O serviço grava imediatamente a mensagem no repositório.
* Se estiver usando um único cliente, defina a contagem de pré-busca como 20 vezes a taxa de processamento do receptor. Se várias mensagens chegam na fila ao mesmo tempo, o protocolo de cliente do barramento de serviço transmite todas elas ao mesmo tempo. Quando o cliente recebe a próxima mensagem, essa mensagem já está no cache local. O cache deve ser pequeno.
* Se estiver usando vários clientes, defina a contagem de pré-busca como 0. Ao definir a contagem, o segundo cliente pode receber a segunda mensagem enquanto o primeiro cliente ainda está processando a primeira mensagem.

### <a name="queue-with-a-large-number-of-senders"></a>Fila com um grande número de remetentes

Meta: maximizar a taxa de transferência de uma fila ou tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa moderada. O número de receptores é pequeno.

O barramento de serviço permite até 1000 conexões simultâneas com uma entidade de mensagens (ou 5000 usando AMQP). Esse limite é imposto no nível de namespace, e filas/tópicos/assinaturas são limitados pelo limite de conexões simultâneas por namespace. Para filas, esse número é compartilhado entre remetentes e destinatários. Se todas as conexões 1000 forem necessárias para os remetentes, substitua a fila por um tópico e uma única assinatura. Um tópico aceita até 1000 conexões simultâneas de remetentes, enquanto a assinatura aceita um adicional 1000 conexões simultâneas de destinatários. Se mais de 1000 remetentes simultâneos forem necessários, os remetentes deverão enviar mensagens para o protocolo do barramento de serviço via HTTP.

Para maximizar a taxa de transferência, execute as seguintes etapas:

* Se cada remetente residir em um processo diferente, use apenas uma única fábrica por processo.
* Use operações assíncronas para aproveitar o envio em lote do lado do cliente.
* Use o intervalo de envio em lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do barramento de serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do barramento de serviço.

### <a name="queue-with-a-large-number-of-receivers"></a>Fila com um grande número de destinatários

Meta: maximize a taxa de recebimento de uma fila ou assinatura com um grande número de destinatários. Cada destinatário recebe mensagens a uma taxa moderada. O número de remetentes é pequeno.

O barramento de serviço permite até 1000 conexões simultâneas com uma entidade. Se uma fila exigir mais de 1000 destinatários, substitua a fila por um tópico e várias assinaturas. Cada assinatura pode dar suporte a até 1000 conexões simultâneas. Como alternativa, os receptores podem acessar a fila por meio do protocolo HTTP.

Para maximizar a taxa de transferência, faça o seguinte:

* Se cada receptor residir em um processo diferente, use apenas uma única fábrica por processo.
* Os receptores podem usar operações síncronas ou assíncronas. Dada a taxa de recebimento moderada de um receptor individual, o envio em lote do lado do cliente de uma solicitação completa não afeta a taxa de transferência do receptor.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso reduz a carga geral da entidade. Ele também reduz a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.
* Defina a contagem de pré-busca como um valor pequeno (por exemplo, PrefetchCount = 10). Essa contagem impede que os receptores estejam ociosos enquanto outros receptores têm grandes quantidades de mensagens armazenadas em cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tópico com um pequeno número de assinaturas

Meta: maximize a taxa de transferência de um tópico com um pequeno número de assinaturas. Uma mensagem é recebida por muitas assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Para maximizar a taxa de transferência, faça o seguinte:

* Para aumentar a taxa geral de envio para o tópico, use várias fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.
* Para aumentar a taxa geral de recebimento de uma assinatura, use várias fábricas de mensagens para criar destinatários. Para cada destinatário, use operações assíncronas ou vários threads.
* Use operações assíncronas para aproveitar o envio em lote do lado do cliente.
* Use o intervalo de envio em lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do barramento de serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do barramento de serviço.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tópico com um grande número de assinaturas

Meta: maximize a taxa de transferência de um tópico com um grande número de assinaturas. Uma mensagem é recebida por muitas assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é muito maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Os tópicos com um grande número de assinaturas normalmente expõem uma taxa de transferência geral baixa se todas as mensagens forem roteadas para todas as assinaturas. Essa baixa taxa de transferência é causada pelo fato de que cada mensagem é recebida muitas vezes e todas as mensagens contidas em um tópico e todas as suas assinaturas são armazenadas no mesmo armazenamento. Supõe-se que o número de remetentes e o número de destinatários por assinatura seja pequeno. O barramento de serviço dá suporte a até 2.000 assinaturas por tópico.

Para maximizar a taxa de transferência, tente as seguintes etapas:

* Use operações assíncronas para aproveitar o envio em lote do lado do cliente.
* Use o intervalo de envio em lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do barramento de serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.
* Defina a contagem de pré-busca como 20 vezes a taxa de recebimento esperada em segundos. Essa contagem reduz o número de transmissões de protocolo de cliente do barramento de serviço.

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription. EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
