---
title: Loja de eventos de tecido de serviço Azure
description: Conheça a Azure Service Fabric's EventStore, uma forma de entender e monitorizar o estado de um cluster ou cargas de trabalho a qualquer momento.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: 0b6258e160794eaf7d0c05775f8fd3b796e3ba47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91354824"
---
# <a name="eventstore-overview"></a>Visão geral da loja de eventos

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. as APIs da EventStore só estão disponíveis para clusters Windows em execução apenas no Azure. Estamos a trabalhar na deposição desta funcionalidade ao Linux, bem como aos nossos clusters autónomos.

## <a name="overview"></a>Descrição geral

Introduzido na versão 6.2, o serviço EventStore é uma opção de monitorização em Tecido de Serviço. A EventStore fornece uma forma de entender o estado do seu cluster ou cargas de trabalho num dado momento.
O EventStore é um serviço stateful Service Fabric que mantém eventos do cluster. O evento é exposto através do Service Fabric Explorer, REST e APIs. O EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade do seu cluster e deve ser usado para ajudar:

* Diagnosticar problemas no desenvolvimento ou teste, ou onde possa estar a usar um gasoduto de monitorização
* Confirme que as ações de gestão que está a tomar no seu cluster estão a ser processadas corretamente
* Obtenha uma "foto" de como o Service Fabric está interagindo com uma determinada entidade

![O screenshot mostra o separador EVENTOS do Painel De Nodes de vários eventos, incluindo um evento NodeDown.](media/service-fabric-diagnostics-eventstore/eventstore.png)

Para ver uma lista completa de eventos disponíveis na Loja de Eventos, consulte os [eventos de Service Fabric.](service-fabric-diagnostics-event-generation-operational.md)

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. os APIs e UX da EventStore estão geralmente disponíveis para clusters Azure Windows. Estamos a trabalhar na deposição desta funcionalidade ao Linux, bem como aos nossos clusters autónomos.

O serviço EventStore pode ser consultado para eventos que estejam disponíveis para cada entidade e tipo de entidade no seu cluster. Isto significa que pode consultar eventos nos seguintes níveis:
* Cluster: eventos específicos do próprio cluster (por exemplo, atualização do cluster)
* Nós: todos os eventos de nível de nó
* Nó: eventos específicos de um nó, identificado por `nodeName`
* Aplicações: todos os eventos de nível de aplicação
* Aplicação: eventos específicos de uma aplicação identificada por `applicationId`
* Serviços: eventos de todos os serviços nos seus clusters
* Serviço: eventos de um serviço específico identificado por `serviceId`
* Partições: eventos de todas as divisórias
* Partição: eventos de uma partição específica identificada por `partitionId`
* Réplicas de partição: eventos de todas as réplicas/instâncias dentro de uma partição específica identificada por `partitionId`
* Réplica de partição: eventos a partir de uma réplica/instância específica identificada por `replicaId` e `partitionId`

Para saber mais sobre a API consulte a referência API da [EventStore](/rest/api/servicefabric/sfclient-index-eventsstore).

O serviço EventStore também tem a capacidade de correlacionar eventos no seu cluster. Olhando para eventos que foram escritos ao mesmo tempo de diferentes entidades que podem ter impactado entre si, o serviço EventStore é capaz de associar estes eventos para ajudar a identificar causas para atividades no seu cluster. Por exemplo, se uma das suas aplicações se tornar insalubre sem alterações induzidas, o EventStore também analisará outros eventos expostos pela plataforma e poderá correlacionar isso com um `Error` ou `Warning` evento. Isto ajuda na deteção rápida de falhas e na análise de causas de raiz.

## <a name="enable-eventstore-on-your-cluster"></a>Ativar o EventStore no seu cluster

### <a name="local-cluster"></a>Aglomerado Local

Em [fabricSettings.jsno seu cluster,](service-fabric-cluster-fabric-settings.md)adicione o EventStoreService como uma funcionalidade addOn e execute uma atualização de cluster.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Versão do cluster Azure 6.5+
Se o seu cluster Azure for atualizado para a versão 6.5 ou superior, o EventStore será automaticamente ativado no seu cluster. Para excluir, tem de atualizar o seu modelo de cluster com o seguinte:

* Use uma versão API de `2019-03-01` ou mais recente 
* Adicione o seguinte código à secção de propriedades no seu cluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Versão do cluster Azure 6.4

Se estiver a utilizar a versão 6.4, pode editar o seu modelo de Gestor de Recursos Azure para ligar o serviço EventStore. Isto é feito através da realização de uma [atualização de cluster config](service-fabric-cluster-config-upgrade-azure.md) e adicionando o seguinte código, pode utilizar PlacementConstraints para colocar as réplicas do serviço EventStore num NodeType específico, por exemplo, um NodeType dedicado aos serviços do sistema. A `upgradeDescription` secção configura a atualização de configuração para ativar um reinício dos nós. Pode remover a secção em outra atualização.

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
* Começar com a API da Loja de Eventos - [Utilização das APIs da EventStore em clusters de tecido de serviço Azure](service-fabric-diagnostics-eventstore-query.md)
* Saiba mais sobre a lista de eventos oferecidos pela EventStore - [Eventos de Tecido de Serviço](service-fabric-diagnostics-event-generation-operational.md)
* Visão geral da monitorização e diagnósticos em Tecido de Serviço - [Monitorização e Diagnóstico de Tecido de Serviço](service-fabric-diagnostics-overview.md)
* Ver a lista completa de chamadas API - [Referência API da Loja de Eventos](/rest/api/servicefabric/sfclient-index-eventsstore)
* Saiba mais sobre a monitorização do seu cluster - [Monitorização do cluster e da plataforma.](service-fabric-diagnostics-event-generation-infra.md)
