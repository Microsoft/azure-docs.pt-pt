---
title: Monitorize um cluster de tecido de serviço em Azure
description: Neste tutorial, aprende-se a monitorizar um cluster visualizando eventos de Tecido de Serviço, consultando as APIs da EventStore, monitorizando os contadores perf e visualizando relatórios de saúde.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: ab58d622511e0d5793eb6df312bc3fd6dd15bfd6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75376635"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Tutorial: Monitor a Service Fabric cluster in Azure

A monitorização e diagnóstico são fundamentais para o desenvolvimento, teste e implantação de cargas de trabalho em qualquer ambiente de nuvem. Este tutorial é a segunda parte de uma série, e mostra-lhe como monitorizar e diagnosticar um cluster de Tecido de Serviço usando eventos, contadores de desempenho e relatórios de saúde.   Para mais informações, leia a visão geral sobre [a monitorização](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) do cluster e [a monitorização das infraestruturas.](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver eventos de tecido de serviço
> * Perguntas eventstore APIs para eventos de cluster
> * Monitorizar infraestruturas/recolher balcões perf
> * Ver relatórios de saúde de cluster

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um [cluster windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro em Azure usando um modelo
> * Monitorize um cluster
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale [a Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou [o Azure CLI](/cli/azure/install-azure-cli).
* Criar um [cluster windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro 
* Recolha de [diagnósticos](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) de configuração para o cluster
* Ativar o [serviço EventStore](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) no cluster
* Configure [os registos do Monitor Azure e o agente Log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) para o cluster

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Ver eventos de tecido de serviço usando registos do Monitor Azure

Os registos do Azure Monitor recolhem e analisam a telemetria a partir de aplicações e serviços hospedados na nuvem e fornecem ferramentas de análise para ajudá-lo a maximizar a sua disponibilidade e desempenho. Pode fazer consultas em registos do Monitor Azure para obter informações e resolver o que está a acontecer no seu cluster.

Para aceder à solução Service Fabric Analytics, vá ao [portal Azure](https://portal.azure.com) e selecione o grupo de recursos em que criou a solução Service Fabric Analytics.

Selecione o **recurso ServiceFabric (mysfomsworkspace)**.

Em **resumo,** você vê azulejos na forma de um gráfico para cada uma das soluções ativadas, incluindo uma para tecido de serviço. Clique no gráfico **de Tecido de Serviço** para continuar na solução Service Fabric Analytics.

![Solução de tecido de serviço](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

A imagem seguinte mostra a página inicial da solução Service Fabric Analytics. Esta página inicial fornece uma visão instantânea do que está a acontecer no seu cluster.

![Solução de tecido de serviço](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Se você permitiu diagnósticos sobre criação de cluster, você pode ver eventos para 

* [Eventos de cluster de tecido de serviço](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos de modelos de programação de atores fiáveis](service-fabric-reliable-actors-diagnostics.md)
* [Eventos de modelos de programação de serviços fiáveis](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos de Tecido de Serviço fora da caixa, eventos de sistema mais detalhados podem ser recolhidos [atualizando o config da sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Ver Eventos de Tecido de Serviço, incluindo ações em nós

Na página Análise de Tecidode Serviço, clique no gráfico para **Eventos**de Cluster .  Os registos de todos os eventos do sistema que foram recolhidos aparecem. Para referência, estes são do **WADServiceFabricSystemEventsTable** na conta de Armazenamento Azure, e da mesma forma os serviços confiáveis e eventos de atores que você vê a seguir são dessas tabelas respetivas.
    
![Canal Operacional de Consulta](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

A consulta usa a linguagem de consulta Kusto, que pode modificar para refinar o que procura. Por exemplo, para encontrar todas as ações tomadas em nós no cluster, você pode usar a seguinte consulta. Os iDs do evento utilizados abaixo encontram-se na referência de [eventos de canais operacionais](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A linguagem de consulta kusto é poderosa. Aqui estão outras perguntas úteis.

Crie uma tabela de pesquisa *ServiceFabricEvent* como função definida pelo utilizador, guardando a consulta como uma função com pseudónimoServiceFabricEvent:

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

Retorno dos eventos operacionais registados na última hora:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Devolver eventos operacionais com EventId == 18604 e EventName == 'NodeDownOperacional':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Devolver eventos operacionais com EventId == 18604 e EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Devoluções Relatórios de Saúde com Estado de Saúde == 3 (Erro) e extrair propriedades adicionais do campo EventMessage:

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

Devolva um gráfico de tempo de eventos com EventId != 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Obtenha eventos operacionais de Tecido de Serviço agregados com o serviço e o nó específicos:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Renderizar a contagem de eventos de Tecido de Serviço por EventId / EventName utilizando uma consulta de recurso transversal:

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

### <a name="view-service-fabric-application-events"></a>Ver eventos de aplicação de tecido de serviço

Pode ver eventos para serviços fiáveis e aplicações de atores fiáveis implantadas no cluster.  Na página análise de tecido de serviço, clique no gráfico para eventos de **aplicação**.

Execute a seguinte consulta para ver eventos a partir das suas aplicações de serviços confiáveis:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Você pode ver diferentes eventos para quando o runasync de serviço é iniciado e concluído, o que normalmente acontece em implementações e upgrades.

![Serviço solução de tecido fiável Serviços fiáveis](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Também pode encontrar eventos para o serviço fiável com ServiceName == "fabric:/Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Eventos de ator confiáveis podem ser vistos de forma semelhante:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Para configurar eventos mais detalhados para `scheduledTransferKeywordFilter` atores fiáveis, pode alterar o config para a extensão de diagnóstico no modelo de cluster. Os detalhes sobre os valores para estes estão na [referência de eventos de atores fiáveis.](service-fabric-reliable-actors-diagnostics.md#keywords)

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Ver contadores de desempenho com registos do Monitor Azure
Para visualizar contadores de desempenho, vá ao [portal Azure](https://portal.azure.com) e ao grupo de recursos em que criou a solução Service Fabric Analytics. 

Selecione o **recurso ServiceFabric (mysfomsworkspace)**, em seguida, **log Analytics Workspace**, e, em seguida, **Definições Avançadas**.

Clique em **Dados**e, em seguida, clique em Contadores de **Desempenho do Windows**. Existe uma lista de contadores predefinidos que pode escolher para ativar e pode definir o intervalo para a recolha também. Também pode adicionar contadores de [desempenho adicionais](service-fabric-diagnostics-event-generation-perf.md) para recolher. O formato adequado é referenciado neste [artigo.](/windows/desktop/PerfCtrs/specifying-a-counter-path) Clique em **Guardar**e, em seguida, clique **em OK**.

Feche a lâmina de Definições Avançadas e selecione resumo do espaço de **trabalho** sob a rubrica **geral.** Para cada uma das soluções ativadas existe um azulejo gráfico, incluindo um para o Tecido de Serviço. Clique no gráfico **de Tecido de Serviço** para continuar na solução Service Fabric Analytics.

Existem azulejos gráficos para canais operacionais e eventos de serviços fiáveis. A representação gráfica dos dados que fluem para os contadores selecionados aparecerá sob **métricas**de nó . 

Selecione o gráfico métrico do **recipiente** para ver detalhes adicionais. Também pode consultar dados de contra-desempenho semelhantes aos eventos de cluster e filtrar nos nós, contra-nome perf e valores usando a linguagem de consulta Kusto.

## <a name="query-the-eventstore-service"></a>Consulta do serviço EventStore
O [serviço EventStore](service-fabric-diagnostics-eventstore.md) fornece uma forma de compreender o estado do seu cluster ou cargas de trabalho num dado momento. A EventStore é um serviço de tecido de serviço imponente que mantém eventos do cluster. Os eventos são expostos através do [Service Fabric Explorer,](service-fabric-visualizing-your-cluster.md)REST e APIs. EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade do seu cluster Para ver uma lista completa de eventos disponíveis na EventStore, consulte [eventos de Tecido de Serviço](service-fabric-diagnostics-event-generation-operational.md).

As APIs da EventStore podem ser consultadas programáticamente utilizando a [biblioteca cliente do Tecido de Serviço](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Aqui está um pedido de exemplo para todos os eventos de cluster entre 2018-04-03T18:00:00Z e 2018-04-04T18:00:00:00:00Z, através da função GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Eis outro exemplo que questiona a saúde do cluster e todos os eventos de nó em setembro de 2018 e os imprime.

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


## <a name="monitor-cluster-health"></a>Monitorizar a saúde do cluster
O Service Fabric introduz um modelo de [saúde](service-fabric-health-introduction.md) com entidades de saúde em que componentes do sistema e cães de guarda podem reportar as condições locais que estão a monitorizar. A loja de [saúde](service-fabric-health-introduction.md#health-store) agrega todos os dados de saúde para determinar se as entidades são saudáveis.

O cluster é automaticamente povoado com relatórios de saúde enviados pelos componentes do sistema. Leia mais na [Use relatórios de saúde do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Serviço Tecido expõe consultas de saúde para cada um dos tipos de [entidades](service-fabric-health-introduction.md#health-entities-and-hierarchy)suportadas . Podem ser acedidos através da API, utilizando métodos em [FabricClient.HealthManager,](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet)PowerShell cmdlets e REST. Estas consultas devolvem informações completas sobre a saúde sobre a entidade: o estado de saúde agregado, eventos de saúde de entidades, estados de saúde infantil (quando aplicável), avaliações pouco saudáveis (quando a entidade não é saudável) e estatísticas de saúde infantil (quando aplicável).

### <a name="get-cluster-health"></a>Obter saúde de cluster
O [Get-ServiceFabricClusterHealth cmdlet](/powershell/module/servicefabric/get-servicefabricclusterhealth) devolve a saúde da entidade cluster e contém os estados de saúde de aplicações e nós (crianças do cluster).  Em primeiro lugar, ligue-se ao cluster utilizando o [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

O estado do cluster é de 11 nós, a aplicação do sistema e tecido:/Votação configurado como descrito.

O exemplo que se segue obtém a saúde do cluster utilizando políticas de saúde padrão. Os 11 nós são saudáveis, mas o estado de saúde agregado do cluster é erro porque a aplicação de tecido:/Votação está em erro. Note como as avaliações pouco saudáveis fornecem detalhes sobre as condições que desencadearam a saúde agregada.

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

O exemplo seguinte obtém a saúde do cluster utilizando uma política de aplicação personalizada. Filtra os resultados para obter apenas aplicações e nódosos em erro ou aviso. Neste exemplo, não são devolvidos nós, uma vez que são todos saudáveis. Apenas o tecido:/Aplicação de voto respeita o filtro de aplicações. Como a política personalizada especifica considerar os avisos como erros para a aplicação de tecido:/Votação, a aplicação é avaliada como por engano e o cluster também.

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

### <a name="get-node-health"></a>Obter saúde nó
O [Get-ServiceFabricNodeHealth cmdlet](/powershell/module/servicefabric/get-servicefabricnodehealth) devolve a saúde de uma entidade nódoa e contém os eventos de saúde relatados no nó. Em primeiro lugar, ligue-se ao cluster utilizando o [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). O exemplo que se segue obtém a saúde de um nó específico utilizando políticas de saúde padrão:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

O exemplo que se segue obtém a saúde de todos os nós do agrupamento:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Obter saúde do serviço de sistema 

Obtenha a saúde agregada dos serviços do sistema:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver eventos de tecido de serviço
> * Perguntas eventstore APIs para eventos de cluster
> * Monitorizar infraestruturas/recolher balcões perf
> * Ver relatórios de saúde de cluster

Em seguida, avance para o seguinte tutorial para aprender a escalar um cluster.
> [!div class="nextstepaction"]
> [Escala rés um cluster](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
