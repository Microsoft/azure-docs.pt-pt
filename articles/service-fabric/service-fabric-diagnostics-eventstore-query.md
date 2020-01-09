---
title: Consultar eventos de cluster usando as APIs EventStore
description: Saiba como usar as APIs do Azure Service Fabric EventStore para consultar eventos de plataforma
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614405"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Consultar APIs do EventStore para eventos de cluster

Este artigo aborda como consultar as APIs do EventStore que estão disponíveis no Service Fabric versão 6,2 e posterior-se você quiser saber mais sobre o serviço EventStore, consulte a [visão geral do serviço EventStore](service-fabric-diagnostics-eventstore.md). Atualmente, o serviço EventStore só pode acessar dados nos últimos 7 dias (isso se baseia na política de retenção de dados de diagnóstico do cluster).

>[!NOTE]
>As APIs EventStore são GA a partir da versão Service Fabric 6,4 somente para clusters do Windows em execução no Azure.

As APIs EventStore podem ser acessadas diretamente por meio de um ponto de extremidade REST ou programaticamente. Dependendo da consulta, há vários parâmetros que são necessários para coletar os dados corretos. Esses parâmetros normalmente incluem:
* `api-version`: a versão das APIs do EventStore que você está usando
* `StartTimeUtc`: define o início do período que você está interessado em examinar
* `EndTimeUtc`: fim do período de tempo

Além desses parâmetros, também há parâmetros opcionais disponíveis, como:
* `timeout`: substituir o segundo tempo limite padrão de 60 para executar a operação de solicitação
* `eventstypesfilter`: isso lhe dá a opção de filtrar tipos de eventos específicos
* `ExcludeAnalysisEvents`: não retornar eventos de ' análise '. Por padrão, as consultas EventStore retornarão com eventos de "análise" sempre que possível. Os eventos de análise são eventos de canal operacional mais ricos que contêm contexto adicional ou informações além de um evento de Service Fabric regular e fornecem mais profundidade.
* `SkipCorrelationLookup`: não procure eventos correlacionados potenciais no cluster. Por padrão, o EventStore tentará correlacionar eventos em um cluster e vincular seus eventos quando possível. 

Cada entidade em um cluster pode ser consultada em busca de eventos. Você também pode consultar eventos para todas as entidades do tipo. Por exemplo, você pode consultar eventos para um nó específico ou para todos os nós no cluster. O conjunto atual de entidades para as quais você pode consultar eventos é (com o modo como a consulta seria estruturada):
* Cluster: `/EventsStore/Cluster/Events`
* Nós: `/EventsStore/Nodes/Events`
* Nó: `/EventsStore/Nodes/<NodeName>/$/Events`
* Aplicativos: `/EventsStore/Applications/Events`
* Aplicativo: `/EventsStore/Applications/<AppName>/$/Events`
* Serviços: `/EventsStore/Services/Events`
* Serviço: `/EventsStore/Services/<ServiceName>/$/Events`
* Partições: `/EventsStore/Partitions/Events`
* Partição: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Réplicas: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Réplica: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Ao referenciar um nome de aplicativo ou de serviço, a consulta não precisa incluir a "Fabric:/" prefixo. Além disso, se seus nomes de aplicativo ou de serviço tiverem um "/" neles, alterne para um "~" para manter a consulta funcionando. Por exemplo, se seu aplicativo aparecer como "Fabric:/App1/FrontendApp", suas consultas específicas do aplicativo serão estruturadas como `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Além disso, os relatórios de integridade dos serviços são mostrados no aplicativo correspondente, portanto, você consultaria `DeployedServiceHealthReportCreated` eventos para a entidade de aplicativo correta. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Consultar o EventStore por meio de pontos de extremidade da API REST

Você pode consultar o EventStore diretamente por meio de um ponto de extremidade REST, fazendo `GET` solicitações para: `<your cluster address>/EventsStore/<entity>/Events/`.

Por exemplo, para consultar todos os eventos de cluster entre `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, sua solicitação teria a seguinte aparência:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Isso poderia não retornar eventos ou a lista de eventos retornados em JSON:

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

Aqui, podemos ver que entre `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, esse cluster concluiu com êxito sua primeira atualização quando ela foi inicialmente acima, de `"CurrentClusterVersion": "0.0.0.0:"` para `"TargetClusterVersion": "6.2:1.0"`, em `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Consultar o EventStore de forma programática

Você também pode consultar o EventStore de forma programática, por meio da [biblioteca de cliente Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Depois de configurar o cliente Service Fabric, você pode consultar eventos acessando o EventStore da seguinte maneira: `sfhttpClient.EventStore.<request>`

Aqui está um exemplo de solicitação para todos os eventos de cluster entre `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, por meio da função `GetClusterEventListAsync`.

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

## <a name="sample-scenarios-and-queries"></a>Cenários e consultas de exemplo

Aqui estão alguns exemplos de como você pode chamar as APIs REST do repositório de eventos para entender o status do cluster.

*Atualizações de cluster:*

Para ver a última vez que o cluster foi bem-sucedido ou tentou ser atualizado na última semana, você pode consultar as APIs para atualizações concluídas recentemente para o cluster, consultando os eventos "ClusterUpgradeCompleted" no EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemas de atualização do cluster:*

Da mesma forma, se houver problemas com uma atualização de cluster recente, você poderá consultar todos os eventos para a entidade de cluster. Você verá vários eventos, incluindo a inicialização de atualizações e cada UD para a qual a atualização foi distribuída com êxito. Você também verá eventos para o ponto em que a reversão foi iniciada e os eventos de integridade correspondentes. Esta é a consulta que você usaria para isso: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alterações de status do nó:*

Para ver as alterações de status do nó nos últimos dias – quando os nós foram ativados ou desativados, ou foram habilitados ou desativados (pela plataforma, pelo serviço de caos ou pela entrada do usuário)-Use a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Eventos do aplicativo:*

Você também pode controlar suas implantações e atualizações de aplicativos recentes. Use a consulta a seguir para ver todos os eventos de aplicativo em seu cluster: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Integridade histórica para um aplicativo:*

Além de ver apenas os eventos do ciclo de vida do aplicativo, talvez você também queira ver dados históricos sobre a integridade de um aplicativo específico. Você pode fazer isso especificando o nome do aplicativo para o qual deseja coletar os dados. Use esta consulta para obter todos os eventos de integridade do aplicativo: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Se você quiser incluir eventos de integridade que podem ter expirado (tempo limite de vida útil), adicione `,ApplicationHealthReportExpired` ao final da consulta para filtrar em dois tipos de eventos.

*Integridade histórica para todos os serviços em "myApp":*

Atualmente, os eventos do relatório de integridade para os serviços aparecem como `DeployedServicePackageNewHealthReport` eventos sob a entidade de aplicativo correspondente. Para ver como seus serviços estão fazendo para o "app1", use a seguinte consulta: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Reconfiguração de partição:*

Para ver todos os movimentos de partição que ocorreram em seu cluster, consulte o evento `PartitionReconfigured`. Isso pode ajudá-lo a descobrir quais cargas de trabalho foram executadas em qual nó em momentos específicos, ao diagnosticar problemas no cluster. Aqui está um exemplo de consulta que faz isso: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Serviço de caos:*

Há um evento para quando o serviço de caos é iniciado ou interrompido, que é exposto no nível do cluster. Para ver seu uso recente do serviço de caos, use a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

