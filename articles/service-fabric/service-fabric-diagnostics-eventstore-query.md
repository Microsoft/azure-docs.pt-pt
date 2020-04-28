---
title: Consulta para eventos de cluster usando as APIs eventstore
description: Saiba como usar as APIs da Loja de Eventos de Tecido de Tecido de Serviço Azure para consultar eventos de plataforma
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614405"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Perguntas eventstore APIs para eventos de cluster

Este artigo abrange como consultar as APIs da EventStore que estão disponíveis na versão 6.2 do Service Fabric e posteriormente - se quiser saber mais sobre o serviço EventStore, consulte a visão geral do [serviço EventStore](service-fabric-diagnostics-eventstore.md). Atualmente, o serviço EventStore só pode aceder a dados nos últimos 7 dias (isto baseia-se na política de conservação de dados de diagnóstico do seu cluster).

>[!NOTE]
>As APIs da EventStore são GA a partir da versão 6.4 do Service Fabric para apenas clusters Windows em funcionamento no Azure.

As APIs da EventStore podem ser acedidas diretamente através de um ponto final REST, ou programáticamente. Dependendo da consulta, existem vários parâmetros que são necessários para recolher os dados certos. Estes parâmetros normalmente incluem:
* `api-version`: a versão das APIs da EventStore que está a utilizar
* `StartTimeUtc`: define o início do período que lhe interessa molhar
* `EndTimeUtc`: fim do período de tempo

Além destes parâmetros, existem também parâmetros opcionais disponíveis, tais como:
* `timeout`: anular o prazo de 60 segundos por defeito para a execução da operação de pedido
* `eventstypesfilter`: isto dá-lhe a opção de filtrar para tipos específicos de eventos
* `ExcludeAnalysisEvents`: não devolver os eventos de "Análise". Por padrão, as consultas da EventStore voltarão com eventos de "análise" sempre que possível. Os eventos de análise são eventos de canais operacionais mais ricos que contêm contexto ou informação adicional para além de um evento regular de Tecido de Serviço e fornecem mais profundidade.
* `SkipCorrelationLookup`: não procure potenciais eventos correlacionados no cluster. Por padrão, a EventStore tentará correlacionar eventos através de um cluster e ligar os seus eventos quando possível. 

Cada entidade num cluster pode ser consultas para eventos. Também pode consultar eventos para todas as entidades do tipo. Por exemplo, você pode consultar eventos para um nó específico, ou para todos os nós no seu cluster. O conjunto atual de entidades para as quais pode consultar eventos é (com a forma como a consulta seria estruturada):
* Aglomerado:`/EventsStore/Cluster/Events`
* Nósos:`/EventsStore/Nodes/Events`
* Nó:`/EventsStore/Nodes/<NodeName>/$/Events`
* Candidaturas:`/EventsStore/Applications/Events`
* Aplicação:`/EventsStore/Applications/<AppName>/$/Events`
* Serviços:`/EventsStore/Services/Events`
* Serviço:`/EventsStore/Services/<ServiceName>/$/Events`
* Partições:`/EventsStore/Partitions/Events`
* Partição:`/EventsStore/Partitions/<PartitionID>/$/Events`
* Réplicas:`/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Réplica:`/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Ao fazer referência a uma aplicação ou nome de serviço, a consulta não precisa de incluir o "tecido:/" prefixo. Além disso, se os seus nomes de aplicação ou serviço tiverem um "/" neles, troque-o para um "~" para manter a consulta a funcionar. Por exemplo, se a sua aplicação aparecer como "tecido:/App1/FrontendApp", `/EventsStore/Applications/App1~FrontendApp/$/Events`as consultas específicas da sua aplicação seriam estruturadas como .
>Além disso, os relatórios de saúde dos serviços de `DeployedServiceHealthReportCreated` hoje aparecem no âmbito da aplicação correspondente, pelo que consultaria eventos para a entidade de aplicação certa. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Consulta da EventStore através de pontos finais rest API

