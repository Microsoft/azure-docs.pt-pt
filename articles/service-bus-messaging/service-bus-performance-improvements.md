---
title: Melhores práticas para melhorar o desempenho com o Azure Service Bus | Documentos da Microsoft
description: Descreve como utilizar o Service Bus para otimizar o desempenho quando troca de mensagens mediadas.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: f5ce8a237bc2ba7fe15acfcd6afa0edcda7ef713
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589653"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Melhores práticas para melhoramentos do desempenho através de mensagens do Service Bus

Este artigo descreve como utilizar o Azure Service Bus para otimizar o desempenho quando troca de mensagens mediadas. A primeira parte deste artigo descreve os diferentes mecanismos disponibilizados para ajudar a aumentar o desempenho. A segunda parte fornece orientações sobre como utilizar o Service Bus de forma que pode oferecer o melhor desempenho num determinado cenário.

Ao longo deste artigo, o termo "cliente" refere-se a qualquer entidade que acede ao Service Bus. Um cliente pode levar a função de um remetente ou um recetor. O termo "remetente" é usado para um cliente de fila ou tópico do Service Bus que envia mensagens para uma subscrição de fila ou tópico do Service Bus. O termo "destinatário" refere-se a um cliente de fila ou subscrição do Service Bus que recebe mensagens de uma fila do Service Bus ou subscrição.

Estas secções introduzem vários conceitos dos quais o Service Bus utiliza para o ajudar a aumentar o desempenho.

## <a name="protocols"></a>Protocolos

Do Service Bus permite que os clientes enviar e receber mensagens através de um dos três protocolos:

1. Avançadas Message Queuing Protocol (AMQP)
2. (SBMP) do protocolo de mensagens do Service Bus
3. HTTP

AMQP e SBMP são mais eficientes, uma vez que mantêm a ligação ao Service Bus, desde que a fábrica de mensagens existe. Ele também implementa o processamento em lote e pré-busca. A menos que explicitamente mencionado, todo o conteúdo neste artigo supõe que o uso de AMQP ou SBMP.

## <a name="reusing-factories-and-clients"></a>Reutilização de fábricas e de clientes

Objetos de cliente do Service Bus, tal como [QueueClient] [ QueueClient] ou [MessageSender][MessageSender], são criados por meio de um [ MessagingFactory] [ MessagingFactory] objeto, que também fornece gerenciamento interno de ligações. Recomenda-se que não feche as fábricas de mensagens ou clientes de fila, tópico e uma subscrição depois de enviar uma mensagem e, em seguida, voltar a criá-los ao enviar a mensagem seguinte. Fechar uma fábrica de mensagens elimina a ligação ao serviço do Service Bus e uma nova ligação é estabelecida quando recriar a fábrica. Estabelecer uma ligação é uma operação dispendiosa que pode evitar ao reutilizar o mesmo fábrica e os objetos de cliente para várias operações. Pode utilizar estes objetos de cliente com segurança para operações assíncronas simultâneas e de vários threads. 

## <a name="concurrent-operations"></a>Operações simultâneas

Efetuar uma operação (enviar, receber, eliminar, etc.) demora algum tempo. Desta vez inclui o processamento da operação pelo serviço do Service Bus, além da latência do pedido e resposta. Para aumentar o número de operações por hora, operações devem executar em simultâneo. 

