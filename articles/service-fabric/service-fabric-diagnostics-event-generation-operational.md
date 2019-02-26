---
title: Lista de eventos de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Lista completa dos eventos fornecidos pelo Azure Service Fabric para ajudar a monitorizar clusters.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 51a334557ddfb57ceab318963e319fd046d3a703
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820047"
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos do Service Fabric 

Service Fabric expõe um conjunto principal de eventos de cluster para o informar do Estado do seu cluster como [eventos de Service Fabric](service-fabric-diagnostics-events.md). Estes são baseiam-se a ações executadas pelo Service Fabric no seu cluster e os nós ou decisões de gerenciamento feitas por um operador/proprietário de cluster. Esses eventos podem ser acedidos através da configuração de diversas formas, incluindo a configuração [Log Analytics com o seu cluster](service-fabric-diagnostics-oms-setup.md), ou consultando o [EventStore](service-fabric-diagnostics-eventstore.md). Nas máquinas do Windows, estes eventos são inseridos no registo de eventos - para que possa ver eventos no Visualizador de eventos de Service Fabric. 

Aqui estão algumas características destes eventos
* Cada evento está associado a uma entidade específica no cluster por exemplo, aplicações, serviço, nó, réplica.
* Cada evento contém um conjunto de campos comuns: EventInstanceId EventName e categoria.
* Cada evento contém campos que vincular o evento de volta para a entidade que está associado. Por exemplo, o evento de ApplicationCreated teria campos que identificam o nome da aplicação criada.
* Eventos são estruturados de tal forma que possam ser consumidas numa variedade de ferramentas para fazer ainda mais análises. Além disso, os detalhes relevantes para um evento são definidos como propriedades separadas em vez de uma cadeia longa. 
* Eventos são escritos por diferentes subsistemas no Service Fabric são identificados pelo Source(Task) abaixo. Estão disponíveis nestes subsistemas em mais informações [arquitetura do Service Fabric](service-fabric-architecture.md) e [descrição geral técnica do Service Fabric](service-fabric-technical-overview.md).

Aqui está uma lista desses eventos do Service Fabric organizados por entidade.

## <a name="cluster-events"></a>Eventos de cluster

**Eventos de atualização de cluster**

