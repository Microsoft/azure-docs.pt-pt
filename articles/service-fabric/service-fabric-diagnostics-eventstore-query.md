---
title: Consulta para eventos de cluster usando as APIs eventStore
description: Saiba como utilizar o Azure Service Fabric EventStore APIs para consultar eventos da plataforma
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bc8909a6f25c3adcba60de639d822769baff703f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628813"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Consultas EventStore APIs para eventos de cluster

Este artigo abrange como consultar as APIs da EventStore que estão disponíveis na versão 6.2 do Service Fabric e posteriormente - se quiser saber mais sobre o serviço EventStore, consulte a visão geral do [serviço EventStore](service-fabric-diagnostics-eventstore.md). Atualmente, o serviço EventStore só pode aceder aos dados dos últimos 7 dias (isto baseia-se na política de retenção de dados de diagnóstico do seu cluster).

>[!NOTE]
>As APIs da EventStore são GA a partir da versão 6.4 do Service Fabric para apenas clusters Windows em execução no Azure.

As APIs da EventStore podem ser acedidas diretamente através de um ponto final REST, ou programáticamente. Dependendo da consulta, existem vários parâmetros que são necessários para recolher os dados certos. Estes parâmetros geralmente incluem:
* `api-version`: a versão das APIs da EventStore que está a utilizar
* `StartTimeUtc`: define o início do período em que está interessado em olhar
* `EndTimeUtc`: fim do período de tempo

Além destes parâmetros, existem também parâmetros opcionais disponíveis, tais como:
* `timeout`: anular o prazo de 60 segundos por defeito para a realização da operação de pedido
* `eventstypesfilter`: isto dá-lhe a opção de filtrar para tipos específicos de eventos
* `ExcludeAnalysisEvents`: não devolva os eventos de 'Análise'. Por padrão, as consultas da EventStore regressarão com eventos de "análise" sempre que possível. Os eventos de análise são eventos de canal operacional mais ricos que contêm contexto ou informação adicional para além de um evento regular de Tecido de Serviço e fornecem mais profundidade.
* `SkipCorrelationLookup`: não procure potenciais eventos correlacionados no cluster. Por padrão, a EventStore tentará correlacionar eventos através de um cluster, e ligar os seus eventos em conjunto, quando possível. 

Cada entidade num cluster pode ser consultas para eventos. Pode ainda consultar eventos para todas as entidades do tipo. Por exemplo, você pode consultar eventos para um nó específico, ou para todos os nós no seu cluster. O conjunto atual de entidades para as quais se pode consultar para eventos é (com a forma como a consulta seria estruturada):
* Cluster: `/EventsStore/Cluster/Events`
* Nó: `/EventsStore/Nodes/Events`
* Nó: `/EventsStore/Nodes/<NodeName>/$/Events`
* Aplicações: `/EventsStore/Applications/Events`
* Aplicação: `/EventsStore/Applications/<AppName>/$/Events`
* Serviços: `/EventsStore/Services/Events`
* Serviço: `/EventsStore/Services/<ServiceName>/$/Events`
* Divisórias: `/EventsStore/Partitions/Events`
* Partição: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Réplicas: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Réplica: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Ao fazer referência a uma aplicação ou nome de serviço, a consulta não precisa de incluir o "tecido:/" prefixo. Além disso, se os nomes da sua aplicação ou serviço tiverem um "/" neles, mude-o para um "~" para manter a consulta a funcionar. Por exemplo, se a sua aplicação aparecer como "fabric:/App1/FrontendApp", as consultas específicas da sua aplicação serão estruturadas como `/EventsStore/Applications/App1~FrontendApp/$/Events` .
>Além disso, os relatórios de saúde dos serviços de hoje aparecem sob a aplicação correspondente, para que você possa consultar `DeployedServiceHealthReportCreated` eventos para a entidade de aplicação certa. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Consulta da Loja de Eventos através de pontos finais rest API

Pode consultar a Loja do Evento diretamente através de um ponto final REST, fazendo `GET` pedidos para: `<your cluster address>/EventsStore/<entity>/Events/` .

