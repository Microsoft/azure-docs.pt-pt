---
title: Monitorização e diagnóstico de atores | Documentos da Microsoft
description: Este artigo descreve os diagnósticos e funcionalidades no tempo de execução do Reliable Actors do Service Fabric, incluindo os eventos e contadores de desempenho emitidos por ela de monitorização de desempenho.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 888f9e04e048e3da4c9809ac4f8570f020030335
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855840"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnóstico e monitorização do desempenho dos Reliable Actors
O tempo de execução do Reliable Actors emite [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos e [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estes fornecem informações sobre como o tempo de execução está a funcionar e ajudar a resolver problemas e monitorização do desempenho.

## <a name="eventsource-events"></a>Eventos de EventSource
O nome do fornecedor EventSource para o tempo de execução do Reliable Actors é "Microsoft-ServiceFabric-Atores". Eventos a partir desta origem de eventos são apresentados no [eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) janela quando o aplicativo de ator está a ser [depurado no Visual Studio](service-fabric-debugging-your-application.md).

São exemplos de ferramentas e tecnologias que ajudam na recolha de e/ou ver eventos de EventSource [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [registo semântico](https://msdn.microsoft.com/library/dn774980.aspx)e o [ Biblioteca do Microsoft TraceEvent](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem a EventSource de Atores fiáveis são associados a um ou mais palavras-chave. Isto permite a filtragem de eventos que são recolhidos. Os bits de palavra-chave seguintes são definidos.

| bit | Descrição |
| --- | --- |
| 0x1 |Conjunto de eventos importantes que resumem a operação do tempo de execução de Atores de recursos de infraestrutura. |
| 0x2 |Conjunto de eventos que descrevem as chamadas de método de ator. Para obter mais informações, consulte a [introdutório tópico em atores](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Conjunto de eventos relacionados com o estado do ator. Para obter mais informações, consulte o tópico sobre [gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Conjunto de eventos relacionados à simultaneidade de turnos de mensagens em fila no ator. Para obter mais informações, consulte o tópico sobre [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução do Reliable Actors define as seguintes categorias de contador de desempenho.

| Categoria | Descrição |
| --- | --- |
| Ator do Service Fabric |Contadores específicos do Azure Service Fabric actors, por exemplo, tempo demora a guardar o estado do ator |
| Método de Ator do Service Fabric |Contadores específicos para métodos implementados pelos atores do Service Fabric, por exemplo, a frequência com que um método de ator é invocado |

Cada uma das categorias acima tem um ou mais contadores.

O [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) aplicativo que está disponível por predefinição no sistema operacional Windows pode ser usado para recolher e visualizar dados de contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instâncias do contador de desempenho
Um cluster com um grande número de serviços de atores ou partições do serviço de ator terá um grande número de instâncias de contadores de desempenho de ator. Os nomes das instâncias do contador de desempenho podem ajudar a identificar as específicas [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e o método de ator (se aplicável) que a instância do contador de desempenho está associada.

#### <a name="service-fabric-actor-category"></a>Categoria de Ator do Service Fabric
Para a categoria `Service Fabric Actor`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia de caracteres de ID de partição o Service Fabric que está associada à instância do contador de desempenho. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada através da [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo tempo de execução de Atores de recursos de infraestrutura para uso interno. Isso está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador de um contador que pertence o `Service Fabric Actor` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia de caracteres de ID de partição Service Fabric, e `635650083799324046` é usar o ID de 64 bits que é gerado para o tempo de execução interno.

#### <a name="service-fabric-actor-method-category"></a>Categoria de método de Ator do Service Fabric
Para a categoria `Service Fabric Actor Method`, os nomes de instância do contador estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método de ator que a instância do contador de desempenho está associada. O formato do nome do método é determinado com base em algumas lógicas no tempo de execução de Atores de recursos de infraestrutura que equilibra a legibilidade do nome com restrições ao comprimento máximo dos nomes de instância do contador de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação de cadeia de caracteres de um inteiro de 32 bits que é gerado pelo tempo de execução de Atores de recursos de infraestrutura para uso interno. Isso está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia de caracteres de ID de partição o Service Fabric que está associada à instância do contador de desempenho. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada através da [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo tempo de execução de Atores de recursos de infraestrutura para uso interno. Isso está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador de um contador que pertence o `Service Fabric Actor Method` categoria:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` corresponde ao nome do método `2` é o ID de 32 bits gerado para utilização interna do tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia de caracteres de ID de partição Service Fabric, e `635650083804480486` é o ID de 64 bits gerado para o utilização interna do tempo de execução.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho
### <a name="actor-method-events-and-performance-counters"></a>Eventos de método de ator e contadores de desempenho
O tempo de execução do Reliable Actors emite os seguintes eventos relacionados com [métodos de ator](service-fabric-reliable-actors-introduction.md).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Verboso |0x2 |Tempo de execução de atores está prestes a invocar um método de ator. |
| ActorMethodStop |8 |Verboso |0x2 |Um método de ator terminou a execução. Ou seja, devolveu a chamada assíncrona do tempo de execução do método de ator e a tarefa retornada pelo método de ator estiver concluída. |
| ActorMethodThrewException |9 |Aviso |0x3 |Foi emitida uma exceção durante a execução de um método de ator, durante a chamada assíncrona do tempo de execução do método de ator ou durante a execução da tarefa retornada pelo método de ator. Este evento indica algum tipo de falha no código de ator que precisa de investigação. |

O tempo de execução do Reliable Actors publica os seguintes contadores de desempenho relacionados com a execução dos métodos de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de Ator do Service Fabric |Invocações/Segundo |Número de vezes que o método de serviço de atores é invocado por segundo |
| Método de Ator do Service Fabric |Média em milissegundos por invocação |Tempo que demora a executar o método do serviço de atores em milissegundos |
| Método de Ator do Service Fabric |Exceções geradas/Segundo |Número de vezes que o serviço de atores método emitiu uma exceção por segundo |

### <a name="concurrency-events-and-performance-counters"></a>Eventos de simultaneidade e contadores de desempenho
O tempo de execução do Reliable Actors emite os seguintes eventos relacionados com [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Verboso |0x8 |Este evento é escrito no início de cada vez novo num ator. Ela contém o número de pendentes chamadas de ator que estão a aguardar para adquirir o bloqueio por ator que impõe simultaneidade turnos. |

O tempo de execução do Reliable Actors publica os seguintes contadores de desempenho relacionados à simultaneidade.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |Número de chamadas de atores a aguardar pela obtenção do bloqueio por ator |Número de pendentes a aguardar para adquirir o bloqueio por ator que impõe simultaneidade turnos de chamadas de ator |
| Ator do Service Fabric |Média em milissegundos por espera de bloqueio |Tempo decorrido (em milissegundos) para adquirir o bloqueio por ator que impõe simultaneidade turnos |
| Ator do Service Fabric |Média em milissegundos com o bloqueio de ator ativado |Tempo (em milissegundos) para o qual o bloqueio por ator é mantido |

### <a name="actor-state-management-events-and-performance-counters"></a>Eventos de gestão do Estado de ator e contadores de desempenho
O tempo de execução do Reliable Actors emite os seguintes eventos relacionados com [gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verboso |0x4 |Tempo de execução de atores está prestes a salvar o estado de ator. |
| ActorSaveStateStop |11 |Verboso |0x4 |Tempo de execução de atores acabou de guardar o estado de ator. |

O tempo de execução do Reliable Actors publica os seguintes contadores de desempenho relacionados com a gestão de estado do ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |Média em milissegundos por operação de estado de gravação |Tempo que demora a guardar o estado do ator em milissegundos |
| Ator do Service Fabric |Média em milissegundos por operação de estado de carregamento |Tempo decorrido para carregar o estado de ator em milissegundos |

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados com réplicas de ator
O tempo de execução do Reliable Actors emite os seguintes eventos relacionados com [réplicas de ator](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informativo |0x1 |Réplica de ator Alterar função para primário. Isso implica que os atores para esta partição serão criados dentro desta réplica. |
| ReplicaChangeRoleFromPrimary |2 |Informativo |0x1 |Réplica de ator Alterar função para não principal. Isso implica que os atores para esta partição já não serão criados dentro desta réplica. Não existem pedidos novos serão entregues para atores já criados dentro desta réplica. Os atores serão destruídos após todos os pedidos em curso são concluídos. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventos de ativação e desativação de ator e contadores de desempenho
O tempo de execução do Reliable Actors emite os seguintes eventos relacionados com [ativação de ator e a desativação](service-fabric-reliable-actors-lifecycle.md).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informativo |0x1 |Foi ativado um ator. |
| ActorDeactivated |6 |Informativo |0x1 |Um ator foi desativado. |

O tempo de execução do Reliable Actors publica os seguintes contadores de desempenho relacionados com a ativação de ator e a desativação.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |Média em milissegundos de OnActivateAsync |Tempo decorrido para executar o método OnActivateAsync em milissegundos |

### <a name="actor-request-processing-performance-counters"></a>Contadores de desempenho de processamento do pedido de ator
Quando um cliente invoca um método por meio de um objeto de proxy de ator, resulta numa mensagem de solicitação a ser enviada através da rede para o serviço de ator. O serviço processa a mensagem de solicitação e envia uma resposta de volta ao cliente. O tempo de execução do Reliable Actors publica os seguintes contadores de desempenho relacionados com o processamento do pedido de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |N.º de pedidos pendentes |Número de pedidos em processamento no serviço |
| Ator do Service Fabric |Média em milissegundos por pedido |Tempo decorrido (em milissegundos) pelo serviço para processar um pedido |
| Ator do Service Fabric |Média em milissegundos para a desserialização do pedido |Tempo decorrido (em milissegundos) para anular a serialização de mensagem de pedido de ator quando recebido no serviço |
| Ator do Service Fabric |Média em milissegundos para a serialização da resposta |Tempo decorrido (em milissegundos) para serializar a mensagem de resposta de ator no serviço antes da resposta é enviada ao cliente |

## <a name="next-steps"></a>Passos Seguintes
* [Como os Reliable Actors utilizam a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Fornecedores de EventSource na PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
