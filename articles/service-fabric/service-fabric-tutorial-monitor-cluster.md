---
title: Monitorizar um cluster do Service Fabric no Azure | Documentos da Microsoft
description: Neste tutorial, saiba como monitorizar um cluster de visualização de eventos do Service Fabric, consultando as APIs de EventStore, contadores de desempenho de monitorização, e visualizar relatórios de estado de funcionamento.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b8234f286f4304b83969a01704735e1f3a7da2c6
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227447"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Tutorial: Monitorizar um cluster do Service Fabric no Azure

Monitorização e diagnóstico é fundamental para desenvolver, testar e implementar cargas de trabalho em qualquer ambiente de cloud. Este tutorial é a segunda parte de uma série e mostra-lhe como monitorizar e diagnosticar um cluster do Service Fabric com eventos, contadores de desempenho e relatórios de estado de funcionamento.   Para obter mais informações, leia a visão geral sobre [monitorização de clusters](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) e [monitoramento da infraestrutura](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver eventos do Service Fabric
> * Consultar APIs de EventStore para eventos de cluster
> * Monitorizar a infraestrutura/recolher contadores de desempenho
> * Ver relatórios de estado de funcionamento do cluster

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um segura [cluster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) no Azure através de um modelo
> * Monitorizar um cluster
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [módulo do Azure PowerShell na versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) ou a [CLI do Azure ](/cli/azure/install-azure-cli).
* Criar um seguro [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Programa de configuração [recolha de diagnóstico](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) para o cluster
* Ativar a [EventStore serviço](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) no cluster
* Configurar [registos do Azure Monitor e o agente Log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) para o cluster

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Ver eventos do Service Fabric com os registos do Azure Monitor

Registos de Monitor do Azure recolhe e analisa a telemetria a partir de aplicações e serviços alojados na cloud e fornece ferramentas de análise para ajudar a maximizar a disponibilidade e desempenho. Pode executar consultas nos registos do Azure Monitor para obter informações e resolver problemas relacionados com o que acontece no seu cluster.

Para acessar a solução de análise do Service Fabric, vá para o [portal do Azure](https://portal.azure.com) e selecione o grupo de recursos em que criou a solução de análise do Service Fabric.

Selecione o recurso **ServiceFabric(mysfomsworkspace)**.

Na **descrição geral** verá os mosaicos na forma de um gráfico para cada uma das soluções de ativadas, incluindo um para o Service Fabric. Clique nas **Service Fabric** gráfico para continuar para a solução de análise do Service Fabric.

![Solução de Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

A imagem seguinte mostra a home page da solução de análise do Service Fabric. Essa página fornece uma vista de instantâneo do que está acontecendo no seu cluster.

![Solução de Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Se ativou o diagnóstico durante a criação de cluster, pode ver eventos para 

* [Eventos de cluster do Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos do modelo de programação de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos do modelo de programação dos Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos do Service Fabric prontos a utilizar, mais detalhada de sistema de eventos pode ser recolhida pelo [atualizar a configuração da sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Ver eventos de Service Fabric, incluindo ações em nós

Na página de análise do Service Fabric, clique no gráfico para **eventos de Cluster**.  Os registos para todos os eventos de sistema que tenham sido recolhidos são apresentados. Para referência, são do **WADServiceFabricSystemEventsTable** no armazenamento do Azure conta e, da mesma forma, os eventos de serviços e atores fiáveis que verá são destas tabelas respectivos.
    
![Canal operacional da consulta](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

A consulta utiliza a linguagem de consulta de Kusto, que pode ser modificado para refinar o que está procurando. Por exemplo, para localizar todas as ações executadas em nós do cluster, pode utilizar a consulta seguinte. Os IDs de evento utilizados abaixo encontram-se no [referência de eventos de canal operacional](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A linguagem de consulta de Kusto é poderosa. Aqui estão algumas outras consultas úteis.

Criar uma *ServiceFabricEvent* tabela de pesquisa como função definida pelo utilizador ao guardar a consulta como uma função com o alias ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Retornados eventos operacionais gravadas na última hora:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Devolver eventos operacionais com EventId = = 18604 e EventName = = "NodeDownOperational":
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Return operational events with EventId == 18604 and EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Devolve os relatórios de estado de funcionamento com HealthState = = 3 (erro) e extrair propriedades adicionais do campo EventMessage:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Devolver um gráfico de tempo de eventos com o EventId! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Obter eventos operacionais agregados com o serviço específico e o nó do Service Fabric:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Compor a contagem de eventos do Service Fabric por EventId / EventName usando uma consulta entre recursos:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Ver eventos de aplicações do Service Fabric

Pode ver eventos para o reliable services e reliable actors aplicações implementadas no cluster.  Na página de análise do Service Fabric, clique no gráfico para **eventos da aplicação**.

Execute a seguinte consulta para ver os eventos a partir de seus aplicativos de serviços fiáveis:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Pode ver a diferentes eventos para quando o serviço runasync é iniciada e concluída que costuma acontecer sobre Implantações e atualizações.

![Serviços fiáveis do Service Fabric solução](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Também pode encontrar eventos para o serviço fiável com ServiceName = = "fabric: / Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Eventos de Reliable actor podem ser visualizados de forma semelhante:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Para configurar eventos mais detalhados dos reliable actors, pode alterar o `scheduledTransferKeywordFilter` a configuração para a extensão de diagnóstico no modelo de cluster. Detalhes sobre os valores para estes foram os [referência de eventos do reliable actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Contadores de desempenho do modo de exibição com os registos do Azure Monitor
Para ver contadores de desempenho, vá para o [portal do Azure](https://portal.azure.com) e o grupo de recursos em que criou a solução de análise do Service Fabric. 

Selecione o recurso **ServiceFabric(mysfomsworkspace)**, em seguida, **área de trabalho do Log Analytics**e, em seguida **definições avançadas**.

Clique em **dados**, em seguida, clique em **contadores de desempenho do Windows**. Há uma lista de contadores padrão, que pode optar por ativar e pode definir o intervalo de coleção. Também pode adicionar [contadores de desempenho adicionais](service-fabric-diagnostics-event-generation-perf.md) para recolher. O formato correto é referenciado nesta [artigo](/windows/desktop/PerfCtrs/specifying-a-counter-path). Clique em **salvar**, em seguida, clique em **OK**.

Feche o painel de definições avançadas e selecione **resumo de área de trabalho** sob a **geral** cabeçalho. Para cada uma das soluções de ativadas por aí é um mosaico de gráfico, incluindo um para o Service Fabric. Clique nas **Service Fabric** gráfico para continuar para a solução de análise do Service Fabric.

Existem mosaicos gráficos para canal operacional e de eventos do reliable services. A representação gráfica de fluxo para os contadores que tiver selecionado serão apresentado de dados **métricas de nó**. 

Selecione o **métrica de contentor** gráfico para ver detalhes adicionais. Pode também consultar os dados de contador de desempenho da mesma forma para eventos de cluster e filtra os nós, o nome do contador de desempenho e a valores usando a linguagem de consulta de Kusto.

## <a name="query-the-eventstore-service"></a>Consultar o serviço de EventStore
O [EventStore serviço](service-fabric-diagnostics-eventstore.md) fornece uma maneira de compreender o estado do seu cluster ou cargas de trabalho num determinado ponto no tempo. O EventStore é um serviço do Service Fabric com monitorização de estado que mantém os eventos do cluster. Os eventos são expostos por meio da [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST e APIs. EventStore consulta o cluster diretamente para obter dados de diagnóstico sobre qualquer entidade no seu cluster para ver uma lista completa de eventos disponíveis na EventStore, consulte [eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

As APIs de EventStore podem ser consultadas programaticamente usando a [biblioteca de cliente do Service Fabric](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Eis um exemplo de solicitação para todos os eventos de cluster entre 2018-04-03T18:00:00Z e 2018-04-04T18:00:00Z, por meio da função de GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Aqui está outro exemplo que consulta o estado de funcionamento do cluster e todos os eventos de nó em Setembro de 2018 e imprime-los.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Monitorizar o estado de funcionamento do cluster
Service Fabric introduz um [modelo de estado de funcionamento](service-fabric-health-introduction.md) com entidades de estado de funcionamento nas quais componentes de sistema e watchdogs pode relatório local condições que está a monitorizar. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) agrega todos os dados de estado de funcionamento para determinar se as entidades estão em bom estadas.

O cluster é preenchido automaticamente com os relatórios de estado de funcionamento enviados pelos componentes de sistema. Leia mais sobre [utilizar os relatórios de estado de funcionamento do sistema para resolver](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric expõe consultas de estado de funcionamento para cada suportadas [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy). Podem ser acedidos através da API, através de métodos na [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), cmdlets do PowerShell e REST. Estas consultas devolvem informações de estado de funcionamento completo sobre a entidade: o estado de funcionamento agregado, eventos de estado de funcionamento da entidade, Estados de funcionamento do filho (quando aplicável), avaliações de mau estado de funcionamento (quando a entidade não está em bom estada) e as estatísticas de estado de funcionamento de filhos (quando aplicável).

### <a name="get-cluster-health"></a>Obter o estado de funcionamento do cluster
O [cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) devolve o estado de funcionamento da entidade de cluster e contém os Estados de funcionamento de aplicativos e nós (filhos do cluster).  Em primeiro lugar, ligue ao cluster através da [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

O estado do cluster é 11 nós, a aplicação de sistema e fabric: / Voting configurada, tal como descrito.

O exemplo seguinte obtém o estado de funcionamento do cluster ao utilizar políticas de estado de funcionamento padrão. Os 11 nós estão em bom Estados, mas o estado de funcionamento do cluster agregado é erro porque os recursos de infraestrutura: / aplicação Voting está em erro. Observe como o das avaliações de mau estado de funcionamento fornecem detalhes sobre as condições que disparou o estado de funcionamento agregado.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

O exemplo seguinte obtém o estado de funcionamento do cluster com uma política de aplicação personalizada. Filtra os resultados para obter apenas os aplicativos e nós no erro ou aviso. Não existem nós são devolvidos neste exemplo, como eles estão todos em bom Estados. Apenas os recursos de infraestrutura: / aplicação Voting respeita o filtro de aplicativos. Uma vez que a política personalizada Especifica a serem considerados avisos como erros nos recursos de infraestrutura: / Voting aplicativo, o aplicativo é avaliado como em erro e é por isso, o cluster.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Obter o estado de funcionamento do nó
O [cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) devolve o estado de funcionamento de uma entidade de nó e contém os eventos de estado de funcionamento comunicados no nó. Em primeiro lugar, ligue ao cluster utilizando o [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). O exemplo seguinte obtém o estado de funcionamento de um nó específico ao utilizar políticas de estado de funcionamento padrão:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

O exemplo seguinte obtém o estado de funcionamento de todos os nós do cluster:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Obter o estado de funcionamento de serviço de sistema 

Obter o estado de funcionamento agregado dos serviços de sistema:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver eventos do Service Fabric
> * Consultar APIs de EventStore para eventos de cluster
> * Monitorizar a infraestrutura/recolher contadores de desempenho
> * Ver relatórios de estado de funcionamento do cluster

Em seguida, avance para o tutorial seguinte para saber como dimensionar um cluster.
> [!div class="nextstepaction"]
> [Dimensionar um cluster](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json