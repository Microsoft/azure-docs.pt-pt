---
title: Repositório de eventos do Azure Service Fabric
description: Saiba mais sobre o EventStore do Azure Service Fabric, uma maneira de entender e monitorar o estado de um cluster ou de cargas de trabalho a qualquer momento.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: d23c8114bf10ef3225775accef6910c0ba539e15
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645740"
---
# <a name="eventstore-overview"></a>Visão geral do EventStore

>[!NOTE]
>A partir da versão Service Fabric 6,4. as APIs EventStore só estão disponíveis para clusters do Windows em execução somente no Azure. Estamos trabalhando para portar essa funcionalidade para o Linux, bem como para nossos clusters autônomos.

## <a name="overview"></a>Visão geral

Introduzido na versão 6,2, o serviço EventStore é uma opção de monitoramento no Service Fabric. O EventStore fornece uma maneira de entender o estado do cluster ou das cargas de trabalho em um determinado momento.
O EventStore é um serviço de Service Fabric com estado que mantém eventos do cluster. O evento é exposto por meio do Service Fabric Explorer, REST e APIs. O EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade no cluster e deve ser usado para ajudar:

* Diagnosticar problemas no desenvolvimento ou teste, ou onde você pode estar usando um pipeline de monitoramento
* Confirmar que as ações de gerenciamento que você está executando no cluster estão sendo processadas corretamente
* Obter um "instantâneo" de como Service Fabric está interagindo com uma entidade específica

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Para ver uma lista completa de eventos disponíveis no EventStore, consulte [Service Fabric eventos](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A partir da versão Service Fabric 6,4. as APIs e a UX do EventStore estão geralmente disponíveis para clusters do Azure Windows. Estamos trabalhando para portar essa funcionalidade para o Linux, bem como para nossos clusters autônomos.

O serviço EventStore pode ser consultado para eventos que estão disponíveis para cada entidade e tipo de entidade no cluster. Isso significa que você pode consultar eventos nos seguintes níveis:
* Cluster: eventos específicos para o próprio cluster (por exemplo, atualização de cluster)
* Nós: todos os eventos de nível de nó
* Node: eventos específicos de um nó, identificados por `nodeName`
* Aplicativos: todos os eventos de nível de aplicativo
* Aplicativo: eventos específicos de um aplicativo identificado por `applicationId`
* Serviços: eventos de todos os serviços em seus clusters
* Serviço: eventos de um serviço específico identificado por `serviceId`
* Partições: eventos de todas as partições
* Partição: eventos de uma partição específica identificada por `partitionId`
* Réplicas de partição: eventos de todas as réplicas/instâncias dentro de uma partição específica identificada por `partitionId`
* Réplica de partição: eventos de uma instância/réplica específica identificadas por `replicaId` e `partitionId`

Para saber mais sobre a API, consulte a [referência da API do EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

O serviço EventStore também tem a capacidade de correlacionar eventos em seu cluster. Ao examinar os eventos que foram gravados ao mesmo tempo de entidades diferentes que podem ter impactado uns aos outros, o serviço EventStore é capaz de vincular esses eventos para ajudar a identificar as causas das atividades no cluster. Por exemplo, se um dos seus aplicativos se tornar não íntegro sem nenhuma alteração induzida, o EventStore também examinará outros eventos expostos pela plataforma e poderá correlacioná-lo com um evento `Error` ou `Warning`. Isso ajuda com a detecção mais rápida de falhas e a análise de causas raiz.

## <a name="enable-eventstore-on-your-cluster"></a>Habilitar EventStore em seu cluster

### <a name="local-cluster"></a>Cluster local

Em [fabricSettings. JSON em seu cluster](service-fabric-cluster-fabric-settings.md), adicione EventStoreService como um recurso de complemento e execute uma atualização de cluster.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Cluster do Azure versão 6.5 +
Se o cluster do Azure for atualizado para a versão 6,5 ou superior, o EventStore será habilitado automaticamente no cluster. Para recusar, você precisa atualizar o modelo de cluster com o seguinte:

* Usar uma versão de API do `2019-03-01` ou mais recente 
* Adicione o código a seguir à seção de propriedades no cluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Versão do cluster do Azure 6,4

Se você estiver usando a versão 6,4, poderá editar seu modelo de Azure Resource Manager para ativar o serviço EventStore. Isso é feito executando uma [atualização de configuração de cluster](service-fabric-cluster-config-upgrade-azure.md) e adicionando o código a seguir, você pode usar PlacementConstraints para colocar as réplicas do serviço EventStore em um NodeType específico, por exemplo, um NodeType dedicado para os serviços do sistema. A seção `upgradeDescription` configura a atualização de configuração para disparar uma reinicialização nos nós. Você pode remover a seção em outra atualização.

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
* Introdução à API do EventStore- [usando as APIs do EventStore no Azure Service Fabric clusters](service-fabric-diagnostics-eventstore-query.md)
* Saiba mais sobre a lista de eventos oferecidos por eventos de EventStore- [Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* Visão geral do monitoramento e diagnóstico no Service Fabric- [monitoramento e diagnóstico para Service Fabric](service-fabric-diagnostics-overview.md)
* Exibir a lista completa de chamadas de API- [referência da API REST do EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Saiba mais sobre como monitorar seu cluster – [monitorando o cluster e a plataforma](service-fabric-diagnostics-event-generation-infra.md).
