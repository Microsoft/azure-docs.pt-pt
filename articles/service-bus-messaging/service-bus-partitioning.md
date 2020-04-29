---
title: Criar filas e tópicos de ônibus de serviço azure divididos Microsoft Docs
description: Descreve como dividir as filas e tópicos do Service Bus utilizando vários corretores de mensagens.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2020
ms.author: aschhab
ms.openlocfilehash: 671368993acb43c0d55eca73119effa934e3cff8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260946"
---
# <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

O Azure Service Bus emprega vários corretores de mensagens para processar mensagens e várias lojas de mensagens para armazenar mensagens. Uma fila ou tópico convencional é tratado por um único corretor de mensagens e armazenado numa loja de mensagens. As *divisórias* de ônibus de serviço permitem que filas e tópicos, ou entidades de *mensagens,* sejam divididos em vários corretores de mensagens e lojas de mensagens. A partilha significa que a produção global de uma entidade dividida já não é limitada pelo desempenho de um único corretor de mensagens ou loja de mensagens. Além disso, uma interrupção temporária de uma loja de mensagens não torna indisponível uma fila divisória ou tópico. Filas e tópicos divididos podem conter todas as funcionalidades avançadas do Service Bus, tais como suporte para transações e sessões.

> [!NOTE]
> A partilha está disponível na criação de entidades para todas as filas e tópicos em SKUs Básico ou Standard. Não está disponível para o SKU de mensagens Premium, mas quaisquer entidades anteriormente existentes em espaços de nome Premium continuam a funcionar como esperado.
 
Não é possível alterar a opção de partição em qualquer fila ou tópico existente; só é possível definir a opção quando criar a entidade.

## <a name="how-it-works"></a>Como funciona

Cada fila ou tópico dividido é composto por múltiplas divisórias. Cada partição é armazenada numa loja de mensagens diferente e tratada por um corretor de mensagens diferente. Quando uma mensagem é enviada para uma fila ou tópico dividido, o Service Bus atribui a mensagem a uma das divisórias. A seleção é feita aleatoriamente por Service Bus ou utilizando uma chave de partição que o remetente pode especificar.

Quando um cliente quer receber uma mensagem de uma fila dividida, ou de uma subscrição para um tópico dividido, o Service Bus questiona todas as divisórias para mensagens, em seguida devolve a primeira mensagem que é obtida de qualquer uma das lojas de mensagens para o recetor. Service Bus caches as outras mensagens e devolve-as quando recebe pedidos de receção adicionais. Um cliente recetor não tem conhecimento da divisão; o comportamento virado para o cliente de uma fila ou tópico dividido (por exemplo, ler, completar, adiar, carta morta, prefetching) é idêntico ao comportamento de uma entidade regular.

Não há custoadicional ao enviar uma mensagem para, ou receber uma mensagem de, uma fila ou tópico dividido.

## <a name="enable-partitioning"></a>Ativar a partilha

Para utilizar filas e tópicos divididos com o Azure Service Bus, utilize a versão `api-version=2013-10` Azure SDK 2.2 ou posterior, ou especifique ou especificar mais tarde nos seus pedidos http.

### <a name="standard"></a>Standard

No nível de mensagens Standard, pode criar filas e tópicos de ônibus de serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é de 1 GB). Com a partilha ativada, o Service Bus cria 16 cópias (16 divisórias) da entidade, cada uma do mesmo tamanho especificada. Como tal, se criar uma fila de 5 GB de tamanho, com 16 \* divisórias o tamanho máximo da fila torna-se (5 16) = 80 GB. Pode ver o tamanho máximo da sua fila ou tópico dividido, olhando para a sua entrada no [portal Azure,][Azure portal]na lâmina **de visão geral** dessa entidade.

### <a name="premium"></a>Premium