Pode consultar a EventStore diretamente através de um `GET` ponto final `<your cluster address>/EventsStore/<entity>/Events/`REST, fazendo pedidos para: .

Por exemplo, para consultar todos os `2018-04-03T18:00:00Z` eventos do Cluster entre e, `2018-04-04T18:00:00Z`o seu pedido seria como:

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

Aqui podemos ver `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`que entre e , este cluster concluiu com sucesso `"CurrentClusterVersion": "0.0.0.0:"` a `"TargetClusterVersion": "6.2:1.0"`sua `"OverallUpgradeElapsedTimeInMs": "120196.5212"`primeira atualização quando foi primeiramente levantou-se, de , em .

## <a name="query-the-eventstore-programmatically"></a>Consulta da EventStore programática

Também pode consultar a EventStore programáticamente, através da [biblioteca de clientes Service Fabric.](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)

Assim que tiver o seu Cliente de Tecido de Serviço configurado, pode consultar eventos acedendo assim à Loja de Eventos:`sfhttpClient.EventStore.<request>`

Aqui está um pedido de `2018-04-03T18:00:00Z` exemplo `2018-04-04T18:00:00Z`para `GetClusterEventListAsync` todos os eventos de cluster entre e, através da função.

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

## <a name="sample-scenarios-and-queries"></a>Cenários e consultas de amostra

Aqui estão poucos exemplos de como pode ligar para as APIs REST DA Loja de Eventos para entender o estado do seu cluster.

*Upgrades de cluster:*

Para ver a última vez que o seu cluster foi atualizado com sucesso ou tentou ser atualizado na semana passada, pode consultar as APIs para atualizações recentemente concluídas para o seu cluster, consultando os eventos "ClusterUpgradeCompleted" na EventStore:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemas de upgrade de cluster:*

Da mesma forma, se houvesse problemas com uma recente atualização do cluster, poderia consultar todos os eventos para a entidade cluster. Você verá vários eventos, incluindo o início de upgrades e cada UD para o qual a atualização passou com sucesso. Você também verá eventos para o ponto em que o relançamento começou e eventos de saúde correspondentes. Aqui está a pergunta que usaria para isto:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alterações no estado do nó:*

Para ver as alterações no estado do nó nos últimos dias - quando os nós subiram ou desceram, ou foram ativados ou desativados (quer pela plataforma, pelo serviço de caos, quer pela entrada do utilizador) - utilize a seguinte consulta:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Eventos de candidatura:*

Também pode acompanhar as suas recentes implementações e upgrades de aplicações. Utilize a seguinte consulta para ver todos os eventos de aplicação no seu cluster:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Saúde histórica para uma aplicação:*

Além de apenas ver eventos de ciclo de vida de aplicações, também pode querer ver dados históricos sobre a saúde de uma aplicação específica. Pode fazê-lo especificando o nome da aplicação para o qual pretende recolher os dados. Use esta consulta para obter todos `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`os eventos de saúde da aplicação: . Se quiser incluir eventos de saúde que possam ter expirado (passou `,ApplicationHealthReportExpired` o seu tempo para viver (TTL)), adicione ao final da consulta, para filtrar em dois tipos de eventos.

*Saúde histórica para todos os serviços no "myApp":*

Atualmente, os eventos de `DeployedServicePackageNewHealthReport` relatório de saúde para serviços aparecem como eventos sob a entidade de aplicação correspondente. Para ver como os seus serviços têm estado a fazer para "App1", utilize a seguinte consulta:`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Reconfiguração da divisória:*

Para ver todos os movimentos de partição que `PartitionReconfigured` aconteceram no seu cluster, consulta para o evento. Isto pode ajudá-lo a descobrir que cargas de trabalho correram em que nó em momentos específicos, ao diagnosticar problemas no seu cluster. Aqui está uma amostra que faz isso:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Serviço de caos:*

Há um evento para quando o serviço Caos é iniciado ou parado que é exposto ao nível do cluster. Para ver o seu uso recente do serviço Caos, use a seguinte consulta:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

