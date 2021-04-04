---
title: Diagnóstico e monitorização dos atores
description: Este artigo descreve os recursos de diagnóstico e monitorização de desempenho no tempo de execução do Service Fabric Reliable Actors, incluindo os eventos e contadores de desempenho emitidos por ele.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: ebaedb5369f3b39372262bfde526706e8d069418
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789621"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnóstico e monitorização do desempenho dos Reliable Actors
O tempo de execução dos Atores Fidedigdos emite eventos [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) e [contadores de desempenho.](/dotnet/api/system.diagnostics.performancecounter) Estes fornecem informações sobre como o tempo de funcionamento está funcionando e ajudam na resolução de problemas e monitorização do desempenho.

## <a name="eventsource-events"></a>Eventos EventSource
O nome do fornecedor EventSource para o tempo de execução de Atores Fiáveis é "Microsoft-ServiceFabric-Actors". Eventos desta fonte do evento aparecem na janela [de Eventos de Diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando a aplicação do ator está a ser [depurada no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam na recolha e/ou visualização de eventos EventSource são [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics,](../cloud-services/cloud-services-dotnet-diagnostics.md) [Semântica Logging,](/previous-versions/msp-n-p/dn774980(v=pandp.10))e a [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem ao Reliable Actors EventSource estão associados a uma ou mais palavras-chave. Isto permite a filtragem de eventos que são recolhidos. São definidos os seguintes bits de palavra-chave.

| Pouco | Description |
| --- | --- |
| 0x1 |Conjunto de eventos importantes que resumem o funcionamento do tempo de execução dos Atores de Tecido. |
| 0x2 |Conjunto de eventos que descrevem chamadas de método de ator. Para mais informações, consulte o [tópico introdutório sobre os atores.](service-fabric-reliable-actors-introduction.md) |
| 0x4 |Conjunto de eventos relacionados com o estado do ator. Para mais informações, consulte o tópico sobre [a gestão do estado do ator.](service-fabric-reliable-actors-state-management.md) |
| 0x8 |Conjunto de eventos relacionados com a concordância baseada na reviravolta no ator. Para mais informações, consulte o tópico sobre [a concordância.](service-fabric-reliable-actors-introduction.md#concurrency) |

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução dos Atores Fidedigdos define as seguintes categorias de contadores de desempenho.

| Categoria | Descrição |
| --- | --- |
| Ator de Tecido de Serviço |Contadores específicos dos atores da Azure Service Fabric, por exemplo, tempo necessário para salvar o estado do ator |
| Método do ator de tecido de serviço |Contadores específicos aos métodos implementados pelos atores do Service Fabric, por exemplo, a frequência com que um método de ator é invocado |

Cada uma das categorias acima tem um ou mais contadores.

A aplicação [Windows Performance Monitor](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) que está disponível por padrão no sistema operativo Windows pode ser usada para recolher e visualizar dados de contador de desempenho. [O Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e enviá-lo para as tabelas Azure.

### <a name="performance-counter-instance-names"></a>Nomes de contra-instância de desempenho
Um cluster que tem um grande número de serviços de atores ou divisórias de serviço de ator terá um grande número de contra-instâncias de desempenho do ator. Os nomes do contraexemplo de desempenho podem ajudar a identificar o método específico de [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e ator (se aplicável) a que o contraexemplo de desempenho está associado.

#### <a name="service-fabric-actor-category"></a>Categoria de Ator de Tecido de Serviço
Para a `Service Fabric Actor` categoria, os nomes de contraexemplo estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição do Tecido de Serviço a que o contador de desempenho está associado. O ID de partição é um GUID, e a sua representação de cordas é gerada através do método com o [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) especificador de formato "D".

*ActorRuntimeInternalID* é a representação de uma corda de um inteiro de 64 bits que é gerado pelos Fabric Actors runtime para o seu uso interno. Isto está incluído no nome do contraexemplo de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome do contador de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à `Service Fabric Actor` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` está a representação de cordas do ID de partição do Tecido de Serviço, e `635650083799324046` é o ID de 64 bits que é gerado para o uso interno do tempo de execução.

#### <a name="service-fabric-actor-method-category"></a>Categoria de Método de Ator de Tecido de Serviço
Para a `Service Fabric Actor Method` categoria, os nomes de contraexemplo estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método do ator a que o contador de desempenho está associado. O formato do nome do método é determinado com base em alguma lógica no tempo de execução dos Fabric Actors que equilibra a legibilidade do nome com restrições no comprimento máximo dos nomes de instância de contador de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação de uma corda de um inteiro de 32 bits que é gerado pelos Fabric Actors runtime para o seu uso interno. Isto está incluído no nome do contraexemplo de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição do Tecido de Serviço a que o contador de desempenho está associado. O ID de partição é um GUID, e a sua representação de cordas é gerada através do método com o [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) especificador de formato "D".

*ActorRuntimeInternalID* é a representação de uma corda de um inteiro de 64 bits que é gerado pelos Fabric Actors runtime para o seu uso interno. Isto está incluído no nome do contraexemplo de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome do contador de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à `Service Fabric Actor Method` categoria:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` é o nome do método, é o `2` ID de 32 bits gerado para o uso interno do tempo `89383d32-e57e-4a9b-a6ad-57c6792aa521` de execução, é a representação de cadeia do ID de partição de tecido de serviço, e `635650083804480486` é o ID de 64 bits gerado para o uso interno do tempo de execução.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho
### <a name="actor-method-events-and-performance-counters"></a>Eventos de método de ator e contadores de desempenho
O runtime dos Reliable Actors emite os seguintes eventos relacionados com os [métodos do ator.](service-fabric-reliable-actors-introduction.md)

| Nome do evento | ID do Evento | Level | Palavra-chave | Description |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Verboso |0x2 |O tempo de execução dos atores está prestes a invocar um método de ator. |
| ActorMethodStop |8 |Verboso |0x2 |Um método de ator terminou a execução. Ou seja, a chamada assíncrona do tempo de execução para o método do ator regressou, e a tarefa devolvida pelo método do ator terminou. |
| ActorMethod ThrowException |9 |Aviso |0x3 |Uma exceção foi lançada durante a execução de um método de ator, quer durante a chamada assíncrona do tempo de execução ao método do ator, quer durante a execução da tarefa devolvida pelo método do ator. Este evento indica algum tipo de falha no código do ator que precisa de investigação. |

O runtime the Reliable Actors publica os seguintes contadores de desempenho relacionados com a execução de métodos de ator.

| Nome da categoria | Nome do contador | Description |
| --- | --- | --- |
| Método do ator de tecido de serviço |Invocações/Sec |Número de vezes que o método de serviço do ator é invocado por segundo |
| Método do ator de tecido de serviço |Milissegundos médios por invocação |Tempo de execução do método de serviço do ator em milissegundos |
| Método do ator de tecido de serviço |Exceções lançadas/Sec |Número de vezes que o método de serviço do ator atirou uma exceção por segundo |

### <a name="concurrency-events-and-performance-counters"></a>Eventos de concurrency e contadores de desempenho
O tempo de execução dos Atores Fidedigdos emite os seguintes eventos relacionados com a [concuência.](service-fabric-reliable-actors-introduction.md#concurrency)

| Nome do evento | ID do Evento | Level | Palavra-chave | Description |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Verboso |0x8 |Este evento é escrito no início de cada nova volta num ator. Contém o número de chamadas pendentes de atores que estão à espera para adquirir o bloqueio por ator que impõe a concordância baseada em turnos. |

O tempo de execução dos Atores Fidedigdos publica os seguintes contadores de desempenho relacionados com a concordância.

| Nome da categoria | Nome do contador | Description |
| --- | --- | --- |
| Ator de Tecido de Serviço |# de chamadas de ator à espera de bloqueio de ator |Número de chamadas pendentes de ator à espera de adquirir o bloqueio por ator que impõe a concordância baseada em turnos |
| Ator de Tecido de Serviço |Milissegundos médios por espera de bloqueio |Tempo demorado (em milissegundos) para adquirir o bloqueio por ator que impõe a concordância baseada na viragem |
| Ator de Tecido de Serviço |Bloqueio médio de ator de milissegundos detido |Tempo (em milissegundos) para o qual a fechadura por ator é realizada |

### <a name="actor-state-management-events-and-performance-counters"></a>Eventos de gestão do estado do ator e balcões de desempenho
O runtime do Reliable Actors emite os seguintes eventos relacionados com a [gestão estatal do ator.](service-fabric-reliable-actors-state-management.md)

| Nome do evento | ID do Evento | Level | Palavra-chave | Description |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verboso |0x4 |O tempo de execução dos atores está prestes a salvar o estado do ator. |
| ActorSaveStateStop |11 |Verboso |0x4 |Os atores terminaram de salvar o estado do ator. |

O runtime the Reliable Actors publica os seguintes contadores de desempenho relacionados com a gestão do estado do ator.

| Nome da categoria | Nome do contador | Description |
| --- | --- | --- |
| Ator de Tecido de Serviço |Milissegundos médios por operação do Estado de salvamento |Tempo tomado para salvar estado ator em milissegundos |
| Ator de Tecido de Serviço |Milissegundos médios por operação do estado de carga |Tempo tomado para carregar estado do ator em milissegundos |

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados com réplicas de atores
O runtime dos Reliable Actors emite os seguintes eventos relacionados com [réplicas de atores.](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)

| Nome do evento | ID do Evento | Level | Palavra-chave | Description |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informativo |0x1 |A réplica do ator mudou de papel para Primária. Isto implica que os atores para esta partição serão criados dentro desta réplica. |
| ReplicaChangeRoleFromPrimary |2 |Informativo |0x1 |A réplica do ator mudou o papel para não-primária. Isto implica que os atores para esta partição não serão mais criados dentro desta réplica. Nenhum novo pedido será entregue aos atores já criados dentro desta réplica. Os atores serão destruídos depois de concluídos os pedidos em curso. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventos de ativação e desativação de atores e contadores de desempenho
O tempo de execução dos Atores Fidedigdos emite os seguintes eventos relacionados com a [ativação e desativação do ator.](service-fabric-reliable-actors-lifecycle.md)

| Nome do evento | ID do Evento | Level | Palavra-chave | Description |
| --- | --- | --- | --- | --- |
| AtorActivado |5 |Informativo |0x1 |Um ator foi ativado. |
| Ator Desativado |6 |Informativo |0x1 |Um ator foi desativado. |

O runtime the Reliable Actors publica os seguintes contadores de desempenho relacionados com a ativação e desativação do ator.

| Nome da categoria | Nome do contador | Description |
| --- | --- | --- |
| Ator de Tecido de Serviço |Média onActivateAync milissegundos |Tempo de execução do método OnActivateAsync em milissegundos |

### <a name="actor-request-processing-performance-counters"></a>Ator solicita processamento de contadores de desempenho
Quando um cliente invoca um método através de um objeto de procuração de ator, resulta em uma mensagem de pedido enviada através da rede para o serviço de ator. O serviço processa a mensagem de pedido e envia uma resposta ao cliente. O runtime the Reliable Actors publica os seguintes contadores de desempenho relacionados com o processamento de pedidos de ator.

| Nome da categoria | Nome do contador | Description |
| --- | --- | --- |
| Ator de Tecido de Serviço |# de pedidos pendentes |Número de pedidos a ser processados no serviço |
| Ator de Tecido de Serviço |Milissegundos médios por pedido |Tempo tomado (em milissegundos) pelo serviço para processar um pedido |
| Ator de Tecido de Serviço |Milissegundos médios para pedido de deserialização |Tempo demorado (em milissegundos) para deserializar a mensagem de pedido do ator quando é recebido no serviço |
| Ator de Tecido de Serviço |Milissegundos médios para serialização de resposta |Tempo demorado (em milissegundos) para serializar a mensagem de resposta do ator no serviço antes de a resposta ser enviada ao cliente |

## <a name="next-steps"></a>Passos seguintes
* [Como os atores fiáveis usam a plataforma de tecido de serviço](service-fabric-reliable-actors-platform.md)
* [Documentação de referência da API do ator](/previous-versions/azure/dn971626(v=azure.100))
* [Código de amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Fornecedores de EventSource em PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