Num espaço de nome de nível Premium, as entidades de partição não são apoiadas. No entanto, ainda pode criar filas e tópicos de ônibus de serviço em tamanhos de 1, 2, 3, 4, 5, 10, 20, 40 ou 80 GB (o padrão é de 1 GB). Pode ver o tamanho da sua fila ou tópico olhando para a sua entrada no [portal Azure,][Azure portal]na lâmina **de visão geral** dessa entidade.

### <a name="create-a-partitioned-entity"></a>Criar uma entidade dividida

Existem várias formas de criar uma fila dividida ou tópico. Quando criar a fila ou tópico a partir da sua aplicação, pode ativar a partilha para a fila ou tópico, definindo respectivamente a Descrição da [Fila.Ativação][QueueDescription.EnablePartitioning] ou Descrição do [Tópico.Habilitar][TopicDescription.EnablePartitioning] a propriedade **de**partição verdadeiramente . Estas propriedades devem ser definidas no momento em que a fila ou tópico é criado, e estão disponíveis apenas na antiga biblioteca [WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Como referido anteriormente, não é possível alterar estas propriedades numa fila ou tópico existente. Por exemplo:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Em alternativa, pode criar uma fila ou tópico dividido no [portal Azure.][Azure portal] Quando cria uma fila ou tópico no portal, a opção **de partição Ativa** na fila ou tópico **Criar** uma caixa de diálogo é verificada por defeito. Só pode desativar esta opção numa entidade de nível Standard; no nível Premium, a partilha não é suportada e a caixa de verificação não tem qualquer efeito. 

## <a name="use-of-partition-keys"></a>Utilização de chaves de partição

Quando uma mensagem é enquecida numa fila ou tópico dividido, o Ônibus de serviço verifica a presença de uma chave de partição. Se encontrar uma, seleciona a divisória com base nessa tecla. Se não encontrar uma chave de partição, seleciona a divisória com base num algoritmo interno.

### <a name="using-a-partition-key"></a>Usando uma chave de partição

Alguns cenários, como sessões ou transações, exigem que as mensagens sejam armazenadas numa divisória específica. Todos estes cenários requerem o uso de uma chave de partição. Todas as mensagens que usam a mesma chave de partição são atribuídas à mesma divisória. Se a partição estiver temporariamente indisponível, o Ônibus de Serviço devolve um erro.

Dependendo do cenário, diferentes propriedades de mensagem são usadas como chave de partição:

**SessionId**: Se uma mensagem tiver o conjunto de propriedades [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) então o Service Bus utiliza o **SessionID** como chave de partição. Desta forma, todas as mensagens que pertencem à mesma sessão são tratadas pelo mesmo corretor de mensagens. As sessões permitem ao Service Bus garantir a encomenda de mensagens, bem como a consistência dos estados da sessão.

**PartitionKey**: Se uma mensagem tiver a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) mas não o conjunto de propriedades [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) então o Ônibus de serviço utiliza o valor da propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) como chave de partição. Se a mensagem tiver o conjunto de propriedades [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) e [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ambas as propriedades devem ser idênticas. Se a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) estiver definida para um valor diferente da propriedade [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) o Ônibus de Serviço devolve uma exceção de operação inválida. A propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) deve ser utilizada se um remetente enviar mensagens transacionais não sessões. A chave de partição garante que todas as mensagens enviadas dentro de uma transação são tratadas pelo mesmo corretor de mensagens.

**MessageId**: Se a fila ou tópico tiver a propriedade [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) definida como **verdadeira** e as propriedades [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) ou [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) não forem definidas, então o valor da propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) serve como chave de partição. (As bibliotecas Microsoft .NET e AMQP atribuem automaticamente um ID de mensagem se a aplicação de envio não o fizer.) Neste caso, todas as cópias da mesma mensagem são tratadas pelo mesmo corretor de mensagens. Este ID permite ao Service Bus detetar e eliminar mensagens duplicadas. Se a propriedade [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) não estiver definida como **verdadeira,** o Ônibus de serviço não considera a propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não usar uma chave de partição

Na ausência de uma chave de partição, o Service Bus distribui mensagens de forma redonda a todas as divisórias da fila ou tópico divididos. Se a divisória escolhida não estiver disponível, o Service Bus atribui a mensagem a uma divisória diferente. Desta forma, a operação de envio é bem sucedida apesar da indisponibilidade temporária de uma loja de mensagens. No entanto, não conseguirá a ordem garantida que uma chave de partição fornece.

Para uma discussão mais aprofundada sobre a troca entre disponibilidade (sem chave de partição) e consistência (utilizando uma chave de partição), consulte [este artigo](../event-hubs/event-hubs-availability-and-consistency.md). Esta informação aplica-se igualmente às entidades de ônibus de serviço selecionadas.

Para dar tempo suficiente ao Service Bus para incomodá-lo numa divisória diferente, o valor [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) especificado pelo cliente que envia a mensagem deve ser superior a 15 segundos. Recomenda-se que coloque a propriedade [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) no valor padrão de 60 segundos.

Uma chave de partição "pins" uma mensagem para uma partição específica. Se a loja de mensagens que detém esta partição não estiver disponível, o Service Bus devolve um erro. Na ausência de uma chave de partição, o Service Bus pode escolher uma divisória diferente e a operação tem sucesso. Por isso, recomenda-se que não forneça uma chave de partição a menos que seja necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos avançados: utilizar transações com entidades divididas

