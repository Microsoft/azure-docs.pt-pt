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
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 683a28ca3cdabd5a7ffbf6e9ffdc3ed0c58d3247
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370484"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Boas Práticas para melhorias de desempenho usando mensagens de ônibus de serviço

Este artigo descreve como usar o Azure Service Bus para otimizar o desempenho ao trocar mensagens intermediadas. A primeira parte deste artigo descreve os diferentes mecanismos que são oferecidos para ajudar a aumentar o desempenho. A segunda parte fornece orientações sobre como usar o Service Bus de uma forma que pode oferecer o melhor desempenho num determinado cenário.

Ao longo deste artigo, o termo "cliente" refere-se a qualquer entidade que aceda ao Service Bus. Um cliente pode assumir o papel de remetente ou recetor. O termo "remetente" é usado para uma fila de ônibus de serviço ou cliente tópico que envia mensagens para uma fila de ônibus de serviço ou subscrição de tópicos. O termo "recetor" refere-se a uma fila de ônibus de serviço ou cliente de subscrição que recebe mensagens de uma fila de ônibus de serviço ou subscrição.

Estas secções introduzem vários conceitos que o Service Bus usa para ajudar a impulsionar o desempenho.

## <a name="protocols"></a>Protocolos

O Service Bus permite que os clientes enviem e recebam mensagens através de um dos três protocolos:

1. Protocolo avançado de fila de mensagens (AMQP)
2. Protocolo de Mensagens de Autocarro de Serviço (SBMP)
3. HTTP

A AMQP e a SBMP são mais eficientes, pois mantêm a ligação ao Service Bus enquanto a fábrica de mensagens existir. Também implementa lotação e prebuscação. Salvo indicação explícita, todos os conteúdos deste artigo assumem a utilização de AMQP ou SBMP.

## <a name="reusing-factories-and-clients"></a>Reutilização de fábricas e clientes

Os objetos de clientes de ônibus de serviço, como [QueueClient][QueueClient] ou [MessageSender,][MessageSender]são criados através de um objeto de [MessagingFactory,][MessagingFactory] que também fornece gestão interna de ligações. Recomenda-se que não feche fábricas de mensagens ou clientes de fila, tópico e subscrição depois de enviar uma mensagem e, em seguida, recriá-las quando envia a próxima mensagem. O encerramento de uma fábrica de mensagens elimina a ligação ao serviço service bus, e uma nova ligação é estabelecida ao recriar a fábrica. Estabelecer uma ligação é uma operação dispendiosa que pode evitar reutilizando a mesma fábrica e objetos de cliente para múltiplas operações. Pode utilizar com segurança estes objetos de cliente para operações assíncronas simultâneas e a partir de vários fios. 

## <a name="concurrent-operations"></a>Operações simultâneas

Realizar uma operação (enviar, receber, excluir, etc.) leva algum tempo. Desta vez inclui o processamento da operação pelo serviço de autocarros de serviço, para além da latência do pedido e da resposta. Para aumentar o número de operações por tempo, as operações devem ser executadas simultaneamente. 

O cliente programa operações simultâneas através da realização de operações assíncronas. O próximo pedido é iniciado antes de o pedido anterior estar concluído. O seguinte código de corte é um exemplo de uma operação de envio assíncrono:
  
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
  
  O código que se segue é um exemplo de uma operação de receção assíncrona. Consulte o programa completo [aqui:](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues)
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Receber modo

Ao criar uma fila ou cliente de subscrição, pode especificar um modo de receção: *Peek-lock* ou *Receive and Delete*. O modo de receção predefinido é [PeekLock][PeekLock]. Ao operar neste modo, o cliente envia um pedido para receber uma mensagem da Service Bus. Depois de o cliente ter recebido a mensagem, envia um pedido para completar a mensagem.

Ao definir o modo de receção para [Receber E Apagar,][ReceiveAndDelete]ambos os passos são combinados num único pedido. Estes passos reduzem o número total de operações e podem melhorar o resultado global da mensagem. Este ganho de desempenho surge com o risco de perder mensagens.

O Service Bus não suporta transações para operações de receção e eliminação. Além disso, são necessárias semânticas de bloqueio de espreitar para quaisquer cenários em que o cliente queira adiar ou enviar uma mensagem de [carta morta.](service-bus-dead-letter-queues.md)

