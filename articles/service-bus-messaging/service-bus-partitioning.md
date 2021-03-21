---
title: Crie filas e tópicos de autocarro da Azure Service com divisórias | Microsoft Docs
description: Descreve como dividir filas e tópicos do Service Bus usando vários corretores de mensagens.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c500a69f853b11437a0dcaa48213fe3a84da53b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97796640"
---
# <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

A Azure Service Bus emprega vários corretores de mensagens para processar mensagens e várias lojas de mensagens para armazenar mensagens. Uma fila ou tópico convencional é tratado por um único corretor de mensagens e armazenado numa loja de mensagens. As *divisórias* de Service Bus permitem que filas e tópicos, ou *entidades de mensagens,* sejam divididos em vários corretores de mensagens e lojas de mensagens. A partilha significa que o rendimento global de uma entidade dividida já não é limitado pelo desempenho de um único corretor de mensagens ou loja de mensagens. Além disso, uma paragem temporária de uma loja de mensagens não torna uma fila ou tópico divididos indisponíveis. Filas e tópicos divididos podem conter todas as funcionalidades avançadas do Service Bus, tais como suporte para transações e sessões.

> [!NOTE]
> A partilha está disponível na criação de entidades para todas as filas e tópicos em SKUs Básicos ou Standard. Não está disponível para o SKU de mensagens Premium, mas quaisquer entidades divisórias anteriormente existentes em espaços de nome Premium continuam a funcionar como esperado.
 
Não é possível alterar a opção de partição em qualquer fila ou tópico existente; só pode definir a opção quando criar a entidade.

## <a name="how-it-works"></a>Como funciona

Cada fila ou tópico dividido consiste em múltiplas divisórias. Cada divisória é armazenada numa loja de mensagens diferente e tratada por um corretor de mensagens diferente. Quando uma mensagem é enviada para uma fila ou tópico dividido, o Service Bus atribui a mensagem a uma das divisórias. A seleção é feita aleatoriamente pela Service Bus ou utilizando uma chave de partição que o remetente pode especificar.

Quando um cliente quer receber uma mensagem de uma fila dividida, ou de uma subscrição de um tópico dividido, o Service Bus consulta todas as divisórias para mensagens, em seguida, devolve a primeira mensagem que é obtida de qualquer uma das lojas de mensagens para o recetor. O Service Bus caches as outras mensagens e devolve-as quando recebe pedidos de receção adicionais. Um cliente recetor não tem conhecimento da partição; o comportamento voltado para o cliente de uma fila ou tópico dividido (por exemplo, ler, ler, completar, adiar, deadletter, prefetching) é idêntico ao comportamento de uma entidade regular.

A operação de espreitar uma entidade não dividida devolve sempre a mensagem mais antiga, mas não numa entidade dividida. Em vez disso, devolve a mensagem mais antiga numa das divisórias cujo corretor de mensagens respondeu primeiro. Não há garantias de que a mensagem devolvida seja a mais antiga em todas as divisórias. 

Não há custo adicional ao enviar uma mensagem ou receber uma mensagem de uma fila ou tópico dividido.

> [!NOTE]
> A operação de espreitar devolve a mensagem mais antiga da partição com base no seu número de sequência. Para entidades divididas, o número da sequência é emitido em relação à partição. Para obter mais informações, consulte [sequenciação de mensagens e semdas de tempo](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Permitir a partilha

Para utilizar filas e tópicos divididos com o Azure Service Bus, utilize a versão 2.2 ou posterior do Azure SDK, ou especifique `api-version=2013-10` ou mais tarde nos seus pedidos HTTP.

### <a name="standard"></a>Standard

No nível de mensagens Standard, pode criar filas e tópicos de Service Bus em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é de 1 GB). Com a partição ativada, a Service Bus cria 16 cópias (16 divisórias) da entidade, cada uma do mesmo tamanho especificada. Como tal, se criar uma fila de 5 GB de tamanho, com 16 divisórias o tamanho máximo da fila torna-se (5 \* 16) = 80 GB. Pode ver o tamanho máximo da sua fila ou tópico dividido, olhando para a sua entrada no [portal Azure,][Azure portal]na lâmina **de visão geral** para essa entidade.