Obter mais detalhes sobre atualizações de cluster podem ser encontradas [aqui](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Name | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Atualizar | Foi iniciada uma atualização de cluster | CM | Informativo |
| 29628 | ClusterUpgradeCompleted | Atualizar | Uma atualização do cluster foi concluída | CM | Informativo | 
| 29629 | ClusterUpgradeRollbackStarted | Atualizar | Uma atualização do cluster foi iniciado para reversão  | CM | Aviso | 
| 29630 | ClusterUpgradeRollbackCompleted | Atualizar | Uma atualização do cluster foi concluída de reversão | CM | Aviso | 
| 29631 | ClusterUpgradeDomainCompleted | Atualizar | Um domínio de atualização concluiu a atualização durante uma atualização do cluster | CM | Informativo | 

## <a name="node-events"></a>Eventos de nó

**Eventos de ciclo de vida de nó** 

| EventId | Name | Categoria | Descrição |Origem (tarefa) | Nível |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Desativação de um nó foi concluída | FM | Informativo | 
| 18603 | NodeUp | StateTransition | O cluster detetou que um nó de ter iniciado | FM | Informativo | 
| 18604 | NodeDown | StateTransition | O cluster detetou que um nó foi encerrado. Durante um reinício do nó, verá um evento de NodeDown seguido de um evento de NodeUp |  FM | Erro | 
| 18605 | NodeAddedToCluster | StateTransition |  Foi adicionado um novo nó ao cluster e recursos de infraestrutura do serviço poderá implementar aplicações para este nó | FM | Informativo | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Foi removido um nó do cluster. Service Fabric já não irá implementar aplicações para este nó | FM | Informativo | 
| 18607 | NodeDeactivateStarted | StateTransition |  Desativação de um nó foi iniciada | FM | Informativo | 
| 25621 | NodeOpenSucceeded | StateTransition |  Um nó iniciado com êxito | FabricNode | Informativo | 
| 25622 | NodeOpenFailed | StateTransition |  Um nó não foi possível iniciar e Junte-se a cadência de | FabricNode | Erro | 
| 25624 | NodeClosed | StateTransition |  Um nó desligado com êxito | FabricNode | Informativo | 
| 25626 | NodeAborted | StateTransition |  Um nó de maneira brusca foi encerrado | FabricNode | Erro | 

## <a name="application-events"></a>Eventos da aplicação

**Eventos de ciclo de vida da aplicação**

| EventId | Name | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Ciclo de vida | Foi criada uma nova aplicação | CM | Informativo | 
| 29625 | ApplicationDeleted | Ciclo de vida | Um aplicativo existente foi eliminado | CM | Informativo | 
| 23083 | ApplicationProcessExited | Ciclo de vida | Um processo dentro de um aplicativo saiu | Alojamento | Informativo | 

**Eventos de atualização de aplicação**

Obter mais detalhes sobre as atualizações de aplicações podem ser encontradas [aqui](service-fabric-application-upgrade.md).

| EventId | Name | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Atualizar | Foi iniciada uma atualização da aplicação | CM | Informativo | 
| 29622 | ApplicationUpgradeCompleted | Atualizar | Uma atualização da aplicação foi concluída | CM | Informativo | 
| 29623 | ApplicationUpgradeRollbackStarted | Atualizar | Uma atualização da aplicação foi iniciada para reversão |CM | Aviso | 
| 29624 | ApplicationUpgradeRollbackCompleted | Atualizar | Uma atualização da aplicação foi concluída de reversão | CM | Aviso | 
| 29626 | ApplicationUpgradeDomainCompleted | Atualizar | Um domínio de atualização concluiu a atualização durante uma atualização da aplicação | CM | Informativo | 

## <a name="service-events"></a>Eventos do serviço

**Eventos de ciclo de vida do serviço**

| EventId | Name | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Ciclo de vida | Foi criado um novo serviço | FM | Informativo | 
| 18658 | ServiceDeleted | Ciclo de vida | Um serviço existente foi eliminado | FM | Informativo | 

## <a name="partition-events"></a>Eventos de partição

**Eventos de mudança de partição**

| EventId | Name | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Ciclo de vida | Foi concluída uma reconfiguração da partição | RA | Informativo | 

## <a name="container-events"></a>Eventos de contentor

**Eventos de ciclo de vida do contentor** 

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Um contentor foi iniciada | Alojamento | Informativo | 1 |
| 23075 | ContainerDeactivated | Um contentor foi parado | Alojamento | Informativo | 1 |
| 23082 | ContainerExited | Um contentor saiu - Verifique o sinalizador de UnexpectedTermination | Alojamento | Informativo | 1 |

## <a name="health-reports"></a>Relatórios de estado de funcionamento

O [modelo de serviço de estado de funcionamento de recursos de infraestrutura](service-fabric-health-introduction.md) fornece uma avaliação de integridade avançado, flexível e extensível e relatórios. Dados de estado de funcionamento a partir do Service Fabric versão 6.2, são escritos como eventos de plataforma para fornecer registos históricos de estado de funcionamento. Para manter o volume de eventos de estado de funcionamento baixa, podemos escrever apenas o seguinte como eventos do Service Fabric:

* Todos os `Error` ou `Warning` relatórios de estado de funcionamento
* `Ok` relatórios de estado de funcionamento durante as transições
* Quando um `Error` ou `Warning` expira de eventos de estado de funcionamento. Isso pode ser usado para determinar o tempo que uma entidade do era mau estado de funcionamento

**Eventos de relatório de estado de funcionamento do cluster**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Um novo relatório de estado de funcionamento do cluster está disponível | HM | Informativo | 1 |
| 54437 | ClusterHealthReportExpired | Expiração de um relatório de estado de funcionamento do cluster existente | HM | Informativo | 1 |

**Eventos de relatório de estado de funcionamento do nó**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Um relatório de estado de funcionamento do novo nó está disponível | HM | Informativo | 1 |
| 54432 | NodeHealthReportExpired | Expiração de um relatório de estado de funcionamento do nó existente | HM | Informativo | 1 |

**Eventos de relatório de estado de funcionamento de aplicações**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Foi criado um novo relatório de estado de funcionamento do aplicativo. Isto é para aplicações não implementadas. | HM | Informativo | 1 |
| 54426 | DeployedApplicationNewHealthReport | Foi criado um novo relatório de estado de funcionamento da aplicação implementada | HM | Informativo | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Foi criado um novo relatório de estado de funcionamento do serviço implementado | HM | Informativo | 1 |
| 54434 | ApplicationHealthReportExpired | Expiração de um relatório de estado de funcionamento de aplicação existente | HM | Informativo | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Um relatório de estado de funcionamento de aplicação implementada existente expirou | HM | Informativo | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Um relatório de estado de funcionamento do serviço implementado existente expirou | HM | Informativo | 1 |

**Eventos de relatório de estado de funcionamento do serviço**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Foi criado um novo relatório de estado de funcionamento de serviço | HM | Informativo | 1 |
| 54433 | ServiceHealthReportExpired | Expiração de um relatório de estado de funcionamento de serviço existente | HM | Informativo | 1 |

**Eventos de relatório de estado de funcionamento de partição**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Foi criado um novo relatório de estado de funcionamento de partição | HM | Informativo | 1 |
| 54431 | PartitionHealthReportExpired | Um relatório de estado de funcionamento de partição existente expirou | HM | Informativo | 1 |

**Eventos de relatório de estado de funcionamento de réplica**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Foi criado um relatório de estado de funcionamento de réplica com monitoração de estado | HM | Informativo | 1 |
| 54430 | StatelessInstanceNewHealthReport | Foi criado um novo relatório de estado de funcionamento de instância sem monitoração de estado | HM | Informativo | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Um relatório de estado de funcionamento com monitoração de estado de réplica existente expirou | HM | Informativo | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Um relatório de estado de funcionamento de instância sem monitoração de estado existente expirou | HM | Informativo | 1 |

## <a name="chaos-testing-events"></a>Eventos de testes de Chaos 

**Eventos de sessão de Chaos**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Foi iniciada uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |
| 50023 | ChaosStopped | Uma sessão de teste de Chaos parou | Capacidade de teste | Informativo | 1 |

**Eventos do nó de Chaos**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Um nó está agendado para reiniciar como parte de uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |
| 50087 | ChaosNodeRestartCompleted | Um nó concluiu a reiniciar como parte de uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |

**Eventos da aplicação caos**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Um reinício do pacote de código foi agendado durante uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Um reinício do pacote de código foi concluída durante uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |

**Eventos de partição caos**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Uma partição primária está agendado para mover como parte de uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Uma partição secundária está agendado para mover como parte de uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | A análise mais aprofundada da mudança de partição primária está disponível | Capacidade de teste | Informativo | 1 |

**Eventos da réplica caos**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Foi agendado um reinício de réplica como parte de uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Foi agendada uma remoção de réplica como parte de uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Uma remoção de réplica foi concluída como parte de uma sessão de teste de Chaos | Capacidade de teste | Informativo | 1 |

## <a name="other-events"></a>Outros eventos

**Eventos do correlacionador**

| EventId | Name | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Foi detetada uma correlação | Capacidade de teste | Informativo | 1 |

## <a name="events-prior-to-version-62"></a>Eventos anteriores à versão 6.2

Aqui está uma lista completa dos eventos fornecidos pelo Service Fabric anterior à versão 6.2.

| EventId | Name | Origem (tarefa) | Nível |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informativo |
| 25621 | NodeOpenedSuccess | FabricNode | Informativo |
| 25622 | NodeOpenedFailed | FabricNode | Informativo |
| 25623 | NodeClosing | FabricNode | Informativo |
| 25624 | NodeClosed | FabricNode | Informativo |
| 25625 | NodeAborting | FabricNode | Informativo |
| 25626 | NodeAborted | FabricNode | Informativo |
| 29627 | ClusterUpgradeStart | CM | Informativo |
| 29628 | ClusterUpgradeComplete | CM | Informativo |
| 29629 | ClusterUpgradeRollback | CM | Informativo |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativo |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativo |
| 23074 | ContainerActivated | Alojamento | Informativo |
| 23075 | ContainerDeactivated | Alojamento | Informativo |
| 29620 | ApplicationCreated | CM | Informativo |
| 29621 | ApplicationUpgradeStart | CM | Informativo |
| 29622 | ApplicationUpgradeComplete | CM | Informativo |
| 29623 | ApplicationUpgradeRollback | CM | Informativo |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativo |
| 29625 | ApplicationDeleted | CM | Informativo |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativo |
| 18566 | ServiceCreated | FM | Informativo |
| 18567 | ServiceDeleted | FM | Informativo |

## <a name="next-steps"></a>Passos Seguintes

* Obtenha uma visão geral de [diagnóstico no Service Fabric](service-fabric-diagnostics-overview.md)
* Saiba mais sobre o EventStore no [descrição geral do Service Fabric Eventstore](service-fabric-diagnostics-eventstore.md)
* Modificar seus [diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) configuração para recolher registos mais
* [Configurar o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para ver seu operacional registos de canal
