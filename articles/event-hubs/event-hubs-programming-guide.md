---
title: Guia de programação - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como escrever um código, para os Hubs de eventos do Azure com o SDK de .NET do Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ab067d09d1e161e463922eeae90fe3b46e887150
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544295"
---
# <a name="programming-guide-for-azure-event-hubs"></a>Guia de programação do Event Hubs do Azure
Este artigo aborda alguns cenários comuns em escrever código usando o Event Hubs do Azure. Parte do princípio de que possui compreensão preliminar dos Event Hubs. Para obter uma descrição geral conceptual dos Event Hubs, consulte [Descrição geral dos Event Hubs](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Publicadores de eventos

Enviar eventos para um hub de eventos utilizando o HTTP POST ou através de uma ligação AMQP 1.0. A escolha de qual utilizar e quando depende do cenário específico a ser resolvido. As ligações AMQP 1.0 são medidas como ligações mediadas no Service Bus e são mais adequadas nos cenários com requisitos de latência inferiores e volumes de mensagens altos frequentes, que fornecem um canal de mensagens persistente.

Quando utilizar as APIs .NET geridas, as construções primárias para publicar dados para os Event Hubs são as classes [EventHubClient][] e [EventData][]. [EventHubClient][] fornece o canal de comunicação AMQP, através do qual os eventos são enviados para o hub de eventos. O [EventData][] classe representa um evento e é utilizado para publicar mensagens num hub de eventos. Esta classe inclui o corpo, alguns metadados e as informações de cabeçalho sobre o evento. Outras propriedades são adicionadas para o [EventData][] à medida que passa por meio de um hub de eventos de objeto.

## <a name="get-started"></a>Introdução

O .NET classes que suportam os Hubs de eventos são fornecidos na [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) pacote NuGet. Pode ser instalada utilizando o Explorador de soluções do Visual Studio, ou o [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) no Visual Studio. Para fazê-lo, emita o comando seguinte na janela da [Consola do Gestor de Pacotes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Pode utilizar o portal do Azure, o Azure PowerShell ou a CLI do Azure para criar os Hubs de eventos. Para obter detalhes, consulte [criar um espaço de nomes de Hubs de eventos e um hub de eventos com o portal do Azure](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Criar um cliente dos Event Hubs

A classe principal para interagir com os Hubs de eventos é [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Pode instanciar esta classe utilizando o [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) método, conforme mostrado no exemplo a seguir:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Enviar eventos para um hub de eventos

Enviar eventos para um hub de eventos através da criação de um [EventHubClient][] instância e enviá-la de modo assíncrono através do [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) método. Este método obtém um único [EventData][] parâmetro de instância e de forma assíncrona envia-os para um hub de eventos.

## <a name="event-serialization"></a>Serialização de eventos

O [EventData][] classe tem [dois construtores sobrecarregados](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) que assumem uma variedade de parâmetros, bytes ou uma matriz de bytes, que representam o payload de dados do evento. Quando utilizar o JSON com [EventData][], pode utilizar **Encoding.UTF8.GetBytes()** para obter a matriz de bytes para uma cadeia codificada em JSON. Por exemplo:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Chave de partição

Ao enviar dados de eventos, pode especificar um valor que é protegido por hash para produzir uma atribuição de partição. Especificar a partição a utilizar o [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) propriedade. No entanto, a decisão de usar partições implica uma escolha entre disponibilidade e consistência. 

### <a name="availability-considerations"></a>Considerações de disponibilidade

A utilização de uma chave de partição é opcional e deve considerar cuidadosamente se deve ou não utilizar um. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin. Em muitos casos, utilizar uma chave de partição é uma boa opção se a ordenação de eventos é importante. Quando utiliza uma chave de partição, estas partições necessitam de disponibilidade num único nó e as falhas podem ocorrer ao longo do tempo; Por exemplo, quando computação reinício de nós e patch. Assim, se definir um ID de partição e nessa partição fica indisponível por algum motivo, uma tentativa de acessar os dados nessa partição irá falhar. Se a elevada disponibilidade é o mais importante, não especifique uma chave de partição; Nesse caso os eventos são enviados para as partições com o modelo de round robin descrito anteriormente. Neste cenário, está fazendo uma opção explícita entre a disponibilidade (ID de partição) e consistência (afixação de eventos para um ID de partição).

Outra consideração é o tratamento de atrasos no processamento de eventos. Em alguns casos, talvez seja melhor remover dados e volte a tentar vez de tentar manter-se com o processamento, o que pode provocar potencialmente mais atrasos de processamento a jusante. Por exemplo, com uma cotação da bolsa é melhor esperar para dados atualizados completos, mas num cenário VOIP preferiria ter os dados rapidamente, ou o chat ao vivo, mesmo que não seja concluída.

Feitas essas considerações de disponibilidade, nesses cenários que pode escolher uma do seguinte erro estratégias de tratamento:

- STOP (parar de ler a partir dos Hubs de eventos até que as coisas são fixas)
- Remover (mensagens não são importantes, soltá-los)
- Repetir (repetição que de mensagens, conforme necessário)

Para obter mais informações e uma discussão sobre as compensações entre disponibilidade e consistência, consulte [disponibilidade e consistência em Hubs de eventos](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operações de envio de eventos em lote

Envio de eventos em lotes pode ajudar a aumentar o débito. Pode utilizar o [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API para criar um lote para que os dados mais tarde é possível adicionar objetos para um [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) chamar.

Um único lote não pode exceder o limite de 1 MB de um evento. Além disso, cada mensagem no lote utiliza a mesma identidade do publicador. É da responsabilidade do remetente certificar-se de que o lote não excede o tamanho máximo do evento. Se exceder esse tamanho, é gerado um erro **Enviar** do cliente. Pode usar o método de programa auxiliar [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) para se certificar de que o lote não excede a 1 MB. Obtém vazio [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) da [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API e, em seguida, utilize [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) adicionar eventos para construir o batch. 

## <a name="send-asynchronously-and-send-at-scale"></a>Enviar no modo assíncrono e enviar à escala

Enviar eventos para um hub de eventos forma assíncrona. Enviar no modo assíncrono aumenta a velocidade a que um cliente é capaz de enviar eventos. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) retorna um [tarefa](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objeto. Pode utilizar o [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) as opções de repetição de classe no cliente para o cliente de controlo.

## <a name="event-consumers"></a>Consumidores de eventos

A classe [EventProcessorHost][] processa dados dos Event Hubs. Deve utilizar esta implementação quando criar os leitores dos eventos na plataforma .NET. O [EventProcessorHost][] fornece um ambiente de tempo de execução seguro para thread com vários processos para as implementações do processador de eventos que também fornece pontos de verificação e gestão da concessão da partição.

Para utilizar a classe [EventProcessorHost][], pode implementar o [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Essa interface contém quatro métodos:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Para iniciar o processamento de eventos, instancie [EventProcessorHost][], fornecendo os parâmetros adequados para o seu hub de eventos. Por exemplo:

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Em seguida, chame [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) registrar seu [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementação com o tempo de execução:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Neste momento, o anfitrião tentará adquirir uma concessão em cada partição no hub de eventos usando um algoritmo "abrangente". Estas concessões duram para um determinado período de tempo e, em seguida, tem de ser renovados. À medida que novos nós, instâncias de trabalho neste caso, ficam online, colocam reservas de concessões e ao longo do tempo a carga alterna entre os nós à medida que cada uma tenta adquirir mais concessões.

![Anfitrião do Processador de Eventos](./media/event-hubs-programming-guide/IC759863.png)

Ao longo do tempo, é estabelecido um equilíbrio. Esta capacidade dinâmica permite que o dimensionamento automático baseado em CPU possa ser aplicado aos consumidores para aumentar verticalmente e horizontalmente. Uma vez que os Hubs de eventos não tem um conceito direto de contagens de mensagens, utilização média da CPU é, muitas vezes, o mecanismo para medir o back-end ou consumidor de dimensionamento. Se os publicadores começarem a publicar mais eventos do que aqueles que os consumidores podem processar, o aumento da CPU nos consumidores pode ser utilizado para provocar um dimensionamento automático na contagem de instâncias de trabalho.

A classe [EventProcessorHost][] também implementa um mecanismo de pontos de verificação baseado no Storage do Azure. Este mecanismo armazena o desvio numa base por partição, para que cada consumidor possa determinar qual era o último ponto de verificação do consumidor anterior. Como a transição das partições entre nós é efetuada por concessões, este é o mecanismo de sincronização que facilita a deslocação da carga.

## <a name="publisher-revocation"></a>Revogação do publicador

Para além das funcionalidades avançadas de tempo de execução do [EventProcessorHost][], os Event Hubs permitem a revogação do publicador para bloquear publicadores específicos enviem eventos para um hub de eventos. Esses recursos são úteis se um token do publicador tiver ficado comprometido ou se uma atualização de software é fazendo com que elas se comportem inapropriadamente. Nestas situações, a identidade do publicador, que faz parte do respetivo token SAS, pode ser bloqueada a partir dos eventos de publicação.

Para obter mais informações sobre a revogação do publicador e sobre como enviar Hubs de Eventos como um publicador, veja o exemplo de [Event Hubs Large Scale Secure Publishing (Publicação Segura em Larga Escala dos Hubs de Eventos)](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [Descrição geral da API dos Hubs de Eventos](event-hubs-api-overview.md)
* [O que é o Hubs de eventos](event-hubs-what-is-event-hubs.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Event processor host API reference (Referência da API do anfitrião do processador de eventos)](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