## <a name="client-side-batching"></a>Lote do lado do cliente

O lote do lado do cliente permite que um cliente de fila ou tópico atrase o envio de uma mensagem por um determinado período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. O lote do lado do cliente também faz com que um cliente de fila ou subscrição emlota vários pedidos **completos** num único pedido. O lote só está disponível para operações assíncronas **de envio** e **completa.** As operações sincronizadas são imediatamente enviadas para o serviço de ônibus de serviço. O loteamento não ocorre para espreitar ou receber operações, nem o loting ocorre entre os clientes.

Por padrão, um cliente usa um intervalo de lote de 20 ms. Pode alterar o intervalo do lote definindo a propriedade [BatchFlushInterval][BatchFlushInterval] antes de criar a fábrica de mensagens. Esta definição afeta todos os clientes que são criados por esta fábrica.

Para desativar o loteamento, detete a propriedade [BatchFlushInterval][BatchFlushInterval] para **TimeSpan.Zero**. Por exemplo:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

O loteamento não afeta o número de operações de mensagens faturadas, e está disponível apenas para o protocolo de cliente do Service Bus utilizando a biblioteca [Microsoft.ServiceBus.Messaging.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) O protocolo HTTP não suporta o loteamento.

> [!NOTE]
> A definição do BatchFlushInterval garante que o loteamento está implícito do ponto de vista da aplicação. ou é, a aplicação faz chamadas SendAsync() e CompleteAsync() e não faz chamadas específicas do Lote.
>
> O lote explícito do lado do cliente pode ser implementado utilizando a chamada de método abaixo - 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Neste caso, o tamanho combinado das mensagens deve ser inferior ao tamanho máximo suportado pelo nível de preços.

## <a name="batching-store-access"></a>Acesso à loja de loting

Para aumentar a produção de uma fila, tópico ou subscrição, o Service Bus emite várias mensagens quando escreve para a sua loja interna. Se ativado numa fila ou tópico, a escrita de mensagens na loja será lotada. Se ativado numa fila ou subscrição, a apagam-se as mensagens da loja. Se o acesso à loja em lotação estiver ativado para uma entidade, o Service Bus atrasa uma operação de escrita de loja relativamente a essa entidade até 20 ms. 

> [!NOTE]
> Não existe o risco de perder mensagens com lotação, mesmo que haja uma falha no ônibus de serviço no final de um intervalo de loteamento de 20ms. 

As operações adicionais de loja que ocorrem durante este intervalo são adicionadas ao lote. O acesso à loja lotada apenas afeta as operações **de envio** e **completa;** receber operações não são afetados. O acesso à loja lotada é uma propriedade de uma entidade. O lote ocorre em todas as entidades que permitem o acesso à loja em lotação.

Ao criar uma nova fila, tópico ou subscrição, o acesso à loja em lotação é ativado por padrão. Para desativar o acesso à loja em lotação, detete tea propriedade [EnableBatchedOperations][EnableBatchedOperations] como **falsa** antes de criar a entidade. Por exemplo:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

O acesso à loja em lotação não afeta o número de operações de mensagens faturadas, e é propriedade de uma fila, tópico ou subscrição. É independente do modo de receção e do protocolo que é utilizado entre um cliente e o serviço de ônibus de serviço.

## <a name="prefetching"></a>Prefetching

[A prebuscação](service-bus-prefetch.md) permite ao cliente de fila ou subscrição carregar mensagens adicionais do serviço quando executa uma operação de receção. O cliente guarda estas mensagens numa cache local. O tamanho da cache é determinado pelas propriedades [QueueClient.PrefetchCount][QueueClient.PrefetchCount] ou [SubscriptionClient.PrefetchCount.][SubscriptionClient.PrefetchCount] Cada cliente que permite a pré-busca mantém a sua própria cache. Uma cache não é partilhada entre os clientes. Se o cliente iniciar uma operação de receção e a sua cache estiver vazia, o serviço transmite um lote de mensagens. O tamanho do lote é igual ao tamanho da cache ou 256 KB, o que for menor. Se o cliente iniciar uma operação de receção e a cache contiver uma mensagem, a mensagem é retirada da cache.

