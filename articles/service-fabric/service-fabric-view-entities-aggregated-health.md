---
title: Como exibir a integridade agregada das entidades de Service Fabric do Azure | Microsoft Docs
description: Descreve como consultar, exibir e avaliar a integridade agregada das entidades de Service Fabric do Azure, por meio de consultas de integridade e consultas gerais.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 1721f10f8950577080a89ba58a3eb4dd3a25c188
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249190"
---
# <a name="view-service-fabric-health-reports"></a>Exibir Service Fabric relatórios de integridade
O Azure Service Fabric introduz um [modelo de integridade](service-fabric-health-introduction.md) com entidades de integridade nas quais os componentes do sistema e os Watchdogs podem relatar condições locais que estão monitorando. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega todos os dados de integridade para determinar se as entidades estão íntegras.

O cluster é preenchido automaticamente com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [usar relatórios de integridade do sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric fornece várias maneiras de obter a integridade agregada das entidades:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização
* Consultas de integridade (por meio do PowerShell, API ou REST)
* Consultas gerais que retornam uma lista de entidades que têm integridade como uma das propriedades (por meio do PowerShell, da API ou do REST)

Para demonstrar essas opções, vamos usar um cluster local com cinco nós e o [aplicativo Fabric:/WordCount](https://aka.ms/servicefabric-wordcountapp). O aplicativo **Fabric:/WordCount** contém dois serviços padrão, um serviço com estado do `WordCountServiceType`tipo e um serviço sem estado do `WordCountWebServiceType`tipo. Alterei o `ApplicationManifest.xml` para exigir sete réplicas de destino para o serviço com estado e uma partição. Como há apenas cinco nós no cluster, os componentes do sistema relatam um aviso na partição do serviço, pois ele está abaixo da contagem de destino.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Integridade no Service Fabric Explorer
Service Fabric Explorer fornece uma exibição visual do cluster. Na imagem abaixo, você pode ver que:

* O aplicativo **Fabric:/WordCount** é vermelho (em caso de erro) porque ele tem um evento de  erro relatado por mywatchdog para a **disponibilidade**da propriedade.
* Um de seus serviços, **Fabric:/WordCount/WordCountService** é amarelo (em aviso). O serviço está configurado com sete réplicas e o cluster tem cinco nós, portanto, duas réplicas não podem ser colocadas. Embora não seja mostrado aqui, a partição de serviço é amarela devido a um relatório do sistema `System.FM` de dizer `Partition is below target replica or instance count`que. A partição amarela dispara o serviço amarelo.
* O cluster é vermelho devido ao aplicativo vermelho.

A avaliação usa políticas padrão do manifesto do cluster e do manifesto do aplicativo. Eles são políticas estritas e não toleram nenhuma falha.

Exibição do cluster com Service Fabric Explorer:

![Exibição do cluster com Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Leia mais sobre [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Consultas de integridade
Service Fabric expõe consultas de integridade para cada um dos [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy)com suporte. Eles podem ser acessados por meio da API, usando métodos em [FabricClient. healthmanager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), cmdlets do PowerShell e REST. Essas consultas retornam informações de integridade completas sobre a entidade: o estado de integridade agregada, eventos de integridade de entidade, Estados de integridade filho (quando aplicável), avaliações não íntegras (quando a entidade não está íntegra) e estatísticas de integridade de filhos (quando aplicável).

> [!NOTE]
> Uma entidade de integridade é retornada quando é totalmente preenchida no repositório de integridade. A entidade deve estar ativa (não excluída) e ter um relatório do sistema. Suas entidades pai na cadeia de hierarquia também devem ter relatórios do sistema. Se qualquer uma dessas condições não for satisfeita, as consultas de integridade retornarão uma [fabricexception](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) com [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` que mostra por que a entidade não é retornada.
>
>

As consultas de integridade devem passar no identificador de entidade, que depende do tipo de entidade. As consultas aceitam parâmetros de política de integridade opcionais. Se nenhuma política de integridade for especificada, as [políticas de integridade](service-fabric-health-introduction.md#health-policies) do manifesto do cluster ou do aplicativo serão usadas para avaliação. Se os manifestos não contiverem uma definição para políticas de integridade, as políticas de integridade padrão serão usadas para avaliação. As políticas de integridade padrão não toleram nenhuma falha. As consultas também aceitam filtros para retornar apenas filhos parciais ou eventos, aqueles que respeitam os filtros especificados. Outro filtro permite a exclusão de estatísticas filhas.

> [!NOTE]
> Os filtros de saída são aplicados no lado do servidor, portanto, o tamanho da resposta da mensagem é reduzido. Recomendamos que você use os filtros de saída para limitar os dados retornados, em vez de aplicar filtros no lado do cliente.
>
>

A integridade de uma entidade contém:

* O estado de integridade agregado da entidade. Calculado pelo repositório de integridade com base nos relatórios de integridade de entidade, Estados de integridade filho (quando aplicável) e políticas de integridade. Leia mais sobre a [avaliação de integridade da entidade](service-fabric-health-introduction.md#health-evaluation).  
* Os eventos de integridade na entidade.
* A coleção de Estados de integridade de todos os filhos para as entidades que podem ter filhos. Os Estados de integridade contêm identificadores de entidade e o estado de integridade agregado. Para obter a integridade completa de um filho, chame a integridade da consulta para o tipo de entidade filho e passe o identificador filho.
* As avaliações não íntegras que apontam para o relatório que disparou o estado da entidade, se a entidade não estiver íntegra. As avaliações são recursivas, contendo as avaliações de integridade dos filhos que acionaram o estado de integridade atual. Por exemplo, um Watchdog relatou um erro em relação a uma réplica. A integridade do aplicativo mostra uma avaliação não íntegra devido a um serviço não íntegro; o serviço não está íntegro devido a uma partição com erro; a partição não está íntegra devido a uma réplica em erro; a réplica não está íntegra devido ao relatório de integridade de erro do Watchdog.
* As estatísticas de integridade para todos os tipos filhos das entidades que têm filhos. Por exemplo, a integridade do cluster mostra o número total de aplicativos, serviços, partições, réplicas e entidades implantadas no cluster. A integridade do serviço mostra o número total de partições e réplicas no serviço especificado.

## <a name="get-cluster-health"></a>Obter integridade do cluster
Retorna a integridade da entidade de cluster e contém os Estados de integridade de aplicativos e nós (filhos do cluster). Entrada:

* Adicional A política de integridade do cluster usada para avaliar os nós e os eventos de cluster.
* Adicional O mapa da política de integridade do aplicativo, com as políticas de integridade usadas para substituir as políticas de manifesto do aplicativo.
* Adicional Filtros para eventos, nós e aplicativos que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos, nós e aplicativos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* Adicional Filtre para excluir as estatísticas de integridade.
* Adicional Filtre para incluir malha:/estatísticas de integridade do sistema nas estatísticas de integridade. Aplicável somente quando as estatísticas de integridade não são excluídas. Por padrão, as estatísticas de integridade incluem apenas estatísticas para aplicativos de usuário e não para o aplicativo do sistema.

### <a name="api"></a>API
Para obter a integridade do cluster, `FabricClient` crie um e chame o método [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) em seu **healthmanager**.

A chamada a seguir obtém a integridade do cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

O código a seguir obtém a integridade do cluster usando uma política de integridade de cluster personalizada e filtros para nós e aplicativos. Ele especifica que as estatísticas de integridade incluem a malha:/estatísticas do sistema. Ele cria [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), que contém as informações de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do cluster é [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O estado do cluster é de cinco nós, o aplicativo do sistema e o Fabric:/WordCount configurados conforme descrito.

O cmdlet a seguir obtém a integridade do cluster usando políticas de integridade padrão. O estado de integridade agregado é aviso, pois o aplicativo Fabric:/WordCount está em aviso. Observe como as avaliações não íntegras fornecem detalhes sobre as condições que dispararam a integridade agregada.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

O cmdlet do PowerShell a seguir obtém a integridade do cluster usando uma política de aplicativo personalizada. Ele filtra os resultados para obter somente os aplicativos e nós com erro ou aviso. Como resultado, nenhum nó é retornado, pois todos estão íntegros. Somente o aplicativo Fabric:/WordCount respeita o filtro de aplicativos. Como a política personalizada especifica a consideração de avisos como erros para o aplicativo Fabric:/WordCount, o aplicativo é avaliado como em erro e, portanto, é o cluster.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Você pode obter a integridade do cluster com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) ou uma [solicitação post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) que inclui as políticas de integridade descritas no corpo.

## <a name="get-node-health"></a>Obter integridade do nó
Retorna a integridade de uma entidade de nó e contém os eventos de integridade relatados no nó. Entrada:

* Necessária O nome do nó que identifica o nó.
* Adicional As configurações de política de integridade do cluster usadas para avaliar a integridade.
* Adicional Filtros de eventos que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade do nó por meio da API `FabricClient` , crie um e chame o método [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) em seu healthmanager.

O código a seguir obtém a integridade do nó para o nome do nó especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

O código a seguir obtém a integridade do nó para o nome do nó especificado e passa o filtro de eventos e a política personalizada por meio de [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do nó é [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .
O cmdlet a seguir obtém a integridade do nó usando políticas de integridade padrão:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O cmdlet a seguir obtém a integridade de todos os nós no cluster:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Você pode obter integridade do nó com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) ou uma [solicitação post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) que inclua as políticas de integridade descritas no corpo.

## <a name="get-application-health"></a>Obter integridade do aplicativo
Retorna a integridade de uma entidade de aplicativo. Ele contém os Estados de integridade do aplicativo e dos filhos de serviço implantados. Entrada:

* Necessária O nome do aplicativo (URI) que identifica o aplicativo.
* Adicional A política de integridade do aplicativo usada para substituir as políticas de manifesto do aplicativo.
* Adicional Filtros para eventos, serviços e aplicativos implantados que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos, serviços e aplicativos implantados são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* Adicional Filtre para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade incluirão a contagem Ok, de aviso e de erro para todos os filhos do aplicativo: serviços, partições, réplicas, aplicativos implantados e pacotes de serviço implantados.

### <a name="api"></a>API
Para obter a integridade do aplicativo, `FabricClient` crie um e chame o método [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) em seu healthmanager.

O código a seguir obtém a integridade do aplicativo para o nome do aplicativo especificado (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

O código a seguir obtém a integridade do aplicativo para o nome do aplicativo (URI) especificado, com filtros e políticas personalizadas especificadas por meio de [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do aplicativo é [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir retorna a integridade do aplicativo **Fabric:/WordCount** :

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

O cmdlet do PowerShell a seguir passa em políticas personalizadas. Ele também filtra os filhos e eventos.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Você pode obter a integridade do aplicativo com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) ou um [post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) que inclua as políticas de integridade descritas no corpo.

## <a name="get-service-health"></a>Obter integridade do serviço
Retorna a integridade de uma entidade de serviço. Ele contém os Estados de integridade da partição. Entrada:

* Necessária O nome do serviço (URI) que identifica o serviço.
* Adicional A política de integridade do aplicativo usada para substituir a política de manifesto do aplicativo.
* Adicional Filtros para eventos e partições que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos e partições são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* Adicional Filtre para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade mostrarão a contagem Ok, de aviso e de erro para todas as partições e réplicas do serviço.

### <a name="api"></a>API
Para obter a integridade do serviço por meio da API `FabricClient` , crie um e chame o método [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) em seu healthmanager.

O exemplo a seguir obtém a integridade de um serviço com o nome do serviço especificado (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

O código a seguir obtém a integridade do serviço para o nome do serviço especificado (URI), especificando filtros e política personalizada por meio de [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do serviço é [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade do serviço usando políticas de integridade padrão:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Você pode obter a integridade do serviço com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) ou um [post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) que inclua as políticas de integridade descritas no corpo.

## <a name="get-partition-health"></a>Obter integridade da partição
Retorna a integridade de uma entidade de partição. Ele contém os Estados de integridade da réplica. Entrada:

* Necessária A ID da partição (GUID) que identifica a partição.
* Adicional A política de integridade do aplicativo usada para substituir a política de manifesto do aplicativo.
* Adicional Filtros para eventos e réplicas que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos e réplicas são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* Adicional Filtre para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade mostrarão quantas réplicas estão em OK, aviso e Estados de erro.

### <a name="api"></a>API
Para obter a integridade da partição por meio da API `FabricClient` , crie um e chame o método [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) em seu healthmanager. Para especificar parâmetros opcionais, crie [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade da partição é [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade de todas as partições do serviço **Fabric:/WordCount/WordCountService** e filtra os Estados de integridade da réplica:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Você pode obter a integridade da partição com uma solicitação [Get](/rest/api/servicefabric/sfclient-api-getpartitionhealth) ou um [post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) que inclua as políticas de integridade descritas no corpo.

## <a name="get-replica-health"></a>Obter integridade da réplica
Retorna a integridade de uma réplica de serviço com estado ou de uma instância de serviço sem estado. Entrada:

* Necessária A ID da partição (GUID) e a ID da réplica que identifica a réplica.
* Adicional Os parâmetros da política de integridade do aplicativo usados para substituir as políticas de manifesto do aplicativo.
* Adicional Filtros de eventos que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade da réplica por meio da API, `FabricClient` crie um e chame o método [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) em seu healthmanager. Para especificar parâmetros avançados, use [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade da réplica é [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade da réplica primária para todas as partições do serviço:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Você pode obter a integridade da réplica com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) ou um [post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) que inclua as políticas de integridade descritas no corpo.

## <a name="get-deployed-application-health"></a>Obter integridade do aplicativo implantado
Retorna a integridade de um aplicativo implantado em uma entidade de nó. Ele contém os Estados de integridade do pacote de serviço implantado. Entrada:

* Necessária O nome do aplicativo (URI) e o nome do nó (cadeia de caracteres) que identificam o aplicativo implantado.
* Adicional A política de integridade do aplicativo usada para substituir as políticas de manifesto do aplicativo.
* Adicional Filtros para eventos e pacotes de serviço implantados que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos e pacotes de serviço implantados são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* Adicional Filtre para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade mostrarão o número de pacotes de serviço implantados nos Estados de integridade Ok, aviso e erro.

### <a name="api"></a>API
Para obter a integridade de um aplicativo implantado em um nó por meio da API `FabricClient` , crie um e chame o método [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) em seu healthmanager. Para especificar parâmetros opcionais, use [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do aplicativo implantado é [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Para descobrir onde um aplicativo é implantado, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e examine os filhos do aplicativo implantado.

O cmdlet a seguir obtém a integridade do aplicativo **Fabric:/WordCount** implantado em **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Você pode obter a integridade do aplicativo implantado com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) ou um [post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) que inclua as políticas de integridade descritas no corpo.

## <a name="get-deployed-service-package-health"></a>Obter integridade do pacote de serviço implantado
Retorna a integridade de uma entidade de pacote de serviço implantada. Entrada:

* Necessária O nome do aplicativo (URI), o nome do nó (cadeia de caracteres) e o nome do manifesto do serviço (cadeia de caracteres) que identificam o pacote de serviço implantado.
* Adicional A política de integridade do aplicativo usada para substituir a política de manifesto do aplicativo.
* Adicional Filtros de eventos que especificam quais entradas são interessantes e devem ser retornados no resultado (por exemplo, somente erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade de um pacote de serviço implantado por meio da `FabricClient` API, crie um e chame o método [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) em seu healthmanager. Para especificar parâmetros opcionais, use [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do pacote de serviço implantado é [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Para ver onde um aplicativo é implantado, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e examine os aplicativos implantados. Para ver quais pacotes de serviço estão em um aplicativo, examine os filhos do pacote de serviço implantado na saída [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade do pacote de serviço **WordCountServicePkg** do aplicativo **Fabric:/WordCount** implantado no **_Node_2**. A entidade tem relatórios **System. Hosting** para ativação bem-sucedida de pacote de serviço e ponto de entrada e registro de tipo de serviço bem-sucedido.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Você pode obter a integridade do pacote de serviço implantado com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) ou um [post](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) que inclua as políticas de integridade descritas no corpo.

## <a name="health-chunk-queries"></a>Consultas de partes de integridade
As consultas de partes de integridade podem retornar os filhos do cluster de vários níveis (recursivamente), por filtros de entrada. Ele dá suporte a filtros avançados que permitem muita flexibilidade na escolha dos filhos a serem retornados. Os filtros podem especificar filhos pelo identificador exclusivo ou por outros identificadores de grupo e/ou Estados de integridade. Por padrão, nenhum filho é incluído, em oposição aos comandos de integridade que sempre incluem filhos de primeiro nível.

As [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries) retornam apenas os filhos de primeiro nível da entidade especificada por filtros obrigatórios. Para obter os filhos dos filhos, você deve chamar APIs de integridade adicionais para cada entidade de interesse. Da mesma forma, para obter a integridade de entidades específicas, você deve chamar uma API de integridade para cada entidade desejada. A filtragem avançada de consulta de bloco permite que você solicite vários itens de interesse em uma consulta, minimizando o tamanho da mensagem e o número de mensagens.

O valor da consulta de bloco é que você pode obter o estado de integridade para mais entidades de cluster (potencialmente todas as entidades de cluster que começam na raiz necessária) em uma chamada. Você pode expressar uma consulta de integridade complexa, como:

* Retorne apenas aplicativos com erro e, para esses aplicativos, inclua todos os serviços em aviso ou erro. Para serviços retornados, inclua todas as partições.
* Retornar apenas a integridade de quatro aplicativos, especificados por seus nomes.
* Retornar apenas a integridade dos aplicativos de um tipo de aplicativo desejado.
* Retornar todas as entidades implantadas em um nó. Retorna todos os aplicativos, todos os aplicativos implantados no nó especificado e todos os pacotes de serviço implantados nesse nó.
* Retornar todas as réplicas com erro. Retorna todos os aplicativos, serviços, partições e somente réplicas com erro.
* Retornar todos os aplicativos. Para um serviço especificado, inclua todas as partições.

Atualmente, a consulta de parte de integridade é exposta apenas para a entidade de cluster. Ele retorna uma parte de integridade do cluster, que contém:

* O estado de integridade agregado do cluster.
* A lista de partes do estado de integridade dos nós que respeitam os filtros de entrada.
* A lista de partes de estado de integridade dos aplicativos que respeitam os filtros de entrada. Cada parte do estado de integridade do aplicativo contém uma lista de partes com todos os serviços que respeitam filtros de entrada e uma lista de partes com todos os aplicativos implantados que respeitam os filtros. O mesmo para os filhos de serviços e aplicativos implantados. Dessa forma, todas as entidades no cluster podem ser retornadas, se solicitadas, de maneira hierárquica.

### <a name="cluster-health-chunk-query"></a>Consulta de parte da integridade do cluster
Retorna a integridade da entidade de cluster e contém as partes de estado de integridade hierárquica dos filhos necessários. Entrada:

* Adicional A política de integridade do cluster usada para avaliar os nós e os eventos de cluster.
* Adicional O mapa da política de integridade do aplicativo, com as políticas de integridade usadas para substituir as políticas de manifesto do aplicativo.
* Adicional Filtros para nós e aplicativos que especificam quais entradas são interessantes e devem ser retornadas no resultado. Os filtros são específicos de uma entidade/grupo de entidades ou são aplicáveis a todas as entidades nesse nível. A lista de filtros pode conter um filtro geral e/ou filtros para identificadores específicos para entidades refinadas retornadas pela consulta. Se estiverem vazios, os filhos não serão retornados por padrão.
  Leia mais sobre os filtros em [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) e [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Os filtros de aplicativo podem especificar recursivamente os filtros avançados para os filhos.

O resultado da parte inclui os filhos que respeitam os filtros.

Atualmente, a consulta de bloco não retorna avaliações não íntegras ou eventos de entidade. Essas informações extras podem ser obtidas usando a consulta de integridade de cluster existente.

### <a name="api"></a>API
Para obter a parte da integridade do cluster `FabricClient` , crie um e chame o método [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) em seu **healthmanager**. Você pode passar [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) para descrever as políticas de integridade e os filtros avançados.

O código a seguir obtém a parte da integridade do cluster com filtros avançados.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do cluster é [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O código a seguir obtém nós somente se eles estiverem com erro, exceto para um nó específico, que sempre deve ser retornado.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

O cmdlet a seguir obtém a parte do cluster com filtros de aplicativo.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

O cmdlet a seguir retorna todas as entidades implantadas em um nó.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Você pode obter a parte da integridade do cluster com uma solicitação [Get](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) ou um [post](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) que inclui políticas de integridade e filtros avançados descritos no corpo.

## <a name="general-queries"></a>Consultas gerais
As consultas gerais retornam uma lista de entidades de Service Fabric de um tipo especificado. Eles são expostos por meio da API (por meio dos métodos em **FabricClient. querymanager**), dos cmdlets do PowerShell e do REST. Essas consultas agregam subconsultas de vários componentes. Um deles é o [repositório de integridade](service-fabric-health-introduction.md#health-store), que popula o estado de integridade agregado para cada resultado da consulta.  

> [!NOTE]
> As consultas gerais retornam o estado de integridade agregado da entidade e não contêm dados de integridade avançados. Se uma entidade não estiver íntegra, você poderá acompanhar as consultas de integridade para obter todas as suas informações de integridade, incluindo eventos, Estados de integridade filho e avaliações não íntegras.
>
>

Se as consultas gerais retornarem um estado de integridade desconhecido para uma entidade, é possível que o repositório de integridade não tenha dados completos sobre a entidade. Também é possível que uma subconsulta ao repositório de integridade não tenha êxito (por exemplo, houve um erro de comunicação ou o repositório de integridade foi limitado). Acompanhe com uma consulta de integridade para a entidade. Se a subconsulta encontrou erros transitórios, como problemas de rede, essa consulta de acompanhamento pode ter sucesso. Ele também pode fornecer mais detalhes do repositório de integridade sobre por que a entidade não está exposta.

As consultas que contêm **HealthState** para entidades são:

* Lista de nós: Retorna os nós de lista no cluster (paginado).
  * API [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista de aplicativos: Retorna a lista de aplicativos no cluster (paginável).
  * API [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista de serviços: Retorna a lista de serviços em um aplicativo (paginado).
  * API [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Lista de partições: Retorna a lista de partições em um serviço (paginado).
  * API [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Lista de réplicas: Retorna a lista de réplicas em uma partição (paginada).
  * API [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Lista de aplicativos implantados: Retorna a lista de aplicativos implantados em um nó.
  * API [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Lista de pacotes de serviço implantados: Retorna a lista de pacotes de serviço em um aplicativo implantado.
  * API [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Algumas das consultas retornam resultados paginados. O retorno dessas consultas é uma lista derivada de [\<PagedList T >](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Se os resultados não se ajustarem a uma mensagem, apenas uma página será retornada e um ContinuationToken que controla onde a enumeração foi interrompida. Continue a chamar a mesma consulta e passe o token de continuação da consulta anterior para obter os próximos resultados.

### <a name="examples"></a>Exemplos
O código a seguir obtém os aplicativos não íntegros no cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet a seguir obtém os detalhes do aplicativo para o aplicativo Fabric:/WordCount. Observe que o estado de integridade está em aviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

O cmdlet a seguir obtém os serviços com um estado de integridade de erro:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Atualizações de cluster e aplicativo
Durante uma atualização monitorada do cluster e do aplicativo, o Service Fabric verifica a integridade para garantir que tudo permaneça íntegro. Se uma entidade não estiver íntegra conforme avaliada usando políticas de integridade configuradas, a atualização aplicará políticas específicas de atualização para determinar a próxima ação. A atualização pode estar em pausa para permitir a interação do usuário (como corrigir condições de erro ou alterar políticas), ou pode ser revertida automaticamente para a versão válida anterior.

Durante uma atualização de *cluster* , você pode obter o status de atualização do cluster. O status de atualização inclui avaliações não íntegras, que apontam o que não está íntegro no cluster. Se a atualização for revertida devido a problemas de integridade, o status da atualização memorizará os últimos motivos não íntegros. Essas informações podem ajudar os administradores a investigar o que deu errado depois que a atualização foi revertida ou interrompida.

Da mesma forma, durante uma atualização de *aplicativo* , todas as avaliações não íntegras estão contidas no status de atualização do aplicativo.

O seguinte mostra o status de atualização do aplicativo para um aplicativo Fabric:/WordCount modificado. Um Watchdog relatou um erro em uma de suas réplicas. A atualização está sendo revertida porque as verificações de integridade não são respeitadas.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Leia mais sobre a [atualização do aplicativo Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Usar avaliações de integridade para solucionar problemas
Sempre que houver um problema com o cluster ou um aplicativo, examine a integridade do cluster ou do aplicativo para identificar o que está errado. As avaliações não íntegras fornecem detalhes sobre o que disparou o estado não íntegro atual. Se for necessário, você poderá fazer uma busca detalhada em entidades filho não íntegras para identificar a causa raiz.

Por exemplo, considere um aplicativo não íntegro porque há um relatório de erros em uma de suas réplicas. O cmdlet do PowerShell a seguir mostra as avaliações não íntegras:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Você pode examinar a réplica para obter mais informações:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> As avaliações não íntegras mostram o primeiro motivo pelo qual a entidade é avaliada como o estado de integridade atual. Pode haver vários outros eventos que disparam esse Estado, mas eles não são refletidos nas avaliações. Para obter mais informações, faça uma busca detalhada nas entidades de integridade para descobrir todos os relatórios não íntegros no cluster.
>
>

## <a name="next-steps"></a>Passos seguintes
[Use system health reports to troubleshoot (Utilizar relatórios de estado de funcionamento do sistema para resolver problemas)](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de integridade de Service Fabric personalizados](service-fabric-report-health.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização do aplicativo Service Fabric](service-fabric-application-upgrade.md)