### <a name="premium"></a>Premium

Num espaço de nome de nível Premium, as entidades de partição não são apoiadas. No entanto, ainda pode criar filas e tópicos de Service Bus em tamanhos de 1, 2, 3, 4, 5, 10, 20, 40 ou 80 GB (o padrão é de 1 GB). Pode ver o tamanho da sua fila ou tópico olhando para a sua entrada no [portal Azure,][Azure portal]na lâmina **Overview** para essa entidade.

### <a name="create-a-partitioned-entity"></a>Criar uma entidade dividida

Existem várias formas de criar uma fila ou tópico dividido. Ao criar a fila ou tópico a partir da sua aplicação, pode ativar a divisão para a fila ou tópico, definindo respectivamente a [FilaDescription.EnablePartitioning][QueueDescription.EnablePartitioning] ou [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] property to **true**. Estas propriedades devem ser definidas no momento em que a fila ou tópico é criado, e estão disponíveis apenas na biblioteca [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) mais antiga. Como indicado anteriormente, não é possível alterar estas propriedades numa fila ou tópico existente. Por exemplo:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Em alternativa, pode criar uma fila ou tópico dividido no [portal Azure.][Azure portal] Quando cria uma fila ou tópico no portal, a opção **de divisão Ativa na** fila ou tópico **Criar** caixa de diálogo é verificada por predefinição. Só pode desativar esta opção numa entidade standard; na partição de nível Premium não é suportado, e a caixa de verificação não tem efeito. 

## <a name="use-of-partition-keys"></a>Utilização de chaves de partição

Quando uma mensagem é encostada numa fila ou tópico dividido, o Service Bus verifica a presença de uma chave de partição. Se encontrar uma, seleciona a partição com base nessa chave. Se não encontrar uma chave de partição, seleciona a partição com base num algoritmo interno.

### <a name="using-a-partition-key"></a>Usando uma chave de partição

Alguns cenários, como sessões ou transações, exigem que as mensagens sejam armazenadas numa partição específica. Todos estes cenários requerem o uso de uma chave de partição. Todas as mensagens que usam a mesma chave de partição são atribuídas à mesma partição. Se a divisória não estiver temporariamente disponível, a Service Bus retorna um erro.

Dependendo do cenário, diferentes propriedades de mensagens são usadas como chave de partição:

**SessionId**: Se uma mensagem tiver o conjunto de propriedades [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) então o Service Bus utiliza **o SessionID** como chave de partição. Desta forma, todas as mensagens que pertencem à mesma sessão são tratadas pelo mesmo corretor de mensagens. As sessões permitem ao Service Bus garantir o pedido de mensagem, bem como a consistência dos estados da sessão.

**PartitionKey**: Se uma mensagem tiver a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) mas não o conjunto de propriedades [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) então o Service Bus utiliza o valor da propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) como chave de partição. Se a mensagem tiver as propriedades [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) e [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) definidas, ambas as propriedades devem ser idênticas. Se a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) estiver definida para um valor diferente da propriedade [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) o Service Bus devolve uma exceção de operação inválida. A [propriedade PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) deve ser utilizada se um remetente enviar mensagens transacionais sem sessão. A chave de partição garante que todas as mensagens enviadas dentro de uma transação são tratadas pelo mesmo corretor de mensagens.

**MessageId**: Se a fila ou tópico tiver a propriedade [RequerduplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) definida como **verdadeira** e as propriedades [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) ou [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) não estiverem definidas, então o valor da propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) serve como chave de partição. (As bibliotecas Microsoft .NET e AMQP atribuem automaticamente um ID de mensagem se a aplicação de envio não o fizer.) Neste caso, todas as cópias da mesma mensagem são tratadas pelo mesmo corretor de mensagens. Este ID permite que o Service Bus detete e elimine mensagens duplicadas. Se a propriedade [RequerduplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) não estiver definida como **verdadeira,** a Service Bus não considera a propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não usar uma chave de partição

Na ausência de uma chave de partição, a Service Bus distribui mensagens de forma redonda a todas as divisórias da fila ou tópico dividido. Se a partição escolhida não estiver disponível, a Service Bus atribui a mensagem a uma partição diferente. Desta forma, a operação de envio tem sucesso apesar da indisponibilidade temporária de uma loja de mensagens. No entanto, não conseguirá o pedido garantido que uma chave de partição proporciona.

Para uma discussão mais aprofundada sobre a compensação entre disponibilidade (sem chave de partição) e consistência (utilizando uma chave de partição), consulte [este artigo](../event-hubs/event-hubs-availability-and-consistency.md). Esta informação aplica-se igualmente às entidades de serviços divididos.

Para dar tempo suficiente ao Service Bus para esquisiturar a mensagem numa partição diferente, o valor [operationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) especificado pelo cliente que envia a mensagem deve ser superior a 15 segundos. Recomenda-se que desemalte a propriedade [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) para o valor predefinido de 60 segundos.

Uma chave de partição "pins" uma mensagem para uma partição específica. Se a loja de mensagens que detém esta divisória não estiver disponível, o Service Bus retorna um erro. Na ausência de uma chave de partição, a Service Bus pode escolher uma divisória diferente e a operação tem sucesso. Por isso, recomenda-se que não forneça uma chave de partição a menos que seja necessária.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos avançados: utilizar transações com entidades divididas

As mensagens que são enviadas como parte de uma transação têm de especificar uma chave de partição. A chave pode ser uma das seguintes propriedades: [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Todas as mensagens enviadas como parte da mesma transação devem especificar a mesma chave de partição. Se tentar enviar uma mensagem sem uma chave de partição dentro de uma transação, a Service Bus devolve uma exceção de operação inválida. Se tentar enviar várias mensagens dentro da mesma transação que tenham diferentes chaves de partição, a Service Bus devolve uma exceção de operação inválida. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Se alguma das propriedades que servem de chave de partição estiver definida, o Service Bus coloca a mensagem numa partição específica. Este comportamento ocorre se uma transação é ou não usada. Recomenda-se que não especifique uma chave de partição se não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Usando sessões com entidades divididas

Para enviar uma mensagem transacional para um tópico ou fila consciente da sessão, a mensagem deve ter a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) definida. Se a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) também for especificada, deve ser idêntica à propriedade [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Se diferirem, a Service Bus retorna uma exceção de funcionamento inválida.

Ao contrário de filas ou tópicos regulares (não divididos), não é possível utilizar uma única transação para enviar várias mensagens para diferentes sessões. Se for tentado, a Service Bus devolve uma exceção de operação inválida. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Reencaminhamento automático de mensagens com entidades divididas

A Service Bus suporta o encaminhamento automático de mensagens de, para ou entre entidades divididas. Para ativar o encaminhamento automático de mensagens, desacrição de [fila.ForwardTo][QueueDescription.ForwardTo] na fila de origem ou subscrição. Se a mensagem especificar uma tecla de partição[(SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), essa tecla de partição é utilizada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e orientações
* **Características de alta consistência**: Se uma entidade utilizar funcionalidades como sessões, deteção duplicada ou controlo explícito da chave de partição, então as operações de mensagens são sempre encaminhadas para uma partição específica. Se alguma das divisórias experimentar tráfego elevado ou a loja subjacente não for saudável, essas operações falham e a disponibilidade é reduzida. Globalmente, a consistência é ainda muito maior do que as entidades não divididas; apenas um subconjunto de tráfego está a ter problemas, ao contrário de todo o tráfego. Para mais informações, consulte esta [discussão de disponibilidade e consistência.](../event-hubs/event-hubs-availability-and-consistency.md)
* **Gestão**: Operações como Criar, Atualizar e Eliminar devem ser realizadas em todas as divisórias da entidade. Se alguma partição não for saudável, pode resultar em falhas nestas operações. Para a operação Get, as informações, como as contagens de mensagens, devem ser agregadas de todas as divisórias. Se alguma partição não for saudável, o estado de disponibilidade da entidade é reportado como limitado.
* **Cenários de mensagens de baixo volume**: Para tais cenários, especialmente quando utilizar o protocolo HTTP, poderá ter de efetuar múltiplas operações de receção para obter todas as mensagens. Para receber pedidos, a parte frontal realiza uma receção em todas as divisórias e caches todas as respostas recebidas. Um pedido de receção subsequente sobre a mesma ligação beneficiaria desta caching e receberia latências mais baixas. No entanto, se tiver várias ligações ou utilizar HTTP, que estabeleça uma nova ligação para cada pedido. Como tal, não há garantias de que aterre no mesmo nó. Se todas as mensagens existentes estiverem bloqueadas e em cache noutra extremidade frontal, a operação de receção **retorna nulo**. As mensagens eventualmente expiram e pode recebê-las novamente. HTTP Keep-alive é recomendado. Ao utilizar a partição em cenários de baixo volume, as operações de receção podem demorar mais tempo do que o esperado. Por isso, recomendamos que não use divisórias nestes cenários. Elimine quaisquer entidades partidas existentes e recrie-as com desativação de divisórias para melhorar o desempenho.
* **Procurar mensagens:** Disponível apenas na biblioteca [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) mais antiga. [O PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nem sempre devolve o número de mensagens especificadas na propriedade [MessageCount.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) Há duas razões comuns para este comportamento. Uma das razões é que o tamanho agregado da recolha de mensagens excede o tamanho máximo de 256 KB. Outra razão é que se a fila ou tópico tiver a [propriedade EnablePartition](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) definida como **verdadeira,** uma divisória pode não ter mensagens suficientes para completar o número de mensagens solicitadas. Em geral, se uma aplicação quiser receber um número específico de mensagens, deve ligar repetidamente para [o PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) até receber esse número de mensagens, ou não há mais mensagens para espreitar. Para obter mais informações, incluindo amostras de código, consulte a documentação [da API queueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ou [subscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API.

## <a name="latest-added-features"></a>As mais recentes funcionalidades adicionadas

* A regra de adicionar ou remover é agora suportada com entidades divididas. Diferentes de entidades não divididas, estas operações não são suportadas no âmbito de operações. 
* A AMQP é agora suportada para o envio e receção de mensagens de e para uma entidade dividida.
* A AMQP está agora suportada para as seguintes operações: [Envio de Lote,](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch) [Receber,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch) [Receber por Número de Sequência](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Espreitar](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), Renovar [mensagem,](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync)Cancelar [](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock) [mensagem agendada](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Adicionar Regra,](/dotnet/api/microsoft.azure.servicebus.ruledescription)Remover [Regra, Remover Regra,](/dotnet/api/microsoft.azure.servicebus.ruledescription) [Renovar a Sessão,](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock) [Definir Estado de Sessão,](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate) [Obter Estado de Sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)e [Sessões enumeradas](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Limitações de entidades divididas

Atualmente, a Service Bus impõe as seguintes limitações em filas e tópicos divididos:

* As filas e tópicos divididos não são suportados no nível de mensagens Premium. As sessões são suportadas no nível principal utilizando o SessionId. 
* Filas e tópicos divididos não suportam o envio de mensagens que pertencem a diferentes sessões numa única transação.
* Atualmente, o Service Bus permite até 100 filas ou tópicos particionados por espaço de nomes. Cada fila ou tópico dividido conta para a quota de 10.000 entidades por espaço de nome (não se aplica ao nível Premium).

## <a name="next-steps"></a>Passos seguintes

Leia sobre os conceitos fundamentais da especificação de mensagens AMQP 1.0 no guia do [protocolo AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
