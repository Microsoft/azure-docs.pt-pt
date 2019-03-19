---
title: Estado de funcionamento agregado de como ver as entidades do Azure Service Fabric | Documentos da Microsoft
description: Descreve como consultar, ver e avaliar a integridade de agregados de entidades do Azure Service Fabric, através de consultas de estado de funcionamento e consultas gerais.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d2b2a772e1cab50b8bad91c6b102529dfb362166
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105221"
---
# <a name="view-service-fabric-health-reports"></a>Ver relatórios de estado de funcionamento do Service Fabric
O Azure Service Fabric introduz um [modelo de estado de funcionamento](service-fabric-health-introduction.md) com entidades de estado de funcionamento nas quais componentes de sistema e watchdogs pode relatório local condições que está a monitorizar. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) agrega todos os dados de estado de funcionamento para determinar se as entidades estão em bom estadas.

O cluster é preenchido automaticamente com os relatórios de estado de funcionamento enviados pelos componentes de sistema. Leia mais sobre [utilizar os relatórios de estado de funcionamento do sistema para resolver](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

O Service Fabric fornece várias formas de obter o estado de funcionamento agregado das entidades:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização
* Consultas de estado de funcionamento (através do PowerShell, API ou REST)
* Geral consulta ou retorno uma lista de entidades que tenham o estado de funcionamento como uma das propriedades (através do PowerShell, API ou REST)

Para demonstrar essas opções, vamos utilizar um cluster local com cinco nós e o [fabric: / WordCount aplicação](https://aka.ms/servicefabric-wordcountapp). O **fabric: / WordCount** aplicação contém dois serviços de predefinição, um serviço com monitorização de estado do tipo `WordCountServiceType`e um serviço sem estado do tipo `WordCountWebServiceType`. Eu Alterei o `ApplicationManifest.xml` para exigir a sete réplicas para o serviço com estado e uma partição de destino. Como há apenas cinco nós no cluster, os componentes de sistema reportam um aviso na partição de serviço porque está abaixo a contagem de destino.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Estado de funcionamento no Service Fabric Explorer
Service Fabric Explorer proporciona uma vista visual do cluster. Na imagem abaixo, pode ver que:

* O aplicativo **fabric: / WordCount** é vermelho (em erro), porque tem um evento de erro comunicado pelo **MyWatchdog** para a propriedade **disponibilidade**.
* Um dos seus serviços, **fabric: / WordCount/WordCountService** for amarelo (num aviso). O serviço está configurado com sete réplicas e o cluster com cinco nós, pelo que não não possível colocar duas réplicas. Apesar de não é apresentado aqui, a partição de serviço for amarela, devido a um relatório do sistema do `System.FM` dizendo que `Partition is below target replica or instance count`. A partição amarela aciona o serviço amarelo.
* O cluster é vermelho devido a aplicação de vermelha.

A avaliação utiliza políticas predefinidas do manifesto do cluster e o manifesto do aplicativo. Eles são diretivas rígidas e não tolerar qualquer falha.

Visão do cluster com o Service Fabric Explorer:

![Visão do cluster com o Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Leia mais sobre [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Consultas de estado de funcionamento
Service Fabric expõe consultas de estado de funcionamento para cada suportadas [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy). Podem ser acedidos através da API, através de métodos na [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), cmdlets do PowerShell e REST. Estas consultas devolvem informações de estado de funcionamento completo sobre a entidade: o estado de funcionamento agregado, eventos de estado de funcionamento da entidade, Estados de funcionamento do filho (quando aplicável), avaliações de mau estado de funcionamento (quando a entidade não está em bom estada) e as estatísticas de estado de funcionamento de filhos (quando aplicável).

> [!NOTE]
> Uma entidade de estado de funcionamento é retornada quando ele é totalmente preenchido no arquivo de estado de funcionamento. A entidade tem de estar ativo (não eliminado) e tem um relatório do sistema. Também tem de ter suas entidades pai na cadeia de hierarquia de relatórios do sistema. Se alguma dessas condições não estão cumpridas, o estado de funcionamento consulta devolver um [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) com [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` que mostra por que a entidade não é devolvida.
>
>

As consultas de estado de funcionamento têm de introduzir o identificador da entidade, que depende do tipo de entidade. As consultas de aceitar os parâmetros de política de estado de funcionamento opcionais. Se forem especificadas não existem políticas de estado de funcionamento, o [políticas de estado de funcionamento](service-fabric-health-introduction.md#health-policies) do manifesto do cluster ou aplicações são utilizadas para avaliação. Se os manifestos não contém uma definição para políticas de estado de funcionamento, as políticas de estado de funcionamento padrão são utilizadas para avaliação. As políticas de estado de funcionamento padrão não tolerar quaisquer falhas. As consultas também aceitam filtros para devolver apenas os filhos parciais ou eventos – aqueles que respeitam os filtros especificados. Outro filtro permite excluir as estatísticas de elementos subordinados.

> [!NOTE]
> Os filtros de saída são aplicados no lado do servidor, para que o tamanho de resposta de mensagem é reduzido. Recomendamos que utilize os filtros de saída para limitar os dados retornados, em vez de aplica os filtros no lado do cliente.
>
>

Estado de funcionamento da entidade contém:

* O estado de funcionamento agregado da entidade. Calculado pelo arquivo de estado de funcionamento com base em políticas de estado de funcionamento, Estados de funcionamento do filho (quando aplicável) e relatórios de estado de funcionamento da entidade. Leia mais sobre [avaliação de estado de funcionamento da entidade](service-fabric-health-introduction.md#health-evaluation).  
* Os eventos de estado de funcionamento na entidade.
* A coleção de Estados de funcionamento de todos os filhos para as entidades que podem ter filhos. Os Estados de funcionamento contém identificadores de entidade e o estado de funcionamento agregado. Para obter o estado de funcionamento completo para um elemento subordinado, o estado de funcionamento de consulta, ligue para o tipo de entidade subordinada e passamos o identificador de subordinados.
* As avaliações de mau estado de funcionamento que apontam para o relatório que disparou o estado da entidade, se a entidade não está em bom estada. As avaliações são recursiva, que contém as avaliações de estado de funcionamento de filhos que disparou o estado de funcionamento atual. Por exemplo, um watchdog comunicou um erro em relação a uma réplica. O estado de funcionamento do aplicativo mostra uma edição de avaliação de mau estado de funcionamento devido a um serviço em mau estado de funcionamento; o serviço está em mau estado de funcionamento devido a uma partição por engano; a partição está danificada devido a uma réplica num erro; a réplica está danificada devido ao relatório de estado de funcionamento de erro de watchdog.
* As estatísticas de estado de funcionamento para todos os tipos de elementos subordinados das entidades que tem elementos subordinados. Por exemplo, o estado de funcionamento do cluster mostra o número total de aplicações, serviços, partições, réplicas e implementar entidades do cluster. Estado de funcionamento do serviço mostra o número total de partições e réplicas em serviço especificado.

## <a name="get-cluster-health"></a>Obter o estado de funcionamento do cluster
Devolve o estado de funcionamento da entidade de cluster e contém os Estados de funcionamento de aplicativos e nós (filhos do cluster). Entrada:

* [Opcional] A política de estado de funcionamento do cluster utilizada para avaliar os nós e os eventos de cluster.
* [Opcional] O estado de funcionamento política mapa da aplicação, com as políticas de estado de funcionamento utilizada para substituir as políticas de manifesto do aplicativo.
* [Opcional] Filtros de eventos, nós e aplicações que especificam qual entradas são de interesse e devem ser devolvidas o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos, nós e aplicações são utilizadas para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.
* [Opcional] Filtro para excluir as estatísticas de estado de funcionamento.
* [Opcional] Filtro para incluir fabric: / estatísticas de estado de funcionamento do sistema nas estatísticas de estado de funcionamento. Apenas aplicável quando as estatísticas de estado de funcionamento não forem excluídas. Por predefinição, as estatísticas de estado de funcionamento incluem apenas as estatísticas de aplicativos de usuário e não a aplicação de sistema.

### <a name="api"></a>API
Para obter o estado de funcionamento do cluster, crie uma `FabricClient` e chamar o [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) método no seu **HealthManager**.

A seguinte chamada obtém o estado de funcionamento do cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

O código seguinte obtém o estado de funcionamento do cluster com uma política de estado de funcionamento de cluster personalizado e filtros para nós e aplicativos. Especifica que as estatísticas de estado de funcionamento incluem os recursos de infraestrutura: / estatísticas de sistema. Ele cria [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), que contém as informações de entrada.

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
O cmdlet para obter o estado de funcionamento do cluster é [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O estado do cluster é cinco nós, a aplicação de sistema e fabric: / WordCount configurada, tal como descrito.

O cmdlet seguinte obtém o estado de funcionamento do cluster ao utilizar políticas de estado de funcionamento padrão. O estado de funcionamento agregado é aviso, porque os recursos de infraestrutura: / aplicação de WordCount é num aviso. Observe como o das avaliações de mau estado de funcionamento fornecem detalhes sobre as condições que disparou o estado de funcionamento agregado.

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

O cmdlet PowerShell seguinte obtém o estado de funcionamento do cluster com uma política de aplicação personalizada. Filtra os resultados para obter apenas os aplicativos e nós no erro ou aviso. Como resultado, não existem nós são retornados, como eles estão todos em bom Estados. Apenas os recursos de infraestrutura: / WordCount aplicação respeita o filtro de aplicativos. Uma vez que a política personalizada Especifica a serem considerados avisos como erros nos recursos de infraestrutura: / WordCount aplicativo, o aplicativo é avaliado como em erro e é por isso, o cluster.

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
Pode obter o estado de funcionamento do cluster com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-node-health"></a>Obter o estado de funcionamento do nó
Devolve o estado de funcionamento de uma entidade de nó e contém os eventos de estado de funcionamento comunicados no nó. Entrada:

* [Necessário] O nome do nó que identifica o nó.
* [Opcional] As definições de política de estado de funcionamento cluster utilizadas para avaliar o estado de funcionamento.
* [Opcional] Filtros para os eventos que especificam qual entradas são de interesse e devem ser devolvidas o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento do nó por meio da API, crie uma `FabricClient` e chamar o [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) método no seu HealthManager.

O código seguinte obtém o estado de funcionamento do nó para o nome de nó especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

O código seguinte obtém o estado de funcionamento do nó para o nome de nó especificado e passa o filtro de eventos e uma política personalizada através de [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do nó é [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.
O cmdlet seguinte obtém o estado de funcionamento do nó ao utilizar políticas de estado de funcionamento padrão:

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

O cmdlet seguinte obtém o estado de funcionamento de todos os nós do cluster:

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
Pode obter o estado de funcionamento do nó com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-application-health"></a>Obter o estado de funcionamento da aplicação
Devolve o estado de funcionamento de uma entidade de aplicação. Contém os Estados de funcionamento da aplicação implementada e filhos de serviço. Entrada:

* [Necessário] O nome da aplicação (URI) que identifica a aplicação.
* [Opcional] A política de estado de funcionamento de aplicações utilizada para substituir as políticas de manifesto do aplicativo.
* [Opcional] Filtros para eventos, serviços e aplicações implementadas que especificam as entradas são de interesse e devem ser devolvidos o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos, serviços e aplicações implementadas são utilizadas para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.
* [Opcional] Filtro para excluir as estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento incluem o ok, aviso e contagem de erros para todos os filhos de aplicação: serviços, partições, as réplicas, aplicativos implantados e implementar pacotes de serviço.

### <a name="api"></a>API
Para obter o estado de funcionamento do aplicativo, criar um `FabricClient` e chamar o [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) método no seu HealthManager.

O código seguinte obtém o estado de funcionamento do aplicativo para o nome da aplicação especificado (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

O código seguinte obtém o estado de funcionamento do aplicativo para o nome da aplicação especificado (URI), com filtros e as políticas personalizadas especificado [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
O cmdlet para obter o estado de funcionamento do aplicativo é [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O seguinte cmdlet devolve o estado de funcionamento da **fabric: / WordCount** aplicação:

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

O seguinte cmdlet do PowerShell passa as políticas personalizadas. Filtra também eventos e subordinados.

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
Pode obter o estado de funcionamento de aplicação com um [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-service-health"></a>Obter o estado de funcionamento do serviço
Devolve o estado de funcionamento de uma entidade de serviço. Contém os Estados de funcionamento de partição. Entrada:

* [Necessário] O nome do serviço (URI) que identifica o serviço.
* [Opcional] A política de estado de funcionamento de aplicações utilizada para substituir a política de manifesto do aplicativo.
* [Opcional] Filtros de eventos e as partições que especificam qual entradas são de interesse e devem ser devolvidas o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos e as partições são utilizadas para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.
* [Opcional] Filtro para excluir as estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento mostram ok, aviso, e contagem de erros para todas as partições e réplicas do serviço.

### <a name="api"></a>API
Para obter o estado de funcionamento do serviço por meio da API, crie uma `FabricClient` e chamar o [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) método no seu HealthManager.

O exemplo seguinte obtém o estado de funcionamento de um serviço com o nome de serviço especificado (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

O código seguinte obtém o estado de funcionamento do serviço o nome de serviço especificado (URI), especificando os filtros e a política personalizada através de [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do serviço é [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O cmdlet seguinte obtém o estado de funcionamento do serviço ao utilizar políticas de estado de funcionamento padrão:

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
Pode obter o estado de funcionamento do serviço com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-partition-health"></a>Obter o estado de funcionamento de partição
Devolve o estado de funcionamento de uma entidade de partição. Contém os Estados de funcionamento de réplica. Entrada:

* [Necessário] A ID (GUID) que identifica a partição de partição.
* [Opcional] A política de estado de funcionamento de aplicações utilizada para substituir a política de manifesto do aplicativo.
* [Opcional] Filtros de eventos e as réplicas que especificam qual entradas são de interesse e devem ser devolvidas o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos e as réplicas são utilizadas para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.
* [Opcional] Filtro para excluir as estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento mostram o número de réplicas em ok, aviso e erro Estados.

### <a name="api"></a>API
Para obter o estado de funcionamento de partição através da API, crie uma `FabricClient` e chamar o [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) método no seu HealthManager. Para especificar os parâmetros opcionais, crie [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento de partição é [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O cmdlet seguinte obtém o estado de funcionamento para todas as partições do **fabric: / WordCount/WordCountService** serviço e filtra os Estados de funcionamento de réplica:

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
Pode obter o estado de funcionamento de partição com um [solicitação GET](/rest/api/servicefabric/sfclient-api-getpartitionhealth) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-replica-health"></a>Obter o estado de funcionamento de réplica
Devolve o estado de funcionamento de uma réplica de serviço com estado ou uma instância de serviço sem estado. Entrada:

* [Necessário] O ID de ID (GUID) e de réplica da partição que identifica a réplica.
* [Opcional] Os parâmetros de política de estado de funcionamento aplicação utilizados para substituir as políticas de manifesto do aplicativo.
* [Opcional] Filtros para os eventos que especificam qual entradas são de interesse e devem ser devolvidas o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento de réplica por meio da API, crie uma `FabricClient` e chamar o [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) método no seu HealthManager. Para especificar parâmetros avançados, utilize [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento de réplica é [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O cmdlet seguinte obtém o estado de funcionamento a réplica primária para todas as partições do serviço:

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
Pode obter o estado de funcionamento de réplica com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-deployed-application-health"></a>Obter o estado de funcionamento da aplicação implementada
Devolve o estado de funcionamento de um aplicativo implantado numa entidade de nó. Contém os Estados de funcionamento do pacote de serviço implementado. Entrada:

* [Necessário] O nome da aplicação (URI) e o nome de nó (cadeia) que identificam a aplicação implementada.
* [Opcional] A política de estado de funcionamento de aplicações utilizada para substituir as políticas de manifesto do aplicativo.
* [Opcional] Filtros de eventos e pacotes de serviços implementados que especificam as entradas são de interesse e devem ser devolvidas o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos e pacotes de serviços implementados são utilizados para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.
* [Opcional] Filtro para excluir as estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento mostram o número de pacotes de serviços implementados em ok, aviso e erro Estados de funcionamento.

### <a name="api"></a>API
Para obter o estado de funcionamento de um aplicativo implantado num nó por meio da API, crie uma `FabricClient` e chamar o [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) método no seu HealthManager. Para especificar os parâmetros opcionais, utilize [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento da aplicação implementada é [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. Para saber em que uma aplicação é implementada, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e examinar os filhos de aplicação implementada.

O cmdlet seguinte obtém o estado de funcionamento da **fabric: / WordCount** aplicação implementada no **_Node_2**.

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
Pode obter o estado de funcionamento da aplicação implementada com um [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-deployed-service-package-health"></a>Obter o estado de funcionamento do serviço implementado pacote
Devolve o estado de funcionamento de uma entidade de pacote de serviço implementado. Entrada:

* [Necessário] O nome da aplicação (URI), o nome de nó (cadeia) e nome do manifesto do serviço (cadeia) que identificam o pacote de serviço implementado.
* [Opcional] A política de estado de funcionamento de aplicações utilizada para substituir a política de manifesto do aplicativo.
* [Opcional] Filtros para os eventos que especificam qual entradas são de interesse e devem ser devolvidas o resultado (por exemplo, apenas, erros ou avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento agregado de entidades, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento de um pacote de serviço implementado por meio da API, crie uma `FabricClient` e chamar o [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) método no seu HealthManager. Para especificar os parâmetros opcionais, utilize [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do pacote de serviço implementado é [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. Para ver onde uma aplicação é implementada, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e examinar as aplicações implementadas. Para ver qual pacotes estão num aplicativo de serviço, observe os filhos de pacote de serviço implementado no [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) saída.

O cmdlet seguinte obtém o estado de funcionamento a **WordCountServicePkg** pacote de serviço da **fabric: / WordCount** aplicação implementada no **_Node_2**. A entidade tem **System.Hosting** relatórios para ativação com êxito do pacote de serviço e o ponto de entrada e de registo do tipo de serviço com êxito.

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
Pode obter o estado de funcionamento do serviço implementado pacote com um [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="health-chunk-queries"></a>Consultas de segmentos de estado de funcionamento
As consultas de segmentos de estado de funcionamento podem retornar os filhos de cluster de vários níveis (recursivamente), por filtros de entrada. Ele oferece suporte a filtros avançados que permitem uma grande flexibilidade na escolha os filhos a serem retornados. Os filtros podem especificar filhos ao identificador exclusivo ou outros identificadores de grupo e/ou os Estados de funcionamento. Por predefinição, sem subordinados estão incluídos, em vez de comandos de estado de funcionamento que incluem sempre o primeiro nível filhos.

O [consultas de estado de funcionamento](service-fabric-view-entities-aggregated-health.md#health-queries) devolver apenas os filhos de primeiro nível da entidade especificada por filtros necessários. Para obter os subordinados dos filhos, deve chamar as APIs de estado de funcionamento adicional para cada entidade de interesse. Da mesma forma, para obter o estado de funcionamento de entidades específicas, deve chamar um Estado de funcionamento API para cada entidade pretendida. A consulta de segmento filtragem avançada permite-lhe pedir vários itens de interesse numa consulta, minimizar o tamanho da mensagem e o número de mensagens.

O valor da consulta de segmento é que pode obter estado de funcionamento para obter mais entidades de cluster (potencialmente todos os clusters entidades a partir de raiz necessário) numa chamada. Pode expressar consulta de estado de funcionamento complexas, tais como:

* Retornados apenas aplicativos num erro de e para esses aplicativos incluem todos os serviços no aviso ou erro. Para os serviços retornados, incluem todas as partições.
* Devolva apenas o estado de funcionamento das aplicações de quatro, especificado pelo respetivos nomes.
* Devolva apenas o estado de funcionamento das aplicações de um tipo de aplicativo desejado.
* Devolve entidades todas implementadas num nó. Devolve todos os aplicativos, implementadas aplicativos no nó especificado e todos os pacotes de serviço implementado nesse nó.
* Devolve todas as réplicas no erro. Devolve todas as aplicações, serviços, partições e réplicas apenas num erro.
* Devolva todos os aplicativos. Para um serviço especificado, incluem todas as partições.

Atualmente, a consulta de segmento de estado de funcionamento é exposta apenas para a entidade de cluster. Devolve um segmento de estado de funcionamento do cluster, que contém:

* O estado de funcionamento do cluster agregado.
* Lista segmentos de estado de funcionamento estado de nós que respeitam os filtros de entrada.
* Lista segmentos de estado de funcionamento estado das aplicações que respeitam os filtros de entrada. Cada segmento de estado de funcionamento do aplicativo contém uma lista de segmentos com todos os serviços que respeitam filtros de entrada e uma lista de segmentos com todas as aplicações implementadas que respeitam os filtros. Mesmo para os filhos de serviços e aplicações implementadas. Dessa forma, todas as entidades do cluster podem ser potencialmente devolvidas se solicitado, de uma forma hierárquica.

### <a name="cluster-health-chunk-query"></a>Consulta de segmento de estado de funcionamento do cluster
Devolve o estado de funcionamento da entidade de cluster e contém os segmentos de estado de estado de funcionamento hierárquica filhos necessária. Entrada:

* [Opcional] A política de estado de funcionamento do cluster utilizada para avaliar os nós e os eventos de cluster.
* [Opcional] O estado de funcionamento política mapa da aplicação, com as políticas de estado de funcionamento utilizada para substituir as políticas de manifesto do aplicativo.
* [Opcional] Filtros para nós e os aplicativos que especificam as entradas são de interesse e devem ser devolvidas no resultado. Os filtros são específicos para um entidade/grupo de entidades ou são aplicáveis a todas as entidades nesse nível. A lista de filtros pode conter um gerais de filtro de e/ou filtros para os identificadores específicos às entidades refinado devolvidas pela consulta. Se estiver vazio, os filhos não são devolvidos por predefinição.
  Saiba mais sobre os filtros na [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) e [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). O recursivamente do aplicativo filtros podem especificar filtros avançados para crianças.

O resultado de segmentos inclui as crianças que respeitam os filtros.

Atualmente, a consulta de segmento não devolve avaliações de mau estado de funcionamento ou eventos de entidade. Que informações adicionais podem ser obtidas com a consulta de estado de funcionamento do cluster existente.

### <a name="api"></a>API
Para obter o segmento de estado de funcionamento do cluster, crie uma `FabricClient` e chamar o [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) método no seu **HealthManager**. Pode passar [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) para descrever as políticas de estado de funcionamento e filtros avançados.

O código seguinte obtém os segmentos de estado de funcionamento do cluster com filtros avançados.

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
O cmdlet para obter o estado de funcionamento do cluster é [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Em primeiro lugar, ligue ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O código seguinte obtém nós apenas se estiverem num erro, exceto para um nó específico, o que sempre deve ser devolvido.

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

O cmdlet seguinte obtém os segmentos de cluster com os filtros de aplicativo.

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

O seguinte cmdlet devolve todas as entidades implementadas num nó.

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
Pode obter o segmento de estado de funcionamento do cluster com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) ou uma [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) que inclui políticas de estado de funcionamento e filtros avançados descritos no corpo.

## <a name="general-queries"></a>Consultas gerais
Consultas gerais retornam uma lista de entidades do Service Fabric de um tipo especificado. Elas são expostas por meio da API (por meio de métodos **FabricClient.QueryManager**), cmdlets do PowerShell e REST. Estas consultas agregam subconsultas de vários componentes. Uma delas é o [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store), que preenche o estado de funcionamento agregado para cada resultado da consulta.  

> [!NOTE]
> Consultas gerais devolvem o estado de funcionamento agregado da entidade e não contêm dados de estado de funcionamento avançado. Se uma entidade não está em bom estada, pode dar seguimento com consultas de estado de funcionamento para obter todas as suas informações de estado de funcionamento, incluindo eventos, Estados de funcionamento do filho e avaliações de mau estado de funcionamento.
>
>

Se as consultas gerais retornam um Estado de funcionamento desconhecido para uma entidade, é possível que o arquivo de estado de funcionamento não tem dados completos sobre a entidade. Também é possível que uma subconsulta para o arquivo de estado de funcionamento não foi concluída com êxito (por exemplo, Ocorreu um erro de comunicação ou o arquivo de estado de funcionamento foi limitado). Acompanhe com uma consulta de estado de funcionamento da entidade. Se a subconsulta encontrou erros transitórios, tais como problemas de rede, esta consulta seguimento pode ter êxito. Ele pode também fornecer mais detalhes do arquivo de estado de funcionamento sobre por que a entidade não está exposta.

As consultas que contêm **HealthState** para entidades são:

* Lista de nós: Devolve os nós de lista do cluster (paginada).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista de aplicativos: Devolve a lista de aplicações no cluster (paginado).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista de serviço: Devolve a lista de serviços num aplicativo (paginado).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Lista de partição: Devolve a lista de partições num serviço (paginado).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Lista de réplica: Devolve a lista de réplicas numa partição (paginada).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Lista de aplicação implementada: Devolve a lista de aplicações implementadas num nó.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Lista de pacotes de serviço implementado: Devolve a lista de pacotes de serviço num aplicativo implantado.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Algumas das consultas devolvem resultados paginados. O retorno dessas consultas é uma lista derivada [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Se os resultados não se encaixam uma mensagem, apenas uma página é devolvida e uma ContinuationToken que controla onde parou a enumeração. Continue chamar a mesma consulta e transmite o token de continuação da consulta anterior para obter resultados seguintes.

### <a name="examples"></a>Exemplos
O código seguinte obtém as aplicações em mau estado de funcionamento do cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet seguinte obtém os detalhes da aplicação para os recursos de infraestrutura: / aplicação WordCount. Tenha em atenção que o estado de funcionamento é no aviso.

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

O cmdlet seguinte obtém os serviços com um Estado de funcionamento de erro:

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

## <a name="cluster-and-application-upgrades"></a>Atualizações de aplicações e clusters
Durante uma atualização monitorizada do cluster e da aplicação e recursos de infraestrutura do serviço verifica o estado de funcionamento para garantir que tudo o que permanece em bom estado. Se uma entidade está danificada, avaliada através de políticas de estado de funcionamento configurado, a atualização se aplica a políticas de atualização específicas para determinar a ação seguinte. A atualização pode ser colocada em pausa para permitir a interação do usuário (como corrigir condições de erro ou alteração de políticas), ou pode automaticamente revertê-lo para a versão anterior.

Durante uma *cluster* atualização, pode obter o estado de atualização do cluster. O estado de atualização inclui avaliações de mau estado de funcionamento, que apontem para o que é mau estado de funcionamento do cluster. Se a atualização é revertida devido a problemas de estado de funcionamento, o estado de atualização se lembra os última motivos mau estado de funcionamento. Essas informações podem ajudar os administradores a investigar o que correu mal, após a atualização revertida ou parado.

Da mesma forma, durante um *aplicativo* atualização, qualquer avaliações de mau estado de funcionamento estão contidas no estado de atualização de aplicação.

O seguinte mostra o estado de atualização de aplicação para um recurso de infraestrutura modificado: / aplicação WordCount. Um watchdog comunicou um erro das suas réplicas. A atualização está a ser porque as verificações de estado de funcionamento não estão a ser respeitadas.

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

Leia mais sobre o [atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Utilizar avaliações de estado de funcionamento para resolver problemas
Sempre que houver um problema com o cluster ou de uma aplicação, ver o estado de funcionamento do cluster ou aplicação para identificar o que está errado. As avaliações de mau estado de funcionamento fornecem detalhes sobre o que acionou o estado de mau estado de funcionamento atual. Se for necessário, pode desagregar em entidades de subordinados danificados para identificar a causa raiz.

Por exemplo, considere um aplicativo mau estado de funcionamento porque não existe um relatório de erros em uma das suas réplicas. O cmdlet Powershell seguinte mostra as avaliações de mau estado de funcionamento:

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

Pode ver a réplica para obter mais informações:

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
> As avaliações de mau estado de funcionamento mostram que o primeiro motivo a entidade é avaliado para o estado de funcionamento atual. Pode haver vários outros eventos que disparam neste estado, mas eles não são refletidas em das avaliações. Para obter mais informações, desagregar para as entidades de estado de funcionamento para descobrir todos os relatórios de mau estado de funcionamento do cluster.
>
>

## <a name="next-steps"></a>Passos Seguintes
[Use system health reports to troubleshoot (Utilizar relatórios de estado de funcionamento do sistema para resolver problemas)](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de estado de funcionamento personalizados do Service Fabric](service-fabric-report-health.md)

[Como comunicar e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)
