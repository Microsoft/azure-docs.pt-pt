---
title: .NET Guia de programação - Azure Event Hubs (legado) | Microsoft Docs
description: Este artigo fornece informações sobre como escrever código para Azure Event Hubs usando o Azure .NET SDK.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a299813620ee90591d8c9491991237f75f2e9382
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623053"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>.NET Guia de programação para Azure Event Hubs (pacote legado Microsoft.Azure.EventHubs)
Este artigo discute alguns cenários comuns em código de escrita usando Azure Event Hubs. Parte do princípio de que possui compreensão preliminar dos Event Hubs. Para obter uma descrição geral conceptual dos Event Hubs, consulte [Descrição geral dos Event Hubs](./event-hubs-about.md).

> [!WARNING]
> Este guia destina-se ao antigo pacote **Microsoft.Azure.EventHubs.** Recomendamos que [emigure](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) o seu código para utilizar o mais recente pacote [Azure.Messaging.EventHubs.](event-hubs-dotnet-standard-getstarted-send.md)  


## <a name="event-publishers"></a>Publicadores de eventos

Envia eventos para um centro de eventos utilizando HTTP POST ou através de uma ligação AMQP 1.0. A escolha de que usar e quando depende do cenário específico que está a ser abordado. As ligações AMQP 1.0 são medidas como ligações mediadas no Service Bus e são mais adequadas nos cenários com requisitos de latência inferiores e volumes de mensagens altos frequentes, que fornecem um canal de mensagens persistente.

Quando utilizar as APIs .NET geridas, as construções primárias para publicar dados para os Event Hubs são as classes [EventHubClient][] e [EventData][]. [O EventHubClient][] fornece o canal de comunicação AMQP sobre o qual os eventos são enviados para o centro de eventos. A classe [EventData][] representa um evento e é usada para publicar mensagens num centro de eventos. Esta classe inclui o corpo, alguns metadados (Propriedades) e informações de cabeçalho (SystemProperties) sobre o evento. Outras propriedades são adicionadas ao objeto [EventData][] à medida que passa por um centro de eventos.