Por exemplo, para consultar todos os eventos do Cluster entre `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z` e, o seu pedido seria como:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Isto pode não devolver nenhum evento ou a lista de eventos devolvidos em json:

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

Aqui podemos ver que entre `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z` e, este cluster completou com sucesso a sua primeira atualização quando foi levantado pela primeira vez, de `"CurrentClusterVersion": "0.0.0.0:"` , em `"TargetClusterVersion": "6.2:1.0"` `"OverallUpgradeElapsedTimeInMs": "120196.5212"` .

## <a name="query-the-eventstore-programmatically"></a>Consultar o programa EventStore

Também pode consultar o Programa EventStore, através da biblioteca de [clientes Service Fabric.](/dotnet/api/overview/azure/service-fabric#client-library)

Assim que tiver o seu Cliente de Tecido de Serviço configurado, pode consultar eventos acedendo ao EventStore desta forma: `sfhttpClient.EventStore.<request>`

Aqui está um pedido de exemplo para todos os eventos de cluster entre `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z` e, através da `GetClusterEventListAsync` função.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Aqui está outro exemplo que consulta a saúde do cluster e todos os eventos de nó em setembro de 2018 e os imprime.

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

## <a name="sample-scenarios-and-queries"></a>Cenários e consultas de amostra

Aqui estão alguns exemplos sobre como pode ligar para as APIs de Descanso da Loja de Eventos para entender o estado do seu cluster.

*Upgrades de cluster:*

Para ver a última vez que o seu cluster foi atualizado com sucesso ou tentou ser atualizado na semana passada, pode consultar as APIs para atualizações recentemente concluídas para o seu cluster, consultando os eventos "ClusterUpgradeCompleted" na Loja de Eventos: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemas de atualização do cluster:*

Da mesma forma, se houvesse problemas com uma recente atualização de cluster, poderia consultar todos os eventos para a entidade cluster. Você verá vários eventos, incluindo o início de upgrades e cada UD para o qual a atualização foi lançada com sucesso. Você também verá eventos para o ponto em que o revés começou e correspondentes eventos de saúde. Aqui está a consulta que usaria para isto: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alterações no estado do nó:*

Para ver as alterações no estado do nó nos últimos dias - quando os nós subiram ou desceram, ou foram ativados ou desativados (quer pela plataforma, pelo serviço de caos, quer pela entrada do utilizador) - utilize a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Eventos de aplicação:*

Também pode rastrear as suas recentes implementações e atualizações de aplicações. Utilize a seguinte consulta para ver todos os eventos de aplicação no seu cluster: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Saúde histórica para uma aplicação:*

Além de apenas ver eventos de ciclo de vida de aplicações, também pode querer ver dados históricos sobre a saúde de uma aplicação específica. Pode fazê-lo especificando o nome da aplicação para o qual pretende recolher os dados. Utilize esta consulta para obter todos os eventos de saúde da aplicação: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport` . Se quiser incluir eventos de saúde que possam ter expirado (passou o seu tempo para viver (TTL)), adicione `,ApplicationHealthReportExpired` ao final da consulta, para filtrar dois tipos de eventos.

*Saúde histórica para todos os serviços em "myApp":*

Atualmente, os eventos de relatório de saúde para serviços aparecem como `DeployedServicePackageNewHealthReport` eventos sob a entidade de aplicação correspondente. Para ver como os seus serviços têm estado a fazer para "App1", utilize a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Reconfiguração da partição:*

Para ver todos os movimentos de partição que aconteceram no seu agrupamento, consulta para o `PartitionReconfigured` evento. Isto pode ajudá-lo a descobrir que cargas de trabalho correram em que nó em momentos específicos, ao diagnosticar problemas no seu cluster. Aqui está uma consulta de amostra que faz isso: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Serviço caos:*

Há um evento para quando o serviço Caos é iniciado ou parado que é exposto ao nível do cluster. Para ver a sua utilização recente do serviço Caos, utilize a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`
