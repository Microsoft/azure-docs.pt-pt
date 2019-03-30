---
title: Store de eventos do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre o Azure Service Fabric EventStore
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: b8e1958947ced5ea2d0bd8b34667210bf935072d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662912"
---
# <a name="eventstore-service-overview"></a>Descrição geral do serviço de EventStore

>[!NOTE]
>A partir da versão 6.4 do Service Fabric. as APIs de EventStore só estão disponíveis para os clusters do Windows em execução no Azure apenas. Estamos a trabalhar no portar essa funcionalidade para Linux, bem como nossos clusters autónomos.

## <a name="overview"></a>Descrição geral

Introduzida na versão 6.2, o serviço de EventStore é uma opção de monitorização no Service Fabric. EventStore fornece uma forma de compreender o estado do seu cluster ou cargas de trabalho num determinado ponto no tempo. O EventStore é um serviço do Service Fabric com monitorização de estado que mantém os eventos do cluster. O evento são expostos por meio do Service Fabric Explorer, REST e APIs. EventStore consulta o cluster diretamente para obter dados de diagnóstico sobre qualquer entidade no seu cluster e deve ser utilizado para o ajudar a:

* Diagnosticar problemas em desenvolvimento ou teste, ou onde poderá estar a utilizar um pipeline de monitorização
* Confirme que as ações de gestão que está a efetuar no seu cluster estão a ser processadas corretamente
* Obtém um "instantâneo" de como o Service Fabric está a interagir com uma entidade específica

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Para ver uma lista completa de eventos disponíveis no EventStore, consulte [eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A partir do Service Fabric versão 6.2. as APIs de EventStore estão atualmente em pré-visualização para clusters do Windows em execução no Azure apenas. Estamos a trabalhar no portar essa funcionalidade para Linux, bem como nossos clusters autónomos.

O serviço de EventStore pode ser consultado para os eventos que estão disponíveis para cada entidade e tipo de entidade no seu cluster. Isso significa que pode consultar os eventos nos seguintes níveis:
* Cluster: eventos específicos para o cluster em si (por exemplo, a atualização de cluster)
* Nós: as todos os eventos ao nível de nó
* Nó: eventos específicos para um nó, identificado por `nodeName`
* Aplicações: as todos os eventos ao nível de aplicativo
* Aplicação: eventos específicos a um aplicativo identificado por `applicationId`
* Serviços: eventos de todos os serviços nos seus clusters
* Serviço: eventos um serviço específico identificado por `serviceId`
* Partições: eventos a partir de todas as partições
* Partição: eventos a partir de uma partição específica identificada pelo `partitionId`
* As réplicas de partições: eventos a partir de todas as réplicas / instâncias dentro de uma partição específica identificada por `partitionId`
* Réplica da partição: eventos a partir de uma réplica específico / instância identificado pelo `replicaId` e `partitionId`

Para saber mais sobre a API veja a [referência da API de EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

O serviço de EventStore também tem a capacidade de correlacionar eventos no seu cluster. Ao examinar os eventos que foram escritos em simultâneo de entidades diferentes que podem ter afetado entre si, o serviço de EventStore é capacidade de vincular esses eventos para o ajudar a identificar causas para as atividades no seu cluster. Por exemplo, se um de seus aplicativos acontecer para se tornar problemático sem quaisquer alterações induzidas, o EventStore será também a análise de outros eventos expostos pela plataforma e pode correlacionar isso com uma `Error` ou `Warning` eventos. Isto ajuda a com deteção de falhas mais rápida e análise de causa raiz.

## <a name="enable-eventstore-on-your-cluster"></a>Ativar EventStore no seu cluster

### <a name="local-cluster"></a>Local Cluster

Na [fabricSettings.json no seu cluster](service-fabric-cluster-fabric-settings.md), adicionar EventStoreService como uma funcionalidade do suplemento e executar uma atualização do cluster.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster"></a>Cluster do Azure

No modelo do Azure Resource Manager do seu cluster, pode ativar o serviço de EventStore ao efetuar uma [atualização de configuração do cluster](service-fabric-cluster-config-upgrade-azure.md) e adicionar o código seguinte. O `upgradeDescription` secção configura a atualização de configuração para acionar um reinício em nós. Pode remover a secção em outra atualização.

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


## <a name="next-steps"></a>Passos Seguintes
* Introdução à EventStore API - [com as APIs de EventStore em clusters do Azure Service Fabric](service-fabric-diagnostics-eventstore-query.md)
* Saiba mais sobre a lista de eventos oferecidos pelo EventStore - [eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* Descrição geral da monitorização e diagnóstico no Service Fabric - [monitorização e diagnóstico para o Service Fabric](service-fabric-diagnostics-overview.md)
* Ver a lista completa de chamadas à API - [referência da API REST do EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Saiba mais sobre a monitorização do seu cluster- [monitorizar o cluster e a plataforma](service-fabric-diagnostics-event-generation-infra.md).