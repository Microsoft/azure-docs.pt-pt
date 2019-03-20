---
title: Criar o Azure Service Bus filas e tópicos particionados | Documentos da Microsoft
description: Descreve como particionar os tópicos e filas do Service Bus, utilizando vários mediadores de mensagens.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2019
ms.author: aschhab
ms.openlocfilehash: 699581c7ccd3f36da0cd0c1def623607b7c0a13b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57891125"
---
# <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

O Azure Service Bus emprega vários mediadores de mensagens para processar mensagens e vários arquivos de mensagens para armazenar as mensagens. Uma fila convencional ou um tópico é manipulado por um mediador de mensagem única e armazenado num arquivo de mensagens. Do Service Bus *partições* ativar as filas e tópicos, ou *entidades de mensagens*, para ser particionado em vários mediadores de mensagens e arquivos de mensagens. Criação de partições, significa que o débito total de uma entidade com partições já não está limitado pelo desempenho de um mediador de mensagem única ou arquivo de mensagens. Além disso, uma indisponibilidade temporária de um arquivo de mensagens não processa uma particionada fila ou tópico indisponível. Filas e tópicos particionados podem conter avançadas todas as funcionalidades do Service Bus, como o suporte para transações e sessões.

> [!NOTE]
> Criação de partições está disponível durante a criação de entidade para todas as filas e tópicos no Basic ou SKUs padrão. Não está disponível para o SKU de mensagens de Premium, mas qualquer entidades particionadas já existentes em espaços de nomes Premium continuam a funcionar conforme esperado.
 
Não é possível alterar a opção de criação de partições em qualquer fila existente ou um tópico; apenas pode definir a opção ao criar a entidade.

## <a name="how-it-works"></a>Como funciona

Cada particionada fila ou tópico consiste em várias partições. Cada partição é armazenada num arquivo de mensagens diferente e manipulada por um mediador de mensagem diferente. Quando uma mensagem é enviada para uma fila particionada ou um tópico, o Service Bus atribui a mensagem a uma das partições. A seleção é feita aleatoriamente ao Service Bus ou ao utilizar uma chave de partição que o remetente pode especificar.

Quando um cliente pretende receber uma mensagem de uma fila particionada, ou a partir de uma subscrição para um tópico particionado, consultas do Service Bus todas as partições para mensagens, em seguida, devolve a primeira mensagem que é obtida a partir de qualquer um dos arquivos de mensagens para o recetor. Pedidos de receção de caches do Service Bus, as outras mensagens e devolve-os ao receber adicionais. Um cliente de receção não está ciente do particionamento; o comportamento com clientes de uma fila particionada ou um tópico (por exemplo, ler, concluir, diferir, mensagens não entregues, pré-busca) é idêntico ao comportamento de uma entidade regular.

Não existe nenhum custo adicional quando uma mensagem a enviar ou receber uma mensagem de uma fila particionada ou tópico.

## <a name="enable-partitioning"></a>Ativar a criação de partições

Para utilizar as filas e tópicos particionados com o Azure Service Bus, utilizar o Azure SDK versão 2.2 ou posterior ou especifique `api-version=2013-10` ou posterior nos seus pedidos HTTP.

### <a name="standard"></a>Standard

No escalão mensagens padrão, pode criar filas do Service Bus e tópicos em 1, 2, 3, 4 ou tamanhos de 5 GB (a predefinição é 1 GB). Com a criação de partições ativado, o Service Bus cria cópias de 16 (16 partições) da entidade, cada um o mesmo tamanho especificado. Assim, se criar uma fila que é de 5 GB de tamanho, com 16 partições o tamanho máximo da fila se torna (5 \* 16) = 80 GB. Pode ver o tamanho máximo de sua particionada fila ou tópico examinando sua entrada [portal do Azure][Azure portal], no **descrição geral** painel para essa entidade.

### <a name="premium"></a>Premium

Um espaço de nomes de escalão Premium, as entidades de criação de partições não são suportadas. No entanto, pode ainda criar tópicos e filas do Service Bus em 1, 2, 3, 4, 5, 10, 20, 40 ou tamanhos de 80 GB (a predefinição é 1 GB). Pode ver o tamanho da sua fila ou tópico examinando sua entrada [portal do Azure][Azure portal], no **descrição geral** painel para essa entidade.

### <a name="create-a-partitioned-entity"></a>Criar uma entidade com partições

Existem várias formas de criar uma fila particionada ou tópico. Ao criar a fila ou tópico da sua aplicação, pode ativar a criação de partições para a fila ou tópico, respectivamente definindo a [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] ou [ TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] propriedade **verdadeiro**. Estas propriedades tem de ser definidas no momento da fila ou tópico é criado e estão disponíveis apenas no antigo [windowsazure. Servicebus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) biblioteca. Conforme indicado anteriormente, não é possível alterar essas propriedades num tópico ou fila existente. Por exemplo:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Em alternativa, pode criar uma fila particionada ou um tópico no [portal do Azure][Azure portal]. Quando cria uma fila ou tópico no portal, o **ativar a criação de partições** opção na fila ou tópico **criar** caixa de diálogo é marcada por padrão. Apenas pode desativar esta opção numa entidade de escalão Standard; no escalão Premium de criação de partições não é suportada e a caixa de verificação não tem qualquer efeito. 

## <a name="use-of-partition-keys"></a>Utilização de chaves de partição

Quando uma mensagem é colocado em fila numa fila particionada ou um tópico, o Service Bus verifica a presença de uma chave de partição. Se ele encontrar um, seleciona a partição com base nessa chave. Se não encontrar uma chave de partição, seleciona a partição com base num algoritmo interno.

### <a name="using-a-partition-key"></a>Utilizar uma chave de partição

Alguns cenários, tais como sessões ou de transações, exigem que as mensagens sejam armazenados numa partição específica. Todos esses cenários requerem a utilização de uma chave de partição. Todas as mensagens que utilizam a mesma chave de partição são atribuídas para a mesma partição. Se a partição está temporariamente indisponível, o Service Bus devolve um erro.

Dependendo do cenário, as propriedades de mensagem diferente são utilizadas como uma chave de partição:

**SessionId**: Se tiver uma mensagem a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) propriedade definida, em seguida, o Service Bus utiliza **SessionID** como a chave de partição. Dessa forma, todas as mensagens que pertencem à mesma sessão são processadas pelo mesmo message broker. Sessões de ativar o Service Bus para garantir a ordenação, bem como a consistência dos Estados de sessão.

**PartitionKey**: Se tiver uma mensagem a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) propriedade, mas não a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) propriedade definida, em seguida, o Service Bus utiliza o [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) valor da propriedade como a chave de partição. Se a mensagem tem ambos os [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) e o [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) conjunto de propriedades, ambas as propriedades têm de ser idênticas. Se o [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) estiver definida como um valor diferente para o [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) propriedade, barramento de serviço retorna uma exceção de operação inválido. O [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) propriedade deve ser utilizada se um remetente envia mensagens transacionais com reconhecimento de não é de sessão. A chave de partição garante que todas as mensagens que são enviadas dentro de uma transação são processadas pelo mesmo broker mensagens.

**MessageId**: Se a fila ou tópico tem o [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) definida como **true** e o [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) ou [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) propriedades não estão definidas, o [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) valor da propriedade serve como a chave de partição. (As bibliotecas do Microsoft .NET e AMQP atribuir automaticamente um ID de mensagem se o aplicativo de envio não existir.) Neste caso, todas as cópias da mesma mensagem são processadas pelo mesmo message broker. Este ID permite que o Service Bus para detetar e eliminar as mensagens duplicadas. Se o [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) propriedade não está definida como **true**, do Service Bus não considera o [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) propriedade como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não utilizar uma chave de partição

Na ausência de uma chave de partição, do Service Bus distribui mensagens de uma forma de rodízio para todas as partições da particionada fila ou tópico. Se a partição escolhida não estiver disponível, o Service Bus atribui a mensagem a uma partição diferente. Dessa forma, a operação de envio for bem-sucedido apesar da indisponibilidade temporária de um arquivo de mensagens. No entanto, não obterá a ordenação garantido que fornece uma chave de partição.

Para uma discussão mais aprofundada sobre o compromisso entre disponibilidade (nenhuma chave de partição) e consistência (com uma chave de partição), consulte [este artigo](../event-hubs/event-hubs-availability-and-consistency.md). Estas informações se aplica igualmente a entidades particionadas do Service Bus.

Para dar do Service Bus suficiente tempo para colocar em fila a mensagem numa partição diferente, o [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) valor especificado pelo cliente que envia a mensagem deve ser superior a 15 segundos. É recomendado que defina os [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) propriedade para o valor predefinido de 60 segundos.

Uma chave de partição "fixa" uma mensagem para uma partição específica. Se o arquivo de mensagens que contém esta partição não estiver disponível, o Service Bus devolve um erro. Na ausência de uma chave de partição, do Service Bus pode escolher uma partição diferente e a operação for concluída com êxito. Por conseguinte, é recomendado que não está a fornecer uma chave de partição, a menos que seja necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos avançados: utilizar transações com entidades particionadas

As mensagens que são enviadas como parte de uma transação têm de especificar uma chave de partição. A chave pode ser uma das seguintes propriedades: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Todas as mensagens que são enviadas como parte da mesma transação tem de especificar a mesma chave de partição. Se tentar enviar uma mensagem sem uma chave de partição dentro de uma transação, o Service Bus devolve uma exceção de operação inválido. Se está tentando enviar várias mensagens na mesma transação que têm chaves de partição diferentes, o Service Bus devolve uma exceção de operação inválido. Por exemplo:

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

Se qualquer uma das propriedades que servem como uma chave de partição forem definidas, o Service Bus fixa a mensagem para uma partição específica. Este comportamento ocorre independentemente de uma transação é utilizada. Recomenda-se que não especificar uma chave de partição se não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Utilizar sessões com entidades particionadas

