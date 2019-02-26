---
title: Consulta para eventos de cluster com as APIs de EventStore em clusters do Azure Service Fabric | Documentos da Microsoft
description: Saiba como utilizar as APIs de EventStore do recursos de infraestrutura do serviço do Azure para a consulta para eventos de plataforma
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 491f113a3f08293b5f978242d27714b576d85d6f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818804"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Consultar APIs de EventStore para eventos de cluster

Este artigo descreve como consultar as APIs de EventStore que estão disponíveis no Service Fabric versão 6.2 e superior - se de que gostaria de saber mais sobre o serviço de EventStore, veja a [descrição geral do serviço de EventStore](service-fabric-diagnostics-eventstore.md). Atualmente, o serviço de EventStore só pode acessar dados nos últimos 7 dias (isso se baseia na política de retenção de dados de diagnóstico do seu cluster).

>[!NOTE]
>As APIs de EventStore são GA a partir do Service Fabric versão 6.4 para apenas os clusters de Windows em execução no Azure.

As APIs de EventStore pode ser acessada diretamente por meio de um ponto final REST ou através de programação. Dependendo da consulta, existem vários parâmetros que são necessárias para recolher os dados adequados. Esses parâmetros incluem normalmente:
* `api-version`: a versão do que está a utilizar as APIs de EventStore
* `StartTimeUtc`: define o início do período de que se estiver interessado em ver
* `EndTimeUtc`: fim do período de tempo

Além desses parâmetros, existem parâmetros opcionais disponíveis, como:
* `timeout`: substituir a 60 segundo tempo limite predefinido para executar a operação de pedido
* `eventstypesfilter`: isso lhe dá a opção para filtrar para tipos de evento específico
* `ExcludeAnalysisEvents`: não retornam eventos de "Análise de". Por predefinição, as consultas de EventStore retornará com eventos de "análise de" sempre que possível. Os eventos de análise são eventos mais avançados do canal operacional que contêm o contexto adicional ou informações além de um evento do Service Fabric regular e fornecem mais detalhes.
* `SkipCorrelationLookup`: não procure eventos correlacionados potenciais no cluster. Por predefinição, o EventStore tentará correlacionar eventos num cluster e vincular os eventos sempre que possível. 

Cada entidade num cluster pode ser consultada para eventos. Também pode consultar todas as entidades do tipo de eventos. Por exemplo, pode consultar para eventos para um nó específico ou para todos os nós do cluster. É o conjunto atual de entidades para o qual pode consultar para eventos (com como a consulta deve ser estruturada):
* Cluster: `/EventsStore/Cluster/Events`
* Nós: `/EventsStore/Nodes/Events`
* Nó: `/EventsStore/Nodes/<NodeName>/$/Events`
* Aplicações: `/EventsStore/Applications/Events`
* Aplicação: `/EventsStore/Applications/<AppName>/$/Events`
* Serviços: `/EventsStore/Services/Events`
* Serviço: `/EventsStore/Services/<ServiceName>/$/Events`
* Partições: `/EventsStore/Partitions/Events`
* Partição: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Réplicas: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replica: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Ao fazer referência uma aplicação ou o nome do serviço, a consulta não precisa de incluir o "fabric: /" prefixo. Além disso, se a sua aplicação ou os nomes de serviço tem um "/" nos mesmos, mude para um "~" para manter o trabalho de consulta. Por exemplo, se seu aplicativo exibido como "fabric: / App1/FrontendApp", suas consultas específicas da aplicação devem ser estruturadas como `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Além disso, os relatórios de estado de funcionamento para os serviços atualmente apresentados no aplicativo correspondente, para que poderia consultar para `DeployedServiceHealthReportCreated` eventos para a entidade de aplicativo adequado. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Consulta EventStore através de pontos finais da REST API

Pode consultar o EventStore diretamente através do ponto final REST, fazendo `GET` pedidos para: `<your cluster address>/EventsStore/<entity>/Events/`.

Por exemplo, para consultar todos os eventos de Cluster entre `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, o seu pedido teria o seguinte aspeto:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Isso poderia optar por retornar nenhum evento ou a lista de eventos devolvidos em json:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Aqui podemos ver que entre `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, este cluster concluída com êxito a primeira atualização quando ele foi primeiro preso, do `"CurrentClusterVersion": "0.0.0.0:"` para `"TargetClusterVersion": "6.2:1.0"`, na `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Consultar o EventStore por meio de programação

Também pode consultar o EventStore através de programação, com o [biblioteca de cliente do Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Assim que tiver o cliente de recursos de infraestrutura de serviço configurado, pode consultar para eventos acessando EventStore como este: ` sfhttpClient.EventStore.<request>`

Eis um exemplo de solicitação para todos os eventos entre do cluster `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, via o `GetClusterEventListAsync` função.

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

## <a name="sample-scenarios-and-queries"></a>Cenários de exemplo e consultas

Aqui estão alguns exemplos sobre como pode chamar as APIs de REST de Store de eventos para compreender o status do seu cluster.

*Atualizações de cluster:*

Para ver a última vez que o cluster foi com êxito ou tentada a ser atualizado na semana passada, pode consultar as APIs para atualizações concluídas recentemente ao seu cluster, através da consulta de eventos "ClusterUpgradeCompleted" o EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemas de atualização de cluster:*

Da mesma forma, se ocorreram problemas com uma recente atualização do cluster, poderia consultar todos os eventos para a entidade de cluster. Verá vários eventos, incluindo à iniciação de atualizações e cada UD para que a atualização implementado por meio de com êxito. Também poderá ver eventos para o ponto em que a reversão iniciado e correspondente a eventos de estado de funcionamento. Esta é a consulta que utilizaria para isso: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alterações de estado do nó:*

Para ver que o estado do nó é alterada ao longo dos últimos alguns dias - quando nós correu ou reduzir verticalmente, ou foram ativados ou desativados (pela plataforma, o serviço de caos, ou de entrada do usuário) - utilizam a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Eventos da aplicação:*

Também pode controlar sua recente implementações de aplicações e atualizações. Utilize a seguinte consulta para ver todos os eventos de aplicação no seu cluster: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Estado de funcionamento histórico para uma aplicação:*

Para além de simplesmente ver eventos de ciclo de vida de aplicativos, também poderá ver dados históricos sobre o estado de funcionamento de uma aplicação específica. Pode fazê-lo ao especificar o nome da aplicação para o qual deseja coletar os dados. Utilize esta consulta para obter todos os eventos de estado de funcionamento de aplicação: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Se pretender incluir eventos de estado de funcionamento que podem ter expirado (passar passado seu tempo para live (TTL)), adicionar `,ApplicationHealthReportExpired` ao final da consulta, para filtrar os dois tipos de eventos.

*Estado de funcionamento histórico para todos os serviços em "myApp":*

Atualmente, eventos de relatório de estado de funcionamento para os serviços são exibidos como `DeployedServicePackageNewHealthReport` eventos sob a entidade correspondente do aplicativo. Para ver como seus serviços já fazemos por "App1", utilize a seguinte consulta: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Reconfiguração da partição:*

Para ver todos os movimentos de partição que aconteceram no seu cluster, consultar o `PartitionReconfigured` eventos. Isto pode ajudar a descobrir o que as cargas de trabalho foi executado no nó que em alturas específicas, quando diagnosticando problemas no seu cluster. Eis uma consulta de exemplo que faz isso: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Serviço de Chaos:*

Existe um evento para quando o Chaos serviço é iniciado ou parado ou seja exposta no nível do cluster. Para ver a sua utilização recente do serviço caos, utilize a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