O cliente agendar operações simultâneas executando operações assíncronas. A próxima solicitação é iniciada antes do pedido anterior for concluído. O fragmento de código seguinte é um exemplo de uma operação de envio assíncrono:
  
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
  
  O código a seguir é um exemplo de assíncrona receber a operação. Ver o programa completo [aqui](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Receber modo

Ao criar um cliente de fila ou subscrição, pode especificar um modo de receção: *O bloqueio de pré-visualização* ou *receber e eliminar*. Modo de receber a predefinição é [PeekLock][PeekLock]. Quando a funcionar neste modo, o cliente envia um pedido para receber uma mensagem do Service Bus. Depois do cliente recebeu a mensagem, ele envia um pedido para concluir a mensagem.

Ao definir o modo de recebimento [ReceiveAndDelete][ReceiveAndDelete], ambas as etapas são combinadas num único pedido. Estes passos reduzem o número global de operações e podem melhorar o débito de mensagem global. Vem esse ganho de desempenho sob a perda de mensagens.

Barramento de serviço não suporta transações para receber-e-operações de eliminação. Além disso, a semântica de bloqueio de pré-visualização é necessária para qualquer cenários em que o cliente quiser diferir ou [entregues](service-bus-dead-letter-queues.md) uma mensagem.

## <a name="client-side-batching"></a>A criação de batches de lado do cliente

A criação de batches de lado do cliente, permite que um cliente de fila ou tópico atrasar o envio de uma mensagem para um determinado período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. A criação de batches de lado do cliente também faz com que um cliente de fila ou subscrição para o lote várias **Complete** pedidos num único pedido. Só está disponível para criação de batches assíncrona **enviar** e **concluída** operações. Operações síncronas são imediatamente enviadas para o serviço do Service Bus. Criação de batches não ocorrer para pré-visualizar ou receber operações, nem a criação de batches ocorrer em clientes.

Por predefinição, um cliente utiliza um intervalo de lotes de 20 ms. Pode alterar o intervalo de lotes, definindo a [BatchFlushInterval] [ BatchFlushInterval] propriedade antes de criar a fábrica de mensagens. Esta definição afeta todos os clientes que são criados por essa fábrica.

Para desativar a criação de batches, defina o [BatchFlushInterval] [ BatchFlushInterval] propriedade **TimeSpan**. Por exemplo:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Criação de batches não afeta o número de operações mensagens a cobrar e está disponível apenas para o Service Bus cliente protocolo a utilizar o [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) biblioteca. O protocolo HTTP não suporta a criação de batches.

> [!NOTE]
> Definição BatchFlushInterval garante que a criação de batches implícita do ponto de vista do aplicativo. ou seja, o aplicativo faça SendAsync() e CompleteAsync() chama e não faz chamadas específicas do Batch.
>
> Criação de batches de lado do cliente explícita pode ser implementada utilizando o abaixo chamada de método - 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Aqui o tamanho combinado das mensagens tem de ser menor que o tamanho máximo suportado pelo escalão de preço.

## <a name="batching-store-access"></a>Acesso à loja de criação de batches

Para aumentar o débito de uma fila, tópico ou subscrição, o Service Bus lotes várias mensagens quando escreve seu armazenamento interno. Se estiver ativada numa fila ou tópico, escrever mensagens para o arquivo irá ser loteado. Se estiver ativada numa fila ou subscrição, eliminar mensagens a partir da loja irá ser loteado. Se o acesso de arquivo em lote é ativado para uma entidade, o Service Bus atrasa uma operação de escrita de arquivo em relação a essa entidade por até 20 ms. 

> [!NOTE]
> Não há nenhum risco de perda de mensagens com processamento em lote, mesmo que haja uma falha do Service Bus no final de um intervalo de criação de batches de 20 MS. 

Operações de arquivo adicionais que ocorrem durante este intervalo são adicionadas para o batch. Envio em batches afeta apenas de acesso de arquivo **enviar** e **concluída** operações; receber operações não são afetadas. Acesso de arquivo em lote é uma propriedade numa entidade. Criação de batches ocorre em todas as entidades que permitem o acesso de arquivo em lote.

Ao criar uma nova fila, tópico ou subscrição, o acesso de arquivo em lote está ativado por predefinição. Para desativar o acesso de arquivo em lote, defina o [EnableBatchedOperations] [ EnableBatchedOperations] propriedade **false** antes de criar a entidade. Por exemplo:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Acesso de arquivo em lote não afeta o número de operações mensagens a cobrar e é uma propriedade de uma fila, tópico ou subscrição. É independente do modo de recebimento e o protocolo que é utilizado entre um cliente e o serviço do Service Bus.

## <a name="prefetching"></a>Pré-busca

[Pré-busca](service-bus-prefetch.md) permite que o cliente de fila ou subscrição carregar mensagens adicionais do serviço quando ele efetuar uma operação de receção. O cliente armazena essas mensagens num cache local. O tamanho da cache é determinado pelos [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] ou [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] propriedades. Cada cliente que permitem pré-busca mantém sua própria cache. Uma cache não é compartilhada entre os clientes. Se o cliente inicia uma operação de receção e a respetiva cache está vazio, o serviço transmite um lote de mensagens. O tamanho do lote é igual ao tamanho da cache ou 256 KB, o que for mais pequeno. Se o cliente inicia uma operação de receção e a cache contém uma mensagem, a mensagem foi obtida da cache.

Quando uma mensagem é pré-obtidos, o serviço bloqueia a mensagem pré-obtidos. Com o bloqueio, a mensagem de pré-obtidos não pode ser recebida por um recetor diferente. Se o recetor não é possível concluir a mensagem antes do bloqueio expira, a mensagem fica disponível para outros recetores. A cópia pré-obtidos da mensagem permanece na cache. O recetor que consome a cópia em cache expirada irá receber uma exceção quando tentar concluir essa mensagem. Por predefinição, o bloqueio da mensagem expira após 60 segundos. Este valor pode ser estendido para 5 minutos. Para impedir o consumo de mensagens expiradas, o tamanho da cache deve ser sempre menor do que o número de mensagens que podem ser consumidos por um cliente no intervalo de tempo limite de bloqueio.

Ao utilizar a expiração de bloqueio da predefinição de 60 segundos, um bom valor para [PrefetchCount] [ SubscriptionClient.PrefetchCount] é o máximo de 20 vezes as taxas de processamento de todos os recetores do factory. Por exemplo, uma fábrica cria três recetores, e cada destinatário pode processar até 10 mensagens por segundo. A contagem de obtenção prévia não deve exceder os 20 X 3 X 10 = 600. Por predefinição, [PrefetchCount] [ QueueClient.PrefetchCount] está definido como 0, o que significa que não existem mensagens adicionais são obtidas a partir do serviço.

Pré-busca mensagens aumenta a produtividade geral para uma fila ou subscrição, dado que reduz o número geral de operações de mensagem ou ida e volta. A obter a primeira mensagem, no entanto, demora mais tempo (devido ao tamanho maior de mensagem). Receber mensagens pré-obtidos irá ser mais rápido porque essas mensagens já tiverem sido descarregadas pelo cliente.

A propriedade para time-to-live (TTL) de uma mensagem é verificada pelo servidor no momento, que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade de valor de TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem pode ser recebida, mesmo que o valor de TTL da mensagem passou enquanto a mensagem foi colocado em cache pelo cliente.

Pré-busca não afeta o número de operações mensagens a cobrar e está disponível apenas para o protocolo de cliente do Service Bus. O protocolo HTTP não suporta o pré-busca. Pré-busca está disponível para síncronas e assíncronas recebem operações.

## <a name="prefetching-and-receivebatch"></a>Pré-busca e ReceiveBatch

Embora os conceitos de pré-busca várias mensagens em conjunto tem uma semântica parecida ao processamento de mensagens num lote (ReceiveBatch), existem algumas pequenas diferenças que devem ser mantidas em mente ao tirar partido destes elementos.

Obtenção prévia é a configuração (ou modo) no cliente (QueueClient e SubscriptionClient) e ReceiveBatch é uma operação (que tem semântica de solicitação-resposta).

Ao utilizar estes em conjunto, considere os seguintes casos-

* Obtenção prévia deve ser maior que ou igual ao número de mensagens que está esperando para receber de ReceiveBatch.
* Obtenção prévia pode ter até n/3 pelo número de mensagens processados por segundo, onde n é a duração de bloqueio predefinida.

Existem alguns desafios em ter uma greedy abordar (ou seja, mantendo a contagem de obtenção prévia muito elevada), uma vez que isso significa que a mensagem está bloqueada para um destinatário específico. A recomendação é tentar horizontalmente obtenção prévia de valores entre os limiares mencionados acima e empiricamente identificar o que se encaixa.

## <a name="multiple-queues"></a>Várias filas

Se a carga esperada não pode ser processada por uma única fila particionada ou um tópico, tem de utilizar várias entidades de mensagens. Ao usar várias entidades, crie um cliente dedicado para cada entidade, em vez de utilizar o mesmo cliente para todas as entidades.

## <a name="development-and-testing-features"></a>Desenvolvimento e recursos de teste

Do Service Bus tem um recurso, usado especificamente para desenvolvimento, que **nunca deve ser usado em configurações de produção**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Quando novas regras ou filtros são adicionados para o tópico, pode usar [TopicDescription.EnableFilteringMessagesBeforePublishing][] para verificar se a nova expressão de filtro está a funcionar conforme esperado.

## <a name="scenarios"></a>Cenários

As secções seguintes descrevem os cenários típicos de mensagens e descrevem as definições preferenciais do Service Bus. As taxas de débito são classificadas tão pequena (menos do que 1 mensagem por segundo), moderado (1 mensagem por segundo ou superior, mas inferior a 100 mensagens por segundo) e a elevada (100 mensagens/segundo ou superior). O número de clientes é classificado como pequenas moderado (5 ou menos), (mais de 5, mas menor ou igual a 20) e grande (mais do que 20).

### <a name="high-throughput-queue"></a>Fila de alto débito

Objetivo: Maximize o débito de uma única fila. O número de remetentes e recetores é pequeno.

* Para aumentar a taxa de envio global na fila, utilize várias fábricas de mensagens para remetentes de criar. Para cada remetente, utilize operações assíncronas ou vários threads.
* Para aumentar a taxa de recebimento global da fila, utilize várias fábricas de mensagens para criar recetores.
* Utilize operações assíncronas para tirar partido de processamento em lote do lado do cliente.
* Defina o intervalo de criação de batches para 50 ms para reduzir o número de transmissões de protocolo de cliente do Service Bus. Se forem utilizadas várias remetentes, aumente o intervalo de criação de batches para 100 ms.
* Deixe o acesso de arquivo em lote ativado. Este acesso aumenta da frequência com que as mensagens podem ser escritas na fila.
* Defina a contagem de obtenção prévia para 20 vezes as máximo as taxas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolo de cliente do Service Bus.
* Utilize uma fila particionada para melhoria do desempenho e disponibilidade.

### <a name="multiple-high-throughput-queues"></a>Várias filas de alto débito

Objetivo: Maximize a produtividade geral de várias filas. O débito de uma fila individual é moderado ou alta.

Para obter o débito máximo por várias filas, utilize as definições descritas para maximizar o débito de uma única fila. Além disso, use alocadores diferentes para criar os clientes que enviar ou recebem de filas diferentes.

### <a name="low-latency-queue"></a>Fila de baixa latência

Objetivo: Minimize a latência de ponto-a-ponto de uma fila ou tópico. O número de remetentes e recetores é pequeno. O débito da fila é pequeno ou moderado.

* Desative a criação de batches de lado do cliente. O cliente enviará imediatamente uma mensagem.
* Desative o acesso de arquivo em lote. O serviço imediatamente escreve a mensagem para o arquivo.
* Se utilizar um único cliente, defina a contagem de obtenção prévia para 20 vezes a taxa de processamento do destinatário. Se receber várias mensagens em fila, ao mesmo tempo, o protocolo de cliente do Service Bus transmite-os todos ao mesmo tempo. Quando o cliente recebe a mensagem seguinte, que a mensagem já está no local cache. O cache deve ser pequena.
* Se utilizar vários clientes, defina a contagem de obtenção prévia para 0. Ao definir a contagem, o segundo cliente pode receber a mensagem segundo enquanto o primeiro cliente ainda está a processar a primeira mensagem.
* Utilize uma fila particionada para melhoria do desempenho e disponibilidade.

### <a name="queue-with-a-large-number-of-senders"></a>Colocar em fila com um grande número de remetentes confiáveis

Objetivo: Maximize o débito de uma fila ou tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa de moderado. O número de recetores é pequeno.

Service Bus permite até 1000 ligações simultâneas para uma entidade de mensagens (ou 5000 através de AMQP). Este limite é aplicado ao nível do espaço de nomes e as filas/tópicos/subscrições estão colocadas pelo limite de ligações simultâneas por espaço de nomes. Para as filas, este número é compartilhado entre remetentes e recetores. Se todas as ligações de 1000 são necessárias para remetentes, substitua a fila de um tópico e uma única subscrição. Um tópico aceita até 1000 ligações simultâneas de remetentes, ao passo que a subscrição aceita um adicionais 1000 ligações simultâneas dos destinatários. Se mais de 1000 remetentes simultâneas são necessários, os remetentes devem enviar mensagens para o protocolo de Service Bus por HTTP.

Para maximizar a taxa de transferência, execute os seguintes passos:

* Se cada remetente reside num processo diferente, utilize apenas um único alocador por processo.
* Utilize operações assíncronas para tirar partido de processamento em lote do lado do cliente.
* Utilize a predefinição de intervalo de 20 ms de criação de batches para reduzir o número de transmissões de protocolo de cliente do Service Bus.
* Deixe o acesso de arquivo em lote ativado. Este acesso aumenta a frequência com que as mensagens podem ser escritas em fila ou tópico.
* Defina a contagem de obtenção prévia para 20 vezes as máximo as taxas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolo de cliente do Service Bus.
* Utilize uma fila particionada para melhoria do desempenho e disponibilidade.

### <a name="queue-with-a-large-number-of-receivers"></a>Colocar em fila com um grande número de destinatários

Objetivo: Maximize a taxa de receção de uma fila ou subscrição com um grande número de recetores. Cada destinatário recebe mensagens a um ritmo moderado. O número de remetentes confiáveis é pequeno.

Service Bus permite até 1000 ligações simultâneas a uma entidade. Se uma fila necessitar de mais de 1000 recetores, substitua a fila de um tópico e várias subscrições. Cada subscrição pode suportar até 1000 de ligações simultâneas. Em alternativa, recetores podem aceder a fila usando o protocolo HTTP.

Para maximizar a taxa de transferência, faça o seguinte:

* Se cada destinatário reside num processo diferente, utilize apenas um único alocador por processo.
* Recetores podem usar operações de síncronas ou assíncronas. Tendo em conta a taxa de receção moderada de um destinatário individual, criação de batches de lado do cliente de uma solicitação completa não afeta o débito de recetor.
* Deixe o acesso de arquivo em lote ativado. Este acesso reduz a carga geral da entidade. Também reduz a taxa global em que as mensagens podem ser escritas em fila ou tópico.
* Defina a contagem de obtenção prévia para um valor pequeno (por exemplo, PrefetchCount = 10). Esta contagem impede recetores de ociosidade, enquanto outros recetores têm grandes quantidades de mensagens em cache.
* Utilize uma fila particionada para melhoria do desempenho e disponibilidade.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tópico com um pequeno número de subscrições

Objetivo: Maximize a taxa de transferência de um tópico com um pequeno número de subscrições. Uma mensagem é recebida pelo número de subscrições, que significa que a taxa combinada ao longo de todas as subscrições é maior do que a taxa de envio. O número de remetentes confiáveis é pequeno. O número de recetores por subscrição é pequeno.

Para maximizar a taxa de transferência, faça o seguinte:

* Para aumentar a taxa de envio global para o tópico, utilize várias fábricas de mensagens para remetentes de criar. Para cada remetente, utilize operações assíncronas ou vários threads.
* Para aumentar a taxa de recebimento global de uma subscrição, utilize várias fábricas de mensagens para criar recetores. Para cada destinatário utilize operações assíncronas ou vários threads.
* Utilize operações assíncronas para tirar partido de processamento em lote do lado do cliente.
* Utilize a predefinição de intervalo de 20 ms de criação de batches para reduzir o número de transmissões de protocolo de cliente do Service Bus.
* Deixe o acesso de arquivo em lote ativado. Este acesso aumenta da frequência com que as mensagens podem ser escritas no tópico.
* Defina a contagem de obtenção prévia para 20 vezes as máximo as taxas de processamento de todos os recetores de uma fábrica. Esta contagem reduz o número de transmissões de protocolo de cliente do Service Bus.
* Utilize um tópico particionado para melhoria do desempenho e disponibilidade.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tópico com um grande número de subscrições

Objetivo: Maximize a taxa de transferência de um tópico com um grande número de subscrições. Uma mensagem é recebida pelo número de subscrições, o que significa que a taxa de receção combinado ao longo de todas as subscrições é muito maior do que a taxa de envio. O número de remetentes confiáveis é pequeno. O número de recetores por subscrição é pequeno.

Tópicos com um grande número de subscrições, normalmente, expõem uma baixa produtividade geral se todas as mensagens são encaminhadas para todas as subscrições. Essa taxa de transferência baixa é causada pelo fato de que cada mensagem é recebida muitas vezes, e todas as mensagens contidas num tópico e todas as respetivas subscrições são armazenadas no mesmo arquivo. É assumido que o número de remetentes e número de recetores por subscrição são pequenos. Service Bus suporta até 2000 subscrições por tópico.

Para maximizar o débito, tente os seguintes passos:

* Utilize operações assíncronas para tirar partido de processamento em lote do lado do cliente.
* Utilize a predefinição de intervalo de 20 ms de criação de batches para reduzir o número de transmissões de protocolo de cliente do Service Bus.
* Deixe o acesso de arquivo em lote ativado. Este acesso aumenta da frequência com que as mensagens podem ser escritas no tópico.
* Defina a contagem de obtenção prévia para 20 vezes a esperado taxa em segundos. Esta contagem reduz o número de transmissões de protocolo de cliente do Service Bus.
* Utilize um tópico particionado para melhoria do desempenho e disponibilidade.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como otimizar o desempenho do Service Bus, veja [entidades de mensagens Particionadas][Partitioned messaging entities].

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
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