Para enviar uma mensagem transacional para um tópico com reconhecimento de sessão ou a fila, a mensagem tem de ter o [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) propriedade definida. Se o [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) propriedade for especificada, bem, tem de ser idêntico do [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) propriedade. Caso sejam diferentes, o barramento de serviço retorna uma exceção de operação inválido.

Ao contrário das filas (não-particionada) regulares ou tópicos, não é possível utilizar uma única transação para enviar várias mensagens para diferentes sessões. Se tentou, o Service Bus devolve uma exceção de operação inválido. Por exemplo:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Reencaminhamento de mensagens automáticas com entidades particionadas

Service Bus suporta mensagem automática de reencaminhamento de, para ou entre entidades particionadas. Para ativar o reencaminhamento de mensagens automática, defina o [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] propriedade na fila de origem ou subscrição. Se a mensagem de especificar uma chave de partição ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), essa chave de partição é utilizada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e diretrizes
* **Recursos de uniformização elevada**: Se uma entidade utiliza funcionalidades como sessões, deteção de duplicados ou controle explícito da chave de criação de partições, em seguida, as operações de mensagens são sempre encaminhadas para a partição específica. Se nenhuma das partições ocorrerem tráfego elevado ou o armazenamento subjacente está danificado, essas operações falharem e disponibilidade é reduzida. Em geral, a consistência é ainda muito maior do que as entidades de não-particionada; apenas um subconjunto de tráfego está a ter problemas, em vez de todo o tráfego. Para obter mais informações, consulte esta [discussão sobre disponibilidade e consistência](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gestão**: Operações, tais como Create, Update e Delete devem ser executadas em todas as partições da entidade. Se qualquer partição está danificada, pode resultar em falhas para essas operações. Para a operação de obtenção de informações como mensagem conta tem de ser agregadas de todas as partições. Se qualquer partição está danificada, o estado de disponibilidade da entidade é comunicado como limitado.
* **Cenários de mensagem do volume de baixa**: Para estes cenários, especialmente quando se utilizam o protocolo HTTP, poderá ter de efetuar várias receber operações para obter todas as mensagens. Para pedidos de receção, o front-end executa um receive em todas as partições e armazena em cache todas as respostas recebidas. Um pedido de receção subsequentes na mesma conexão seria beneficiar Esta colocação em cache e receber latências será inferiores. No entanto, se tiver várias ligações ou utilizar HTTP, que estabelece uma ligação nova para cada solicitação. Como tal, não é garantido que ele seria apresentado no mesmo nó. Se todas as mensagens existentes são bloqueadas e armazenados em cache no outro front-end, a operação receive devolve **nulo**. As mensagens expiram, eventualmente, e pode recebê-las novamente. Recomenda-se a ligação keep-alive de HTTP.
* **Procurar/Peek mensagens**: Disponível apenas no antigo [windowsazure. Servicebus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) biblioteca. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) não devolve o número de mensagens especificado no sempre o [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) propriedade. Existem dois motivos comuns para esse comportamento. Uma razão é que o tamanho do agregado da coleção de mensagens excede o tamanho máximo de 256 KB. Outro motivo é que, se a fila ou tópico tem o [propriedade EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) definida como **verdadeiro**, uma partição pode não ter suficiente mensagens para concluir o número pedido de mensagens. Em geral, se um aplicativo quiser receber um número específico de mensagens, ela deve chamar [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) repetidamente até que ele obtém esse número de mensagens ou não existirem mais mensagens de olhar. Para obter mais informações, incluindo exemplos de código, consulte a [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ou [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) documentação da API.

## <a name="latest-added-features"></a>Mais recentes recursos adicionais

* Adicionar ou remover regra é agora suportada com entidades particionadas. Ao contrário do não-particionada entidades, estas operações não são suportadas em transações. 
* AMQP é agora suportado para o envio e recebimento de mensagens para e de uma entidade com partições.
* AMQP é agora suportado nas seguintes operações: [Envio do batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [lote receber](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [receber pelo número de sequência](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [observar](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [renovar bloqueio](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [agenda Mensagem](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [Cancelar mensagem agendada](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Adicionar regra](/dotnet/api/microsoft.azure.servicebus.ruledescription), [remover regra](/dotnet/api/microsoft.azure.servicebus.ruledescription), [sessão renovar bloqueio](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [ Estado da sessão de conjunto](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [estado de sessão de Get](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), e [enumerar sessões](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Limitações de entidades particionadas

Atualmente o Service Bus impõe as seguintes limitações nas filas e tópicos particionados:

* Filas e tópicos particionados não são suportados no escalão mensagens Premium. Sessões são suportados na camada de premier utilizando SessionId. 
* Filas e tópicos particionados não suporta o envio de mensagens que pertencem a diferentes sessões numa única transação.
* Atualmente, o Service Bus permite até 100 filas ou tópicos particionados por espaço de nomes. Cada particionada fila ou tópico conta para a quota de 10 000 entidades por espaço de nomes (não é aplicável para o escalão Premium).

## <a name="next-steps"></a>Passos Seguintes

Leia sobre os conceitos principais do AMQP 1.0 mensagens especificação na [Guia do protocolo AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
