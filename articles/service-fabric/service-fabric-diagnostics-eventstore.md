---
title: Loja de Eventos de Tecido de Serviço Azure
description: Conheça a Loja de Eventos da Azure Service Fabric, uma forma de compreender e monitorizar o estado de um cluster ou cargas de trabalho a qualquer momento.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: d23c8114bf10ef3225775accef6910c0ba539e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645740"
---
# <a name="eventstore-overview"></a>Visão geral da EventStore

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. as APIs da EventStore só estão disponíveis para clusters Windows que executam apenas em Azure. Estamos a trabalhar na colocação desta funcionalidade para o Linux, bem como para os nossos clusters autónomos.

## <a name="overview"></a>Descrição geral

Introduzido na versão 6.2, o serviço EventStore é uma opção de monitorização no Service Fabric. A EventStore fornece uma forma de compreender o estado do seu cluster ou cargas de trabalho num dado momento.
A EventStore é um serviço de tecido de serviço imponente que mantém eventos do cluster. O evento está exposto através do Service Fabric Explorer, REST e APIs. EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade do seu cluster e deve ser usado para ajudar:

* Diagnosticar problemas no desenvolvimento ou nos testes, ou onde pode estar a utilizar um pipeline de monitorização
* Confirme que as ações de gestão que está a tomar sobre o seu cluster estão a ser processadas corretamente
* Obtenha um "instantâneo" de como o Service Fabric está interagindo com uma determinada entidade

![Loja de Eventos](media/service-fabric-diagnostics-eventstore/eventstore.png)

Para ver uma lista completa de eventos disponíveis na EventStore, consulte [eventos de Tecido de Serviço](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. as APIs e UX da EventStore estão geralmente disponíveis para clusters Do Windows Azure. Estamos a trabalhar na colocação desta funcionalidade para o Linux, bem como para os nossos clusters autónomos.

O serviço EventStore pode ser consultado para eventos disponíveis para cada entidade e tipo de entidade no seu cluster. Isto significa que pode consultar eventos nos seguintes níveis:
* Cluster: eventos específicos do próprio cluster (por exemplo, upgrade de cluster)
* Nós: todos os eventos de nível de nó
* Nó: eventos específicos de um nó, identificados por`nodeName`
* Aplicações: todos os eventos de nível de aplicação
* Aplicação: eventos específicos de uma aplicação identificada por`applicationId`
* Serviços: eventos de todos os serviços dos seus clusters
* Serviço: eventos de um serviço específico identificado por`serviceId`
* Divisórias: eventos de todas as divisórias
* Partição: eventos de uma partição específica identificados por`partitionId`
* Réplicas de partição: eventos de todas as réplicas/instâncias dentro de uma partição específica identificada por`partitionId`
* Réplica da partilha: eventos de uma `replicaId` réplica/instância específica identificado s e`partitionId`

Para saber mais sobre a API consulte a [referência da API eventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

O serviço EventStore também tem a capacidade de correlacionar eventos no seu cluster. Ao analisar eventos que foram escritos ao mesmo tempo de diferentes entidades que possam ter impactado mutuamente, o serviço EventStore é capaz de ligar estes eventos para ajudar na identificação de causas para atividades no seu cluster. Por exemplo, se uma das suas aplicações ficar insalubre sem alterações induzidas, a EventStore também `Error` `Warning` analisará outros eventos expostos pela plataforma e poderá correlacionar isso com um ou evento. Isto ajuda na deteção mais rápida de falhas e na análise de causas de raiz.

## <a name="enable-eventstore-on-your-cluster"></a>Ativar a EventStore no seu cluster

### <a name="local-cluster"></a>Aglomerado Local

No [tecidoSettings.json no seu cluster,](service-fabric-cluster-fabric-settings.md)adicione eventStoreService como uma funcionalidade addOn e execute uma atualização de cluster.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Versão do cluster Azure 6.5+
Se o seu cluster Azure for atualizado para a versão 6.5 ou superior, o EventStore será automaticamente ativado no seu cluster. Para não optar, precisa de atualizar o seu modelo de cluster com o seguinte:

* Use uma versão `2019-03-01` API de ou mais recente 
* Adicione o seguinte código à secção de propriedades no seu cluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Versão do cluster Azure 6.4

Se estiver a utilizar a versão 6.4, pode editar o seu modelo de Gestor de Recursos Azure para ativar o serviço EventStore. Isto é feito através da realização de uma atualização de [config](service-fabric-cluster-config-upgrade-azure.md) cluster e adicionando o seguinte código, pode utilizar As Restrições de Colocação para colocar as réplicas do serviço EventStore num NodeType específico, por exemplo, um NodeType dedicado aos serviços do sistema. A `upgradeDescription` secção configura a atualização do config para desencadear um reinício dos nódosos. Pode remover a secção noutra atualização.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Passos seguintes
* Inicie-se com a API EventStore - [Utilizando as APIs da EventStore em clusters de tecido de serviço Azure](service-fabric-diagnostics-eventstore-query.md)
* Saiba mais sobre a lista de eventos oferecidos pela EventStore - [Eventos de Tecido de Serviço](service-fabric-diagnostics-event-generation-operational.md)
* Visão geral da monitorização e diagnóstico sintetizado em Tecido de Serviço - [Monitorização e Diagnóstico para Tecido de Serviço](service-fabric-diagnostics-overview.md)
* Ver a lista completa de chamadas DaPI - [EventStore REST API Reference](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Saiba mais sobre monitorização do seu cluster - [Monitorização do cluster e plataforma](service-fabric-diagnostics-event-generation-infra.md).
