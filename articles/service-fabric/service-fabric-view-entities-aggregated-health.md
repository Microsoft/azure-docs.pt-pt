---
title: Como ver a saúde agregada das entidades do Azure Service Fabric
description: Descreve como consultar, visualizar e avaliar a saúde agregada das entidades do Azure Service Fabric, através de consultas de saúde e consultas gerais.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: eeb2fd94e6b98bc9d89be22501406db9a8ba7773
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013168"
---
# <a name="view-service-fabric-health-reports"></a>Ver relatórios de saúde do Service Fabric
A Azure Service Fabric introduz um [modelo de saúde](service-fabric-health-introduction.md) com entidades de saúde sobre as quais componentes do sistema e cães de guarda podem reportar as condições locais que estão a monitorizar. A [loja de saúde](service-fabric-health-introduction.md#health-store) agrega todos os dados de saúde para determinar se as entidades são saudáveis.

O cluster é automaticamente povoado com relatórios de saúde enviados pelos componentes do sistema. Leia mais na [Use system health reports to troubleshoot](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

A Service Fabric oferece múltiplas formas de obter a saúde agregada das entidades:

* [Explorador de Tecidos de Serviço](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização
* Consultas de saúde (através de PowerShell, API ou REST)
* Consultas gerais que devolvem uma lista de entidades que têm saúde como uma das propriedades (através de PowerShell, API ou REST)

Para demonstrar estas opções, vamos utilizar um cluster local com cinco nós e a [aplicação tecido:/WordCount](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). A aplicação **do tecido:/WordCount** contém dois serviços predefinidos, um serviço de tipo stateful, `WordCountServiceType` e um serviço apátrida de tipo `WordCountWebServiceType` . Mudei o `ApplicationManifest.xml` para exigir sete réplicas de alvo para o serviço estatal e uma divisória. Como existem apenas cinco nós no cluster, os componentes do sistema reportam um aviso sobre a partição de serviço porque está abaixo da contagem de alvos.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Health in Service Fabric Explorer
O Service Fabric Explorer proporciona uma visão visual do cluster. Na imagem abaixo, pode ver que:

* O **tecido da aplicação:/WordCount** é vermelho (por engano) porque tem um evento de erro reportado pela **MyWatchdog** para a **disponibilidade**da propriedade .
* Um dos seus serviços, **tecido:/WordCount/WordCountService** é amarelo (em aviso). O serviço está configurado com sete réplicas e o cluster tem cinco nós, pelo que duas réplicas não podem ser colocadas. Embora não seja mostrado aqui, a divisão de serviço é amarela por causa de um relatório do sistema `System.FM` dizendo que `Partition is below target replica or instance count` . A divisória amarela aciona o serviço amarelo.
* O aglomerado é vermelho por causa da aplicação vermelha.

A avaliação utiliza políticas predefinidos do manifesto de cluster e manifesto de aplicação. São políticas rigorosas e não toleram qualquer falha.

Vista do cluster com Explorador de Tecido de Serviço:

![Vista do cluster com Explorador de Tecido de Serviço.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Leia mais sobre [o Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md)
>
>

## <a name="health-queries"></a>Consultas de saúde
A Service Fabric expõe consultas de saúde para cada um dos tipos de [entidades](service-fabric-health-introduction.md#health-entities-and-hierarchy)suportadas. Podem ser acedidos através da API, utilizando métodos em [FabricClient.HealthManager,](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet)PowerShell cmdlets e REST. Estas consultas devolvem informações completas sobre a saúde sobre a entidade: o estado de saúde agregado, os eventos de saúde das entidades, os estados de saúde infantil (quando aplicável), as avaliações pouco saudáveis (quando a entidade não é saudável) e as estatísticas de saúde das crianças (quando aplicável).

> [!NOTE]
> Uma entidade de saúde é devolvida quando está totalmente povoada na loja de saúde. A entidade deve estar ativa (não eliminada) e ter um relatório do sistema. As suas entidades-mãe na cadeia hierarquia também devem ter relatórios do sistema. Se alguma destas condições não estiver satisfeita, as consultas de saúde devolvem uma [FabricException](/dotnet/api/system.fabric.fabricexception) com [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` que mostra porque é que a entidade não é devolvida.
>
>

As consultas de saúde devem passar no identificador de entidades, que depende do tipo de entidade. As consultas aceitam parâmetros de política de saúde opcionais. Se não forem especificadas políticas de [saúde,](service-fabric-health-introduction.md#health-policies) as políticas de saúde do cluster ou manifesto de aplicação são utilizadas para avaliação. Se os manifestos não contiverem uma definição para políticas de saúde, as políticas de saúde padrão são usadas para avaliação. As políticas de saúde padrão não toleram falhas. As consultas também aceitam filtros para devolver apenas crianças ou eventos parciais - os que respeitam os filtros especificados. Outro filtro permite excluir as estatísticas das crianças.

> [!NOTE]
> Os filtros de saída são aplicados no lado do servidor, de modo que o tamanho da resposta da mensagem é reduzido. Recomendamos que utilize os filtros de saída para limitar os dados devolvidos, em vez de aplicar filtros do lado do cliente.
>
>

A saúde de uma entidade contém:

* O estado de saúde agregado da entidade. Calculada pela loja de saúde com base em relatórios de saúde de entidades, estados de saúde infantil (quando aplicável) e políticas de saúde. Leia mais sobre [a avaliação de saúde da entidade.](service-fabric-health-introduction.md#health-evaluation)  
* Os eventos de saúde na entidade.
* A recolha de estados de saúde de todas as crianças para as entidades que podem ter filhos. Os estados de saúde contêm identificadores de entidades e o estado de saúde agregado. Para obter uma saúde completa para uma criança, chame a saúde da consulta para o tipo de entidade infantil e passe no identificador da criança.
* As avaliações pouco saudáveis que apontam para o relatório que desencadeou o estado da entidade, se a entidade não for saudável. As avaliações são recursivas, contendo as avaliações de saúde das crianças que desencadearam o estado atual de saúde. Por exemplo, um cão de guarda relatou um erro contra uma réplica. A saúde da aplicação mostra uma avaliação pouco saudável devido a um serviço pouco saudável; o serviço não é saudável devido a uma partição por engano; a partição não é saudável devido a uma réplica de erro; a réplica não é saudável devido ao relatório de saúde do cão de guarda.
* As estatísticas de saúde de todas as crianças das entidades que têm filhos. Por exemplo, a saúde do cluster mostra o número total de aplicações, serviços, divisórias, réplicas e entidades implantadas no cluster. A saúde do serviço mostra o número total de divisórias e réplicas no âmbito do serviço especificado.

## <a name="get-cluster-health"></a>Obtenha saúde de cluster
Devolve a saúde da entidade do cluster e contém os estados de saúde das aplicações e dos nóns (crianças do agrupamento). Entrada:

* [Opcional] A política de saúde do cluster usada para avaliar os nós e os eventos de cluster.
* [Opcional] O mapa da política de saúde da aplicação, com as políticas de saúde usadas para anular as políticas de manifesto de aplicação.
* [Opcional] Filtros para eventos, nós e aplicações que especificam quais as entradas que são de interesse e devem ser devolvidas no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos, nós e aplicações são usados para avaliar a saúde agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir estatísticas de saúde.
* [Opcional] Filtro para incluir estatísticas de saúde do tecido:/Estatísticas de saúde do sistema nas estatísticas de saúde. Só é aplicável quando as estatísticas de saúde não forem excluídas. Por padrão, as estatísticas de saúde incluem apenas estatísticas para aplicações de utilizadores e não a aplicação do Sistema.

### <a name="api"></a>API
Para obter saúde do cluster, crie um `FabricClient` e chame o método [GetClusterHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) no seu **HealthManager**.

A seguinte chamada recebe a saúde do cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

O seguinte código obtém a saúde do cluster utilizando uma política de saúde personalizada do cluster e filtros para nós e aplicações. Especifica que as estatísticas de saúde incluem as estatísticas do tecido:/Sistema. Cria [clusterHealthQueryDescription,](/dotnet/api/system.fabric.description.clusterhealthquerydescription)que contém a informação de entrada.

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
O cmdlet para obter a saúde do cluster é [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

O estado do cluster é de cinco nós, a aplicação do sistema e o tecido:/WordCount configurado como descrito.

O cmdlet seguinte obtém a saúde do cluster utilizando políticas de saúde padrão. O estado de saúde agregado é um aviso, porque a aplicação do tecido:/WordCount está em alerta. Note como as avaliações pouco saudáveis fornecem detalhes sobre as condições que desencadearam a saúde agregada.

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

O cmdlet PowerShell seguinte obtém a saúde do cluster utilizando uma política de aplicação personalizada. Filtra os resultados para obter apenas aplicações e nós por engano ou aviso. Como resultado, não são devolvidos nós, pois todos são saudáveis. Apenas o tecido:/Aplicação WordCount respeita o filtro de aplicações. Como a política personalizada especifica considerar os avisos como erros para a aplicação tecido:/WordCount, a aplicação é avaliada como erro, assim como o cluster.

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
Você pode obter saúde do cluster com um [pedido GET](/rest/api/servicefabric/get-the-health-of-a-cluster) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) QUE inclua políticas de saúde descritas no corpo.

## <a name="get-node-health"></a>Obter saúde de nó
Devolve a saúde de uma entidade nólad unida e contém os eventos de saúde relatados no nó. Entrada:

* [Obrigatório] O nome do nó que identifica o nó.
* [Opcional] As definições de política de saúde do cluster usadas para avaliar a saúde.
* [Opcional] Filtros para eventos que especifiquem quais as entradas que são de interesse e devem ser devolvidos no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos são usados para avaliar a saúde agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a saúde do nó através da API, crie um `FabricClient` e chame o método [GetNodeHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) no seu HealthManager.

O seguinte código obtém a saúde do nó para o nome do nó especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

O seguinte código obtém a saúde do nó para o nome do nó especificado e passa em filtragem de eventos e política personalizada através do [NodeHealthQueryDescription](/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a saúde do nó é [Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)
O cmdlet seguinte obtém a saúde do nó utilizando políticas de saúde predefinidos:

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

O seguinte cmdlet obtém a saúde de todos os nós do cluster:

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
Você pode obter saúde do nó com um [pedido GET](/rest/api/servicefabric/get-the-health-of-a-node) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) QUE inclua políticas de saúde descritas no corpo.

## <a name="get-application-health"></a>Obtenha saúde de aplicação
Devolve a saúde de uma entidade de aplicação. Contém os estados de saúde das crianças de aplicação e serviço implantados. Entrada:

* [Obrigatório] O nome da aplicação (URI) que identifica o pedido.
* [Opcional] A política de saúde da aplicação usada para anular as políticas de manifesto de aplicação.
* [Opcional] Filtros para eventos, serviços e aplicações implementadas que especificam quais as entradas que são de interesse e devem ser devolvidas no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos, serviços e aplicações implementadas são utilizados para avaliar a saúde agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir as estatísticas de saúde. Se não forem especificadas, as estatísticas de saúde incluem a contagem de erros ok, aviso e erro para todas as crianças de aplicação: serviços, divisórias, réplicas, aplicações implementadas e pacotes de serviço implantados.

### <a name="api"></a>API
Para obter saúde da aplicação, crie `FabricClient` um e ligue para o método [GetApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) no seu HealthManager.

O seguinte código obtém a saúde da aplicação para o nome de aplicação especificado (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

O código seguinte obtém a saúde da aplicação para o nome de aplicação especificado (URI), com filtros e políticas personalizadas especificadas através da [AplicaçãoHealthQueryDescription](/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
O cmdlet para obter a saúde da aplicação é [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

O cmdlet seguinte devolve a saúde do **tecido:/Aplicação WordCount:**

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

O cmdlet PowerShell seguinte passa em políticas personalizadas. Também filtra crianças e eventos.

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
Você pode obter saúde de aplicação com um [pedido GET](/rest/api/servicefabric/get-the-health-of-an-application) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) QUE inclua políticas de saúde descritas no organismo.

## <a name="get-service-health"></a>Obter saúde de serviço
Devolve a saúde de uma entidade de serviço. Contém os estados de saúde da partição. Entrada:

* [Obrigatório] O nome de serviço (URI) que identifica o serviço.
* [Opcional] A política de saúde da aplicação usada para anular a política de manifesto de aplicação.
* [Opcional] Filtros para eventos e divisórias que especifiquem quais as entradas que são de interesse e devem ser devolvidas no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos e divisórias são usados para avaliar a saúde agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir estatísticas de saúde. Se não for especificado, as estatísticas de saúde mostram a contagem de erros e ok para todas as divisórias e réplicas do serviço.

### <a name="api"></a>API
Para obter a saúde do serviço através da API, crie um `FabricClient` e ligue para o método [GetServiceHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) no seu HealthManager.

O exemplo a seguir obtém a saúde de um serviço com nome de serviço especificado (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

O seguinte código obtém a saúde do serviço para o nome de serviço especificado (URI), especificando filtros e política personalizada através do [ServiceHealthQueryDescription](/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a saúde do serviço é [Get-ServiceFabricServiceHealth](/powershell/module/servicefabric/get-servicefabricservicehealth). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

O seguinte cmdlet obtém a saúde do serviço utilizando políticas de saúde predefinidos:

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
Você pode obter saúde de serviço com um [pedido GET](/rest/api/servicefabric/get-the-health-of-a-service) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) QUE inclua políticas de saúde descritas no corpo.

## <a name="get-partition-health"></a>Obtenha saúde de partição
Devolve a saúde de uma entidade de partição. Contém os estados de saúde réplica. Entrada:

* [Obrigatório] O ID de partição (GUID) que identifica a partição.
* [Opcional] A política de saúde da aplicação usada para anular a política de manifesto de aplicação.
* [Opcional] Filtros para eventos e réplicas que especificam quais as entradas que são de interesse e devem ser devolvidas no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos e réplicas são usados para avaliar a saúde agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir estatísticas de saúde. Se não for especificado, as estatísticas de saúde mostram quantas réplicas estão em estados ok, alerta e erro.

### <a name="api"></a>API
Para obter a saúde da partição através da API, crie um `FabricClient` e chame o método [GetPartitionHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) no seu HealthManager. Para especificar parâmetros opcionais, crie [PartitionHealthQueryDescription](/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a saúde da partição é [Get-ServiceFabricPartitionHealth](/powershell/module/servicefabric/get-servicefabricpartitionhealth). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

O cmdlet seguinte obtém a saúde para todas as divisórias do **tecido:/Serviço WordCount/WordCountService** e filtra estados de saúde réplicas:

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
Você pode obter saúde de partição com um [pedido GET](/rest/api/servicefabric/sfclient-api-getpartitionhealth) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) QUE inclua políticas de saúde descritas no corpo.

## <a name="get-replica-health"></a>Obtenha a saúde da réplica
Devolve a saúde de uma réplica de serviço estatal ou de uma instância de serviço apátrida. Entrada:

* [Obrigatório] O ID de partição (GUID) e o ID de réplica que identifica a réplica.
* [Opcional] Os parâmetros da política de saúde da aplicação usados para anular as políticas de manifesto de aplicação.
* [Opcional] Filtros para eventos que especifiquem quais as entradas que são de interesse e devem ser devolvidos no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos são usados para avaliar a saúde agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a saúde réplica através da API, crie um `FabricClient` e chame o método [GetReplicaHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) no seu HealthManager. Para especificar parâmetros avançados, utilize [replicaHealthQueryDescription](/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a saúde da réplica é [Get-ServiceFabricReplicaHealth](/powershell/module/servicefabric/get-servicefabricreplicahealth). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

O seguinte cmdlet obtém a saúde da réplica primária para todas as divisórias do serviço:

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
Você pode obter saúde réplica com um [pedido GET](/rest/api/servicefabric/get-the-health-of-a-replica) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) QUE inclua políticas de saúde descritas no corpo.

## <a name="get-deployed-application-health"></a>Obtenha a saúde da aplicação implementada
Devolve a saúde de uma aplicação implantada numa entidade nód particular. Contém os estados de saúde do pacote de serviços implantados. Entrada:

* [Obrigatório] O nome da aplicação (URI) e o nome do nó (cadeia) que identificam a aplicação implementada.
* [Opcional] A política de saúde da aplicação usada para anular as políticas de manifesto de aplicação.
* [Opcional] Filtros para eventos e pacotes de serviço implantados que especificam quais as entradas são de interesse e devem ser devolvidos no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos e pacotes de serviços implantados são utilizados para avaliar a saúde agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir estatísticas de saúde. Se não forem especificados, as estatísticas de saúde mostram o número de pacotes de serviços implantados em estados de saúde ok, alerta e erro.

### <a name="api"></a>API
Para obter a saúde de uma aplicação implantada num nó através da API, crie um `FabricClient` e chame o método [GetDeployedApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) no seu HealthManager. Para especificar parâmetros opcionais, utilize [ImplementaçãoApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a saúde da aplicação implementada é [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Para saber onde é implementada uma aplicação, gere o [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e olha para as crianças da aplicação implementadas.

O cmdlet seguinte obtém a saúde do **tecido:/Aplicação WordCount** implantada em **_Node_2**.

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
Pode obter saúde de aplicação implantada com um [pedido GET](/rest/api/servicefabric/get-the-health-of-a-deployed-application) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) QUE inclua políticas de saúde descritas no organismo.

## <a name="get-deployed-service-package-health"></a>Obter saúde de pacote de serviço implantado
Devolve a saúde de uma entidade de pacote de serviços implantado. Entrada:

* [Obrigatório] O nome da aplicação (URI), o nome do nó (string) e o nome manifesto de serviço (cadeia) que identificam o pacote de serviço implantado.
* [Opcional] A política de saúde da aplicação usada para anular a política de manifesto de aplicação.
* [Opcional] Filtros para eventos que especifiquem quais as entradas que são de interesse e devem ser devolvidos no resultado (por exemplo, apenas erros, ou ambos avisos e erros). Todos os eventos são usados para avaliar a saúde agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a saúde de um pacote de serviço implantado através da API, crie um `FabricClient` e ligue para o método [GetDeployedServicePackageHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) no seu HealthManager. Para especificar parâmetros opcionais, utilize [o Serviço de Serviços DistribuídosHealthQueryDescription](/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a saúde do pacote de serviço implantado é [Get-ServiceFabricDeployedServicePackageHealth](/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Para ver onde uma aplicação é implementada, executar [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e olhar para as aplicações implementadas. Para ver quais os pacotes de serviços que estão numa aplicação, consulte o pacote de serviços implantado para crianças na produção [Get-ServiceFabricDeployedApplicationHealth.](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps)

O cmdlet seguinte obtém a saúde do pacote de serviço **WordCountServicePkg** do **tecido:/Aplicação WordCount** implantada no **_Node_2**. A entidade tem relatórios **system.hosting** para ativação bem sucedida de pacote de serviço e ponto de entrada, e registo de tipo de serviço bem sucedido.

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
Você pode obter saúde de pacote de serviço implantado com um [pedido GET](/rest/api/servicefabric/get-the-health-of-a-service-package) ou um [pedido DE](/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) QUE inclua políticas de saúde descritas no corpo.

## <a name="health-chunk-queries"></a>Consultas de pedaços de saúde
As consultas de pedaços de saúde podem devolver crianças de cluster de vários níveis (recursivamente), por filtros de entrada. Suporta filtros avançados que permitem muita flexibilidade na escolha das crianças para serem devolvidas. Os filtros podem especificar as crianças pelo identificador único ou por outros identificadores de grupo e/ou estados de saúde. Por defeito, nenhuma criança está incluída, ao contrário dos comandos de saúde que incluem sempre crianças de primeiro nível.

As [consultas de saúde](service-fabric-view-entities-aggregated-health.md#health-queries) devolvem apenas crianças de primeiro nível da entidade especificada por filtros necessários. Para obter os filhos das crianças, você deve chamar APIs de saúde adicional para cada entidade de interesse. Da mesma forma, para obter a saúde de entidades específicas, você deve chamar uma API de saúde para cada entidade desejada. A filtragem avançada da consulta de pedaços permite-lhe solicitar vários itens de interesse numa consulta, minimizando o tamanho da mensagem e o número de mensagens.

O valor da consulta do pedaço é que você pode obter estado de saúde para mais entidades de cluster (potencialmente todas as entidades de cluster começando com a raiz necessária) em uma chamada. Pode expressar consultas de saúde complexas tais como:

* Devolva apenas as aplicações por engano, e para essas aplicações incluem todos os serviços em aviso ou erro. Para serviços devolvidos, inclua todas as divisórias.
* Devolva apenas a saúde de quatro aplicações, especificadas pelos seus nomes.
* Devolva apenas a saúde das aplicações de um tipo de aplicação pretendido.
* Devolva todas as entidades implantadas num nó. Devolve todas as aplicações, todas as aplicações implantadas no nó especificado e todos os pacotes de serviço implantados nesse nó.
* Devolva todas as réplicas por engano. Devolve todas as aplicações, serviços, divisórias e apenas réplicas por engano.
* Devolva todas as aplicações. Para um serviço especificado, inclua todas as divisórias.

Atualmente, a consulta de pedaços de saúde é exposta apenas para a entidade cluster. Devolve um pedaço de saúde do cluster, que contém:

* O estado de saúde agregado do aglomerado.
* A lista de nós que respeitam os filtros de entrada do estado de saúde.
* A lista de aplicações que respeitam os filtros de entrada do estado de saúde. Cada pedaço do estado de saúde da aplicação contém uma lista de pedaços com todos os serviços que respeitam os filtros de entrada e uma lista de pedaços com todas as aplicações implementadas que respeitam os filtros. O mesmo para os filhos dos serviços e aplicações implementadas. Desta forma, todas as entidades do cluster podem ser potencialmente devolvidas se solicitadas, de forma hierárquica.

### <a name="cluster-health-chunk-query"></a>Consulta de pedaços de saúde do cluster
Devolve a saúde da entidade do cluster e contém os pedaços hierárquicos do estado de saúde das crianças necessitadas. Entrada:

* [Opcional] A política de saúde do cluster usada para avaliar os nós e os eventos de cluster.
* [Opcional] O mapa da política de saúde da aplicação, com as políticas de saúde usadas para anular as políticas de manifesto de aplicação.
* [Opcional] Filtros para nós e aplicações que especificam quais as entradas que são de interesse e devem ser devolvidos no resultado. Os filtros são específicos de uma entidade/grupo de entidades ou são aplicáveis a todas as entidades a esse nível. A lista de filtros pode conter um filtro geral e/ou filtros para identificadores específicos a entidades de grãos finos devolvidos pela consulta. Se vazias, as crianças não são devolvidas por defeito.
  Leia mais sobre os filtros no [NodeHealthStateFilter](/dotnet/api/system.fabric.health.nodehealthstatefilter) e [NodeHealthStateFilter](/dotnet/api/system.fabric.health.applicationhealthstatefilter). Os filtros de aplicação podem especificar novamente filtros avançados para crianças.

O resultado do pedaço inclui as crianças que respeitam os filtros.

Atualmente, a consulta de pedaços não devolve avaliações pouco saudáveis ou eventos de entidades. Essa informação extra pode ser obtida usando a consulta de saúde do cluster existente.

### <a name="api"></a>API
Para obter o pedaço de saúde do cluster, crie `FabricClient` um e chame o método [GetClusterHealthChunkAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) no seu **HealthManager**. Pode passar no [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) para descrever políticas de saúde e filtros avançados.

O seguinte código recebe o pedaço de saúde do cluster com filtros avançados.

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
O cmdlet para obter a saúde do cluster é [Get-ServiceFabricClusterChunkHealth](/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Em primeiro lugar, ligue-se ao cluster utilizando o cmdlet [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

O seguinte código só recebe nós se estiverem em Erro, exceto num nó específico, que deve ser sempre devolvido.

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

O cmdlet seguinte recebe o pedaço de cluster com filtros de aplicação.

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

O cmdlet seguinte devolve todas as entidades implantadas num nó.

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
Você pode obter pedaço de saúde do cluster com um [pedido GET](/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) ou um [pedido DE](/rest/api/servicefabric/health-of-cluster) QUE inclua políticas de saúde e filtros avançados descritos no corpo.

## <a name="general-queries"></a>Consultas gerais
Consultas gerais devolvem uma lista de entidades de Tecido de Serviço de um tipo especificado. São expostos através da API (através dos métodos **fabricClient.QueryManager),** Comandantes PowerShell e REST. Estas consultas agregam subqueries de vários componentes. Um deles é a loja de [saúde,](service-fabric-health-introduction.md#health-store)que povoa o estado de saúde agregado para cada resultado de consulta.  

> [!NOTE]
> As consultas gerais devolvem o estado de saúde agregado da entidade e não contêm dados de saúde ricos. Se uma entidade não for saudável, pode acompanhar as consultas de saúde para obter todas as suas informações de saúde, incluindo eventos, estados de saúde infantil e avaliações pouco saudáveis.
>
>

Se as consultas gerais devolverem um estado de saúde desconhecido para uma entidade, é possível que a loja de saúde não tenha dados completos sobre a entidade. Também é possível que um subquery para a loja de saúde não tenha sido bem sucedido (por exemplo, houve um erro de comunicação, ou a loja de saúde foi estrangulada). Acompanhe uma consulta de saúde para a entidade. Se a subquery tiver encontrado erros transitórios, tais como problemas de rede, esta consulta de seguimento pode ter sucesso. Também pode dar-lhe mais detalhes da loja de saúde sobre o porquê da entidade não estar exposta.

As consultas que contêm **O Estado de Saúde** para entidades são:

* Lista de nó: Devolve os nós da lista no cluster (paged).
  * API: [FabricClient.QueryClient.GetNodeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista de candidaturas: Devolve a lista de candidaturas no cluster (paged).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista de serviços: Devolve a lista de serviços numa aplicação (paged).
  * API: [FabricClient.QueryClient.GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Lista de partições: Devolve a lista de divisórias num serviço (paged).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Lista de réplicas: Devolve a lista de réplicas numa partição (paged).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Lista de candidaturas implantadas: Devolve a lista de aplicações implantadas num nó.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Lista de pacotes de serviço implantado: Devolve a lista de pacotes de serviço numa aplicação implantada.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Algumas das consultas devolvem os resultados da página. O retorno destas consultas é uma lista derivada do [PagedList \<T> ](/dotnet/api/system.fabric.query.pagedlist-1). Se os resultados não encaixarem numa mensagem, apenas uma página é devolvida e uma ContinuationToken que rastreia onde a enumeração parou. Continue a chamar a mesma consulta e passe na ficha de continuação da consulta anterior para obter os próximos resultados.

### <a name="examples"></a>Exemplos
O seguinte código obtém as aplicações pouco saudáveis no cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet seguinte obtém os detalhes da aplicação para o tecido:/WordCount. Reparem que o estado de saúde está a ser avisado.

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

O seguinte cmdlet recebe os serviços com um estado de erro sanitário:

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

## <a name="cluster-and-application-upgrades"></a>Atualizações de cluster e aplicações
Durante uma atualização monitorizada do cluster e aplicação, o Service Fabric verifica a saúde para garantir que tudo permanece saudável. Se uma entidade não for saudável como avaliada através da utilização de políticas de saúde configuradas, a atualização aplica políticas específicas de upgrade para determinar a próxima ação. A atualização pode ser interrompida para permitir a interação do utilizador (como corrigir condições de erro ou alterar políticas), ou pode voltar automaticamente para a versão boa anterior.

Durante uma atualização *de cluster,* você pode obter o estado de upgrade do cluster. O estado de upgrade inclui avaliações pouco saudáveis, que apontam para o que não é saudável no cluster. Se a atualização for revoada devido a problemas de saúde, o estado de upgrade lembra-se das últimas razões pouco saudáveis. Esta informação pode ajudar os administradores a investigar o que correu mal depois da atualização ter recuado ou parado.

Da mesma forma, durante uma atualização *de aplicações,* quaisquer avaliações não saudáveis estão contidas no estado de atualização da aplicação.

O seguinte mostra o estado de atualização da aplicação para uma aplicação de tecido modificado:/WordCount. Um cão de guarda relatou um erro numa das suas réplicas. A atualização está a reverter porque os controlos de saúde não são respeitados.

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

Leia mais sobre a atualização da [aplicação Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Use avaliações de saúde para resolver problemas
Sempre que houver um problema com o cluster ou uma aplicação, olhe para o cluster ou saúde da aplicação para identificar o que está errado. As avaliações pouco saudáveis fornecem detalhes sobre o que desencadeou o estado atual pouco saudável. Se precisar, pode perfurar entidades infantis pouco saudáveis para identificar a causa principal.

Por exemplo, considere uma aplicação pouco saudável porque há um relatório de erro numa das suas réplicas. O seguinte cmdlet Powershell mostra as avaliações pouco saudáveis:

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

Pode olhar para a réplica para obter mais informações:

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
> As avaliações pouco saudáveis mostram a primeira razão pela qual a entidade é avaliada para o estado de saúde atual. Pode haver vários outros eventos que desencadeiam este estado, mas não se refletem nas avaliações. Para obter mais informações, aprofundar as entidades de saúde para descobrir todos os relatórios insalubres no cluster.
>
>

## <a name="next-steps"></a>Passos seguintes
[Use system health reports to troubleshoot (Utilizar relatórios de estado de funcionamento do sistema para resolver problemas)](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de saúde personalizados do Service Fabric](service-fabric-report-health.md)

[Como reportar e verificar a saúde do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação do Tecido de Serviço](service-fabric-application-upgrade.md)