Quando uma mensagem é prefetchda, o serviço bloqueia a mensagem pré-rebuscada. Com o cadeado, a mensagem pré-rebuscada não pode ser recebida por um recetor diferente. Se o recetor não conseguir completar a mensagem antes de o bloqueio expirar, a mensagem fica disponível para outros recetores. A cópia prefetchda da mensagem permanece na cache. O recetor que consome a cópia em cache expirada receberá uma exceção quando tentar completar essa mensagem. Por predefinição, o bloqueio da mensagem expira após 60 segundos. Este valor pode ser alargado a 5 minutos. Para evitar o consumo de mensagens expiradas, o tamanho da cache deve ser sempre menor do que o número de mensagens que podem ser consumidas por um cliente dentro do intervalo de tempo de bloqueio.

Ao utilizar a expiração do bloqueio predefinido de 60 segundos, um bom valor para o [PrefetchCount][SubscriptionClient.PrefetchCount] é 20 vezes superior às taxas máximas de processamento de todos os recetores da fábrica. Por exemplo, uma fábrica cria três recetores, e cada recetor pode processar até 10 mensagens por segundo. A contagem de pré-fetch não deve exceder 20 X 3 X 10 = 600. Por predefinição, o [PrefetchCount][QueueClient.PrefetchCount] está definido para 0, o que significa que não são recolhidas mensagens adicionais do serviço.

As mensagens prefetching aumentam o serviço global de uma fila ou subscrição porque reduz o número total de operações de mensagens, ou viagens de ida e volta. Buscar a primeira mensagem, no entanto, demorará mais tempo (devido ao aumento do tamanho da mensagem). Receber mensagens pré-rebuscadas será mais rápido porque estas mensagens já foram descarregadas pelo cliente.

A propriedade time-to-live (TTL) de uma mensagem é verificada pelo servidor no momento em que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem pode ser recebida mesmo que a mensagem tenha passado enquanto a mensagem foi emcachepelo cliente.

A prebuscação não afeta o número de operações de mensagens faturadas, e está disponível apenas para o protocolo de cliente do Service Bus. O protocolo HTTP não suporta a prebuscação. A prefetching está disponível para operações de receção sincronizadas e assíncronas.

## <a name="prefetching-and-receivebatch"></a>Prefetching e ReceiveBatch

Embora os conceitos de pré-rebuscamento de várias mensagens em conjunto tenham semântica semelhante ao processamento de mensagens num lote (ReceiveBatch), existem algumas pequenas diferenças que devem ser mantidas em mente ao alavancar em conjunto.

Prefetch é uma configuração (ou modo) no cliente (QueueClient e SubscriptionClient) e ReceiveBatch é uma operação (que tem semântica de resposta a pedidos).

Ao usá-los juntos, considere os seguintes casos -

* A prefetch deve ser maior ou igual ao número de mensagens que espera receber do ReceiveBatch.
* A prefetch pode ser até n/3 vezes o número de mensagens processadas por segundo, onde n é a duração padrão do bloqueio.

Existem alguns desafios em ter uma abordagem gananciosa (isto é, manter a contagem de pré-fetch muito alta), porque implica que a mensagem está bloqueada a um determinado recetor. A recomendação é experimentar valores de prefetch entre os limiares acima mencionados e identificar empiricamente o que se encaixa.

## <a name="multiple-queues"></a>Múltiplas filas

Se a carga esperada não puder ser tratada por uma única fila ou tópico, deve utilizar várias entidades de mensagens. Ao utilizar várias entidades, crie um cliente dedicado para cada entidade, em vez de utilizar o mesmo cliente para todas as entidades.

## <a name="development-and-testing-features"></a>Funcionalidades de desenvolvimento e teste

O Service Bus tem uma funcionalidade, utilizada especificamente para o desenvolvimento, que **nunca deve ser utilizada nas configurações**de produção : [Descrição do tópico.Ativar Mensagens de Filtragem Antes de Publicar][].

Quando novas regras ou filtros forem adicionados ao tópico, pode utilizar [Descrição do tópico.Ativar Mensagens de Filtragem Antes de Publicar][] para verificar se a nova expressão do filtro está a funcionar como esperado.

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
[Descrição do tópico.Ativar Mensagens de Filtragem Antes de Publicar]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