As mensagens que são enviadas como parte de uma transação têm de especificar uma chave de partição. A chave pode ser uma das seguintes propriedades: [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Todas as mensagens enviadas como parte da mesma transação devem especificar a mesma chave de partição. Se tentar enviar uma mensagem sem chave de partição dentro de uma transação, o Service Bus devolve uma exceção de operação inválida. Se tentar enviar várias mensagens dentro da mesma transação que tenham chaves de partição diferentes, o Service Bus devolve uma exceção de operação inválida. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Se alguma das propriedades que servem como chave de partição estiverem definidas, o Service Bus fixa a mensagem numa divisória específica. Este comportamento ocorre se uma transação é ou não usada. Recomenda-se que não especifique uma chave de partição se não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Utilização de sessões com entidades divididas

Para enviar uma mensagem transacional para um tópico ou fila de sessão, a mensagem deve ter o conjunto de propriedades [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Se a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) também for especificada, deve ser idêntica à propriedade [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Se diferirem, o Ônibus de serviço devolve uma exceção de operação inválida.

Ao contrário de filas ou tópicos regulares (não divididos), não é possível utilizar uma única transação para enviar várias mensagens para diferentes sessões. Se for tentado, o Ônibus de Serviço devolve uma exceção de operação inválida. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Reencaminhamento automático de mensagens com entidades divididas

O Service Bus suporta o reencaminhamento automático de mensagens de, para ou entre entidades divididas. Para ativar o reencaminhamento automático de mensagens, coloque a propriedade [QueueDescription.ForwardTo][QueueDescription.ForwardTo] na fila de origem ou subscrição. Se a mensagem especificar uma chave de partição[(SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)ou [MessageId),](/dotnet/api/microsoft.azure.servicebus.message.messageid)essa chave de partição é utilizada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e orientações
* **Características de alta consistência**: Se uma entidade utilizar funcionalidades como sessões, deteção duplicada ou controlo explícito da chave de partição, então as operações de mensagens são sempre encaminhadas para partição específica. Se alguma das divisórias experimentar tráfego elevado ou a loja subjacente não for saudável, essas operações falham e a disponibilidade é reduzida. Globalmente, a consistência é ainda muito maior do que as entidades não divididas; apenas um subconjunto de tráfego está a passar por problemas, ao contrário de todo o tráfego. Para mais informações, consulte esta [discussão sobre disponibilidade e consistência.](../event-hubs/event-hubs-availability-and-consistency.md)
* **Gestão**: Operações como Criar, Atualizar e Eliminar devem ser realizadas em todas as divisórias da entidade. Se alguma partição não for saudável, pode resultar em falhas nestas operações. Para a operação Get, as informações como a contagem de mensagens devem ser agregadas de todas as divisórias. Se alguma partição não for saudável, o estado de disponibilidade da entidade é reportado como limitado.
* **Cenários**de mensagens de baixo volume : Para tais cenários, especialmente quando utilizar o protocolo HTTP, poderá ter de realizar várias operações de receção para obter todas as mensagens. Para receber pedidos, a parte frontal executa uma receção em todas as divisórias e caches todas as respostas recebidas. Um pedido de receção subsequente sobre a mesma ligação beneficiaria deste cache e receberá tardios mais baixos. No entanto, se tiver múltiplas ligações ou utilizar http, isso estabelece uma nova ligação para cada pedido. Como tal, não há garantias de que aterraria no mesmo nó. Se todas as mensagens existentes estiverem bloqueadas e em cache noutra extremidade frontal, a operação de receção devolve **nulas**. As mensagens acabam por expirar e pode recebê-las novamente. Recomenda-se a manutenção viva HTTP. Ao utilizar a partilha em cenários de baixo volume, receber operações pode demorar mais do que o esperado. Por isso, recomendamos que não use a partilha nestes cenários. Elimine quaisquer entidades divisórias existentes e recrie-as com divisórias desativadas para melhorar o desempenho.
* **Navegue/espreite as mensagens**: Disponível apenas na antiga biblioteca [WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nem sempre devolve o número de mensagens especificadas na propriedade [MessageCount.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) Há duas razões comuns para este comportamento. Uma das razões é que o tamanho agregado da recolha de mensagens excede o tamanho máximo de 256 KB. Outra razão é que se a fila ou tópico tiver a [propriedade EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) definida como **verdadeira,** uma partição pode não ter mensagens suficientes para completar o número de mensagens solicitada. Em geral, se uma aplicação quiser receber um número específico de mensagens, deve ligar repetidamente para [peekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) até que obtenha esse número de mensagens, ou não há mais mensagens para espreitar. Para mais informações, incluindo amostras de código, consulte a documentação [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ou [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API.

## <a name="latest-added-features"></a>As mais recentes funcionalidades adicionadas

* Adicionar ou remover a regra é agora suportado com entidades divididas. Diferentes das entidades não divididas, estas operações não são suportadas no âmbito de transações. 
* A AMQP é agora apoiada para o envio e receção de mensagens de e para uma entidade dividida.
* A AMQP é agora suportada para as seguintes operações: Envio de [lote,](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch)receção de [lote,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch) [receber por número](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive)de sequência , [espreitar](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Renovar bloqueio,](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock) [Agendar Mensagem,](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync) [Cancelar mensagem agendada,](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync)regra de [adição,](/dotnet/api/microsoft.azure.servicebus.ruledescription) [regra de remoção,](/dotnet/api/microsoft.azure.servicebus.ruledescription)bloqueio de renovação da [sessão,](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock)estado de [sessão,](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate)obter estado de [sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)e [enumerar sessões.](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions)

## <a name="partitioned-entities-limitations"></a>Limitações de entidades divididas

Atualmente a Service Bus impõe as seguintes limitações em filas e tópicos divididos:

* As filas e tópicos divididos não são suportados no nível de mensagens Premium. As sessões são suportadas no nível principal utilizando o SessionId. 
* Filas e tópicos divididos não suportam o envio de mensagens que pertencem a diferentes sessões numa única transação.
* Atualmente, o Service Bus permite até 100 filas ou tópicos particionados por espaço de nomes. Cada fila ou tópico dividido conta para a quota de 10.000 entidades por espaço de nome (não se aplica ao nível Premium).

## <a name="next-steps"></a>Passos seguintes

Leia sobre os conceitos fundamentais da especificação de mensagens AMQP 1.0 no guia protocolo [AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
