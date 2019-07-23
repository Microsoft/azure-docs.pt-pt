---
title: Monitorar um Cluster Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, você aprende a monitorar um cluster exibindo Service Fabric eventos, consultando as APIs EventStore, monitorando contadores de desempenho e exibindo relatórios de integridade.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: 63069dcdebf19c64d7bcde298fa234622a6d9a2b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385273"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Tutorial: Monitorar um Cluster Service Fabric no Azure

O monitoramento e o diagnóstico são essenciais para o desenvolvimento, o teste e a implantação de cargas de trabalho em qualquer ambiente de nuvem. Este tutorial é a parte dois de uma série e mostra como monitorar e diagnosticar um Cluster Service Fabric usando eventos, contadores de desempenho e relatórios de integridade.   Para obter mais informações, leia a visão geral sobre monitoramento de [cluster](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) e monitoramento de [infraestrutura](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Exibir eventos de Service Fabric
> * Consultar APIs do EventStore para eventos de cluster
> * Monitorar a infraestrutura/coletar contadores de desempenho
> * Exibir relatórios de integridade do cluster

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure usando um modelo
> * Monitorar um cluster
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou [CLI do Azure](/cli/azure/install-azure-cli).
* Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro 
* Configurar a [coleta](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) de diagnósticos para o cluster
* Habilitar o [serviço EventStore](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) no cluster
* Configurar [logs de Azure monitor e o agente de log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) para o cluster

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Exibir eventos de Service Fabric usando logs de Azure Monitor

Os logs de Azure Monitor coletam e analisam a telemetria de aplicativos e serviços hospedados na nuvem e fornecem ferramentas de análise para ajudá-lo a maximizar a disponibilidade e o desempenho. Você pode executar consultas em logs de Azure Monitor para obter informações e solucionar o que está acontecendo no cluster.

Para acessar a solução de Análise do Service Fabric, vá para o [portal do Azure](https://portal.azure.com) e selecione o grupo de recursos no qual você criou a solução de análise do Service Fabric.

Selecione o recurso **mysfomsworkspace (Resource Fabric)** .

Em **visão geral** , você vê blocos na forma de um grafo para cada uma das soluções habilitadas, incluindo uma para Service Fabric. Clique no **Service Fabric** grafo para continuar a solução de análise do Service Fabric.

![Solução Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

A imagem a seguir mostra a home page da solução Análise do Service Fabric. Este home page fornece uma exibição de instantâneo do que está acontecendo em seu cluster.

![Solução Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Se você tiver habilitado o diagnóstico na criação do cluster, poderá ver eventos para 

* [Service Fabric eventos de cluster](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos de modelo de programação de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos de modelo de programação de Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos de Service Fabric prontos para uso, eventos de sistema mais detalhados podem ser coletados [atualizando a configuração de sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Exibir eventos de Service Fabric, incluindo ações em nós

Na página Análise do Service Fabric, clique no grafo para eventos de **cluster**.  Os logs de todos os eventos do sistema que foram coletados aparecem. Para referência, eles são do **WADServiceFabricSystemEventsTable** na conta de armazenamento do Azure e, da mesma forma, os eventos Reliable Services e atores que você vê em seguida são das respectivas tabelas.
    
![Canal operacional de consulta](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

A consulta usa a linguagem de consulta Kusto, que pode ser modificada para refinar o que você está procurando. Por exemplo, para localizar todas as ações executadas em nós no cluster, você pode usar a consulta a seguir. As IDs de evento usadas abaixo são encontradas na [referência de eventos de canal operacional](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A linguagem de consulta Kusto é poderosa. Aqui estão algumas outras consultas úteis.

Crie uma tabela de pesquisa *ServiceFabricEvent* como uma função definida pelo usuário salvando a consulta como uma função com o alias ServiceFabricEvent:

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

Retornar eventos operacionais registrados na última hora:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Retornar eventos operacionais com EventId = = 18604 e EventName = = ' NodeDownOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Retornar eventos operacionais com EventId = = 18604 e EventName = = ' NodeUpOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Retorna relatórios de integridade com HealthState = = 3 (erro) e extrai propriedades adicionais do campo EventMessage:

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

Retornar um gráfico de tempo de eventos com EventId! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Obtenha Service Fabric eventos operacionais agregados com o serviço e o nó específicos:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Renderiza a contagem de eventos de Service Fabric por EventId/EventName usando uma consulta entre recursos:

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

### <a name="view-service-fabric-application-events"></a>Exibir Service Fabric eventos de aplicativo

Você pode exibir eventos para os aplicativos Reliable Services e Reliable Actors implantados no cluster.  Na página Análise do Service Fabric, clique no grafo para **eventos de aplicativo**.

Execute a consulta a seguir para exibir eventos de seus aplicativos de Reliable Services:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Você pode ver eventos diferentes para quando o serviço RunAsync é iniciado e concluído, o que normalmente ocorre em implantações e atualizações.

![Service Fabric solução Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Você também pode encontrar eventos para o serviço confiável com ServiceName = = "Fabric:/Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Eventos de ator confiáveis podem ser exibidos de maneira semelhante:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Para configurar eventos mais detalhados para Reliable Actors, você `scheduledTransferKeywordFilter` pode alterar o na configuração da extensão de diagnóstico no modelo de cluster. Os detalhes sobre os valores para eles estão na [referência de eventos](service-fabric-reliable-actors-diagnostics.md#keywords)dos Reliable Actors.

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Exibir contadores de desempenho com logs de Azure Monitor
Para exibir os contadores de desempenho, vá para a [portal do Azure](https://portal.azure.com) e o grupo de recursos no qual você criou a solução de análise do Service Fabric. 

Selecione o Resource **Fabric (mysfomsworkspace)** , em seguida, **log Analytics espaço de trabalho**e, em seguida, **Configurações avançadas**.

Clique em **dados**e em **contadores de desempenho do Windows**. Há uma lista de contadores padrão que você pode optar por habilitar e também pode definir o intervalo para coleta. Você também pode adicionar [outros contadores de desempenho](service-fabric-diagnostics-event-generation-perf.md) para coletar. O formato correto é referenciado neste [artigo](/windows/desktop/PerfCtrs/specifying-a-counter-path). Clique em **salvar**e em **OK**.

Feche a folha configurações avançadas e selecione **Resumo do espaço de trabalho** no cabeçalho **geral** . Para cada uma das soluções habilitadas, há um bloco gráfico, incluindo um para Service Fabric. Clique no **Service Fabric** grafo para continuar a solução de análise do Service Fabric.

Há blocos gráficos para eventos de canal operacional e Reliable Services. A representação gráfica dos dados que fluem para os contadores selecionados aparecerá em métricas de **nó**. 

Selecione o grafo de **métrica de contêiner** para ver detalhes adicionais. Você também pode consultar dados do contador de desempenho da mesma forma que os eventos de cluster e filtrar os nós, o nome do contador perf e os valores usando a linguagem de consulta Kusto.

## <a name="query-the-eventstore-service"></a>Consultar o serviço EventStore
O [serviço EventStore](service-fabric-diagnostics-eventstore.md) fornece uma maneira de entender o estado do cluster ou das cargas de trabalho em um determinado momento. O EventStore é um serviço de Service Fabric com estado que mantém eventos do cluster. Os eventos são expostos por meio do [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST e APIs. EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade em seu cluster para ver uma lista completa de eventos disponíveis no EventStore, consulte [Service Fabric eventos](service-fabric-diagnostics-event-generation-operational.md).

As APIs EventStore podem ser consultadas programaticamente usando a [biblioteca de cliente Service Fabric](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Aqui está um exemplo de solicitação para todos os eventos de cluster entre 2018-04-03T18:00:00Z e 2018-04-04T18:00:00Z, por meio da função GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Aqui está outro exemplo que consulta a integridade do cluster e todos os eventos de nó em setembro de 2018 e os imprime.

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
Service Fabric introduz um [modelo de integridade](service-fabric-health-introduction.md) com entidades de integridade nas quais os componentes do sistema e os Watchdogs podem relatar condições locais que estão monitorando. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega todos os dados de integridade para determinar se as entidades estão íntegras.

O cluster é preenchido automaticamente com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [usar relatórios de integridade do sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric expõe consultas de integridade para cada um dos [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy)com suporte. Eles podem ser acessados por meio da API, usando métodos em [FabricClient. healthmanager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), cmdlets do PowerShell e REST. Essas consultas retornam informações de integridade completas sobre a entidade: o estado de integridade agregada, eventos de integridade de entidade, Estados de integridade filho (quando aplicável), avaliações não íntegras (quando a entidade não está íntegra) e estatísticas de integridade de filhos (quando aplicável).

### <a name="get-cluster-health"></a>Obter integridade do cluster
O [cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) retorna a integridade da entidade de cluster e contém os Estados de integridade de aplicativos e nós (filhos do cluster).  Primeiro, conecte-se ao cluster usando o [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

O estado do cluster é de 11 nós, o aplicativo do sistema e a malha:/votação configurada conforme descrito.

O exemplo a seguir obtém a integridade do cluster usando políticas de integridade padrão. Os 11 nós estão íntegros, mas o estado de integridade agregado do cluster é um erro porque o aplicativo Fabric:/votação está em erro. Observe como as avaliações não íntegras fornecem detalhes sobre as condições que dispararam a integridade agregada.

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

O exemplo a seguir obtém a integridade do cluster usando uma política de aplicativo personalizada. Ele filtra os resultados para obter somente os aplicativos e nós com erro ou aviso. Neste exemplo, nenhum nó é retornado, pois todos eles estão íntegros. Somente o aplicativo Fabric:/votação respeita o filtro de aplicativos. Como a política personalizada especifica a consideração de avisos como erros para o aplicativo de malha:/votação, o aplicativo é avaliado como em caso de erro e, portanto, é o cluster.

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

### <a name="get-node-health"></a>Obter integridade do nó
O [cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) retorna a integridade de uma entidade de nó e contém os eventos de integridade relatados no nó. Primeiro, conecte-se ao cluster usando o [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). O exemplo a seguir obtém a integridade de um nó específico usando políticas de integridade padrão:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

O exemplo a seguir obtém a integridade de todos os nós no cluster:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Obter integridade do serviço do sistema 

Obtenha a integridade agregada dos serviços do sistema:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Exibir eventos de Service Fabric
> * Consultar APIs do EventStore para eventos de cluster
> * Monitorar a infraestrutura/coletar contadores de desempenho
> * Exibir relatórios de integridade do cluster

Em seguida, avance para o tutorial a seguir para saber como dimensionar um cluster.
> [!div class="nextstepaction"]
> [Dimensionar um cluster](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