## <a name="get-started"></a>Introdução
As classes .NET que suportam os Centros de Eventos são fornecidas no pacote [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet. Pode instalar-se utilizando o explorador visual Studio Solution ou a [Consola De Gestor de Pacotes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) em Estúdio Visual. Para fazê-lo, emita o comando seguinte na janela da [Consola do Gestor de Pacotes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Pode utilizar o portal Azure, Azure PowerShell ou Azure CLI para criar Centros de Eventos. Para mais detalhes, consulte [Criar um espaço de nomes de Centros de Eventos e um centro de eventos utilizando o portal Azure.](event-hubs-create.md)

## <a name="create-an-event-hubs-client"></a>Criar um cliente dos Event Hubs

A principal classe para interagir com os Centros de Eventos é [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Pode instantaneamente esta classe utilizando o método [CreateFromConnectionString,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) como mostra o seguinte exemplo:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Enviar eventos para um centro de eventos

Você envia eventos para um centro de eventos criando um [caso EventHubClient][] e enviando-o assíncronosamente através do método [SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) Este método requer um único parâmetro de instância [EventData][] e assíncronea envia-o para um centro de eventos.

## <a name="event-serialization"></a>Serialização de eventos

A classe [EventData][] tem [dois construtores sobrecarregados](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) que tomam uma variedade de parâmetros, bytes ou uma matriz byte, que representam a carga útil de dados do evento. Quando utilizar o JSON com [EventData][], pode utilizar **Encoding.UTF8.GetBytes()** para obter a matriz de bytes para uma cadeia codificada em JSON. Por exemplo:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Chave de partição

> [!NOTE]
> Se não está familiarizado com as divisórias, consulte [este artigo.](event-hubs-features.md#partitions) 

Ao enviar dados do evento, pode especificar um valor que é hashed para produzir uma atribuição de partição. Especifica a partição utilizando a propriedade [PartitionSender.PartitionID.](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) No entanto, a decisão de utilizar divisórias implica uma escolha entre disponibilidade e consistência. 

### <a name="availability-considerations"></a>Considerações de disponibilidade

A utilização de uma chave de partição é opcional, e deve considerar cuidadosamente se deve ou não utilizar uma. Se não especificar uma chave de partição ao publicar um evento, o Event Hubs equilibra a carga entre divisórias. Em muitos casos, usar uma chave de partição é uma boa escolha se a encomenda de eventos for importante. Quando se utiliza uma chave de partição, estas divisórias requerem disponibilidade num único nó, e podem ocorrer interrupções ao longo do tempo; por exemplo, quando os nós de computação reiniciam e remendam. Como tal, se definir um ID de partição e essa partição ficar indisponível por alguma razão, uma tentativa de aceder aos dados dessa partição falhará. Se a alta disponibilidade for mais importante, não especifique uma chave de partição. Nesse caso, os eventos são enviados para divisórias usando um algoritmo interno de equilíbrio de carga. Neste cenário, está a fazer uma escolha explícita entre disponibilidade (sem ID de partição) e consistência (fixando eventos a um ID de partição).

Outra consideração é lidar com atrasos no processamento de eventos. Em alguns casos, seria melhor deixar cair dados e tentar novamente do que tentar acompanhar o processamento, o que pode potencialmente causar mais atrasos no processamento a jusante. Por exemplo, com um ticker de stock é melhor esperar por dados atualizados completos, mas num cenário de chat ao vivo ou VOIP você prefere ter os dados rapidamente, mesmo que não esteja completo.

Tendo em conta estas considerações de disponibilidade, nestes cenários poderá escolher uma das seguintes estratégias de tratamento de erros:

- Parar (parar de ler nos Centros de Eventos até que as coisas sejam corrigidas)
- Drop (as mensagens não são importantes, largue-as)
- Reda o redando (relemisse as mensagens como entender)

Para obter mais informações e uma discussão sobre as compensações entre disponibilidade e consistência, consulte [Disponibilidade e consistência nos Centros de Eventos.](event-hubs-availability-and-consistency.md) 

## <a name="batch-event-send-operations"></a>Operações de envio de eventos em lote

Enviar eventos em lotes pode ajudar a aumentar a produção. Pode utilizar a API [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) para criar um lote ao qual os objetos de dados podem ser adicionados mais tarde para uma chamada [SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)

Um único lote não deve exceder o limite de 1 MB de um evento. Além disso, cada mensagem no lote utiliza a mesma identidade do publicador. É da responsabilidade do remetente certificar-se de que o lote não excede o tamanho máximo do evento. Se exceder esse tamanho, é gerado um erro **Enviar** do cliente. Pode utilizar o método de ajuda [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) para garantir que o lote não exceda 1 MB. Obtém um [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) vazio da API [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) e, em seguida, use [o TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) para adicionar eventos para construir o lote. 

## <a name="send-asynchronously-and-send-at-scale"></a>Enviar no modo assíncrono e enviar à escala

Envia eventos para um centro de eventos assíncronosamente. O envio assíncronos aumenta a taxa a que um cliente é capaz de enviar eventos. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) devolve um objeto [de Tarefa.](/dotnet/api/system.threading.tasks.task) Pode utilizar a aula [de RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) no cliente para controlar as opções de repetição do cliente.

## <a name="event-consumers"></a>Consumidores de eventos
A classe [EventProcessorHost][] processa dados dos Event Hubs. Deve utilizar esta implementação quando criar os leitores dos eventos na plataforma .NET. O [EventProcessorHost][] fornece um ambiente de tempo de execução seguro para thread com vários processos para as implementações do processador de eventos que também fornece pontos de verificação e gestão da concessão da partição.

Para utilizar a classe [EventProcessorHost][], pode implementar o [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Esta interface contém quatro métodos:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Para iniciar o processamento de eventos, o [EventProcessorHost,][]fornecendo os parâmetros adequados para o seu centro de eventos. Por exemplo:

> [!NOTE]
> EventProcessorHost e as suas classes relacionadas são fornecidas no pacote **Microsoft.Azure.EventHubs.Processor.** Adicione o pacote ao seu projeto Visual Studio seguindo as instruções [deste artigo](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) ou emitindo o seguinte comando na janela consola do Gestor [de Pacotes:](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) `Install-Package Microsoft.Azure.EventHubs.Processor` .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Em seguida, ligue para [o RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) para registar a sua implementação [do IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) com o tempo de execução:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Neste ponto, o anfitrião tenta adquirir um arrendamento em cada divisória no centro do evento usando um algoritmo "ganancioso". Estes contratos de arrendamento duram um determinado prazo e devem ser renovados. À medida que novos nós, instâncias de trabalho neste caso, ficam online, colocam reservas de concessões e ao longo do tempo a carga alterna entre os nós à medida que cada uma tenta adquirir mais concessões.

![Anfitrião do Processador de Eventos](./media/event-hubs-programming-guide/IC759863.png)

Ao longo do tempo, é estabelecido um equilíbrio. Esta capacidade dinâmica permite que o dimensionamento automático baseado em CPU possa ser aplicado aos consumidores para aumentar verticalmente e horizontalmente. Como os Centros de Eventos não têm um conceito direto de contagem de mensagens, a utilização média do CPU é muitas vezes o melhor mecanismo para medir a parte de trás ou a escala do consumidor. Se os publicadores começarem a publicar mais eventos do que aqueles que os consumidores podem processar, o aumento da CPU nos consumidores pode ser utilizado para provocar um dimensionamento automático na contagem de instâncias de trabalho.

A classe [EventProcessorHost][] também implementa um mecanismo de pontos de verificação baseado no Storage do Azure. Este mecanismo armazena o desvio numa base por partição, para que cada consumidor possa determinar qual era o último ponto de verificação do consumidor anterior. Como a transição das partições entre nós é efetuada por concessões, este é o mecanismo de sincronização que facilita a deslocação da carga.

## <a name="publisher-revocation"></a>Revogação do publicador

Além das funcionalidades avançadas de tempo de execução do Event Processor Host, o serviço Event Hubs permite a [revogação](/rest/api/eventhub/revoke-publisher) da editora de forma a impedir que editores específicos enviem eventos para um centro de eventos. Estas funcionalidades são úteis se um token de editor foi comprometido, ou uma atualização de software está a fazê-las comportarem-se de forma inadequada. Nestas situações, a identidade do publicador, que faz parte do respetivo token SAS, pode ser bloqueada a partir dos eventos de publicação.

> [!NOTE]
> Atualmente, apenas a REST API suporta esta funcionalidade[(revogação da editora).](/rest/api/eventhub/revoke-publisher)


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [Descrição geral da API dos Hubs de Eventos](./event-hubs-samples.md)
* [O que é Os Centros de Eventos](./event-hubs-about.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Referência API do processador de eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
