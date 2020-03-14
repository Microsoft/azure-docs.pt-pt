---
title: Lista de eventos de tecido de serviço Azure
description: Lista completa de eventos fornecidos pela Azure Service Fabric para ajudar a monitorizar os clusters.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258541"
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos de Tecido de Serviço 

Service Fabric expõe um conjunto primário de eventos de cluster para informá-lo do estado do seu cluster como [Eventos de Tecido de Serviço](service-fabric-diagnostics-events.md). Estas baseiam-se em ações realizadas pela Service Fabric nos seus nós e nas suas decisões de cluster ou gestão tomadas por um proprietário/operador de cluster. Estes eventos podem ser acedidos configurando de várias formas, incluindo configurar [os registos do Monitor Azure com o seu cluster,](service-fabric-diagnostics-oms-setup.md)ou consultando a [EventStore](service-fabric-diagnostics-eventstore.md). Nas máquinas Windows, estes eventos são alimentados no EventLog - para que possa ver eventos de tecido de serviço no Espectador de Eventos. 

Aqui estão algumas características destes eventos
* Cada evento está ligado a uma entidade específica no cluster, por exemplo, aplicação, serviço, nó, réplica.
* Cada evento contém um conjunto de campos comuns: EventInstanceId, EventName e Category.
* Cada evento contém campos que ligam o evento à entidade com a qual está associado. Por exemplo, o evento ApplicationCreated teria campos que identificassem o nome da aplicação criada.
* Os eventos são estruturados de forma a que possam ser consumidos numa variedade de ferramentas para fazer uma análise mais aprofundada. Além disso, os detalhes relevantes para um evento são definidos como propriedades separadas em oposição a uma longa Corda. 
* Os eventos são escritos por diferentes subsistemas em Tecido de Serviço são identificados pela Source (Task) abaixo. Mais informações estão disponíveis sobre estes subsistemas em Arquitetura de [Tecido de Serviço](service-fabric-architecture.md) e Visão Técnica de Tecido de [Serviço.](service-fabric-technical-overview.md)

Aqui está uma lista destes eventos de Tecido de Serviço organizados por entidade.

## <a name="cluster-events"></a>Eventos de cluster

**Eventos de upgrade de cluster**

Mais detalhes sobre upgrades de cluster podem ser encontrados [aqui](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Nome | Categoria | Descrição |Fonte (Tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Atualizar | Uma atualização de cluster começou | CM | Informativo |
| 29628 | ClusterUpgradeCompleted | Atualizar | Uma atualização de cluster completou | CM | Informativo | 
| 29629 | ClusterUpgradeRollbackStarted | Atualizar | Uma atualização de cluster começou a reverter  | CM | Aviso | 
| 29630 | ClusterUpgradeRollbackCompleted | Atualizar | Uma atualização de cluster completou a reversão | CM | Aviso | 
| 29631 | ClusterUpgradeDomainCompleted | Atualizar | Um domínio de upgrade terminou o upgrade durante uma atualização de cluster | CM | Informativo | 

## <a name="node-events"></a>Eventos de nó

**Eventos de ciclo de vida do nó** 

| EventId | Nome | Categoria | Descrição |Fonte (Tarefa) | Nível |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | Transição estatal | A desativação de um nó completou | FM | Informativo | 
| 18603 | NodeUp | Transição estatal | O aglomerado detetou que um nó começou. | FM | Informativo | 
| 18604 | NodeDown | Transição estatal | O aglomerado detetou que um nó fechou. Durante um reinício do nó, você verá um evento NodeDown seguido de um evento NodeUp |  FM | Erro | 
| 18605 | NodeAddedToCluster | Transição estatal |  Um novo nó foi adicionado ao cluster e o Tecido de Serviço pode implementar aplicações para este nó | FM | Informativo | 
| 18606 | NodeRemovedFromCluster | Transição estatal |  Um nó foi removido do aglomerado. Service Fabric deixará de implementar aplicações para este nó | FM | Informativo | 
| 18607 | NodeDeactivateStarted | Transição estatal |  A desativação de um nó começou | FM | Informativo | 
| 25621 | NodeOpenSucceeded | Transição estatal |  Um nó começou com sucesso | Nó de Tecido | Informativo | 
| 25622 | NodeOpenFailed | Transição estatal |  Um nó não começou e se juntou ao anel | Nó de Tecido | Erro | 
| 25624 | NodeClosed | Transição estatal |  Um nó fechou com sucesso | Nó de Tecido | Informativo | 
| 25626 | NodeAborted | Transição estatal |  Um nó fechou de forma vergonhosa | Nó de Tecido | Erro | 

## <a name="application-events"></a>Eventos de aplicação

**Eventos de ciclo de vida de aplicação**

| EventId | Nome | Categoria | Descrição |Fonte (Tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | Aplicações Criadas | Ciclo de Vida | Foi criada uma nova aplicação | CM | Informativo | 
| 29625 | AplicaçãoEliminada | Ciclo de Vida | Uma aplicação existente foi eliminada | CM | Informativo | 
| 23083 | ApplicationProcessExited | Ciclo de Vida | Um processo dentro de uma aplicação saiu | Alojamento | Informativo | 

**Eventos de upgrade de aplicações**

Mais detalhes sobre atualizações de aplicações podem ser encontrados [aqui](service-fabric-application-upgrade.md).

| EventId | Nome | Categoria | Descrição |Fonte (Tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | Upgrade de aplicaçõesIniciado | Atualizar | Uma atualização de aplicação já começou | CM | Informativo | 
| 29622 | ApplicationUpgradeCompleted | Atualizar | Uma atualização de aplicação concluída | CM | Informativo | 
| 29623 | ApplicationUpgradeRollbackStarted | Atualizar | Uma atualização de aplicações começou a reverter |CM | Aviso | 
| 29624 | ApplicationUpgradeRollbackCompleted | Atualizar | Uma atualização de aplicações completou a reversão | CM | Aviso | 
| 29626 | ApplicationUpgradeDomainCompleted | Atualizar | Um domínio de upgrade terminou a atualização durante uma atualização de aplicações | CM | Informativo | 

## <a name="service-events"></a>Eventos de serviço

**Eventos de ciclo de vida de serviço**

| EventId | Nome | Categoria | Descrição |Fonte (Tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Ciclo de Vida | Foi criado um novo serviço | FM | Informativo | 
| 18658 | ServiçoEliminado | Ciclo de Vida | Um serviço existente foi eliminado | FM | Informativo | 

## <a name="partition-events"></a>Eventos de partição

**Eventos de movimento de partição**

| EventId | Nome | Categoria | Descrição |Fonte (Tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Ciclo de Vida | Uma reconfiguração da divisória completou | RA | Informativo | 

## <a name="replica-events"></a>Eventos de réplica

**Replicar eventos de ciclo de vida**

| EventId | Nome | Categoria | Descrição |Fonte (Tarefa) | Nível |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Ciclo de Vida | Dicionário fiável abriu | Dicionário Distribuído | Informativo |
| 61702 | ReliableDictionaryClosed | Ciclo de Vida | Dicionário fiável fechou | Dicionário Distribuído | Informativo |
| 61703 | ReliableDictionaryCheckpointRecovered | Ciclo de Vida | O dicionário fiável recuperou o seu posto de controlo | Dicionário Distribuído | Informativo |
| 61704 | ReliableDictionaryCheckpointFilesSent | Ciclo de Vida | A réplica enviou ficheiros de controlo fiáveis do dicionário | Dicionário Distribuído | Informativo |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Ciclo de Vida | Réplica recebeu ficheiros de controlo fiáveis do dicionário | Dicionário Distribuído | Informativo |
| 61963 | Fila de Confiança Aberta | Ciclo de Vida | Abriu fila fiável | Fila distribuída | Informativo |
| 61964 | ReliableQueueClosed | Ciclo de Vida | Fila fiável fechou | Fila distribuída | Informativo |
| 61965 | ReliableQueueCheckpointRecovered | Ciclo de Vida | Fila fiável recuperou o seu posto de controlo | Fila distribuída | Informativo |
| 61966 | ReliableQueueCheckpointFilesSent | Ciclo de Vida | A réplica enviou ficheiros de controlo fiáveis da fila | Fila distribuída | Informativo |
| 63647 | ReliableQueueCheckpointFilesReceived | Ciclo de Vida | Réplica recebeu ficheiros de controlo de fila fiáveis | Fila distribuída | Informativo |
| 63648 | ReliableConcurrentQueueOpened | Ciclo de Vida | Abriu uma fila simultânea fiável | ReliableConcurrentQueue | Informativo |
| 63649 | ReliableConcurrentQueueClosed | Ciclo de Vida | Fila simultânea fiável fechou | ReliableConcurrentQueue | Informativo |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Ciclo de Vida | Fila simultânea fiável recuperou o seu posto de controlo | ReliableConcurrentQueue | Informativo |
| 61687 | TStoreError | Falha | A recolha fiável recebeu um erro inesperado | Loja tstore | Erro |
| 63831 | PrimeiraFullCopyIniciada | Ciclo de Vida | A réplica primária iniciou uma cópia completa | TReplicator | Informativo |
| 63832 | Cópia Parcial PrimáriaIniciada | Ciclo de Vida | A réplica primária iniciou uma cópia parcial. | TReplicator | Informativo |
| 16831 | BuildidleReplicaStarted | Ciclo de Vida | Réplica primária começou a construir réplica ociosa | Replicação | Informativo |
| 16832 | BuildIdleReplicaCompleted | Ciclo de Vida | Réplica primária completou réplica ociosa de edifício | Replicação | Informativo |
| 16833 | BuildIdleReplicaFailed | Ciclo de Vida | Réplica primária falhou a réplica inativa de construção | Replicação | Aviso |
| 16834 | PrimaryReplicationQueueFull | Estado de Funcionamento | A fila de replicação da réplica primária está cheia | Replicação | Aviso |
| 16835 | PrimaryReplicationQueueWarning | Estado de Funcionamento | A fila de replicação da réplica primária está quase cheia | Replicação | Aviso |
| 16836 | PrimaryReplicationQueueWarningMitigated | Estado de Funcionamento | A fila de replicação da réplica primária está bem. | Replicação | Informativo |
| 16837 | SecondaryReplicationQueueFull | Estado de Funcionamento | A fila de replicação da réplica secundária está cheia | Replicação | Aviso |
| 16838 | SecondaryReplicationQueueWarning | Estado de Funcionamento | A fila de replicação da réplica secundária está quase cheia | Replicação | Aviso |
| 16839 | SecondaryReplicationQueueWarningMitigated | Estado de Funcionamento | A fila de replicação da réplica secundária está bem | Replicação | Informativo |
| 16840 | PrimaryFaultedSlowSecondary | Estado de Funcionamento | A réplica primária falhou uma réplica secundária lenta. | Replicação | Aviso |
| 16841 | ReplicatorFaulted | Estado de Funcionamento | A réplica falhou. | Replicação | Aviso |

## <a name="container-events"></a>Eventos de contentores

**Eventos de ciclo de vida de contentores** 

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContentorAtivado | Um contentor começou | Alojamento | Informativo | 1 |
| 23075 | ContainerDeactivated | Um contentor parou. | Alojamento | Informativo | 1 |
| 23082 | Contentor-Brexit | Um contentor saiu - Verifique a bandeira de InesperadoTerm | Alojamento | Informativo | 1 |

## <a name="health-reports"></a>Relatórios de saúde

O Modelo de [Saúde](service-fabric-health-introduction.md) de Tecido de Serviço proporciona uma avaliação e reporte de saúde ricos, flexíveis e extensíveis. Iniciando a versão 6.2 do Service Fabric, os dados de saúde são escritos como eventos da Plataforma para fornecer registos históricos de saúde. Para manter o volume de eventos de saúde baixo, só escrevemos o seguinte como eventos de Tecido de Serviço:

* Todos os relatórios de saúde `Error` ou `Warning`
* `Ok` relatórios de saúde durante as transições
* Quando um evento de saúde `Error` ou `Warning` expira. Isto pode ser usado para determinar quanto tempo uma entidade não era saudável

**Eventos de relatório de saúde de cluster**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Um novo relatório de saúde do cluster está disponível | HM | Informativo | 1 |
| 54437 | ClusterHealthReportExpired | Um relatório de saúde de cluster existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde do nó**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Um novo relatório de saúde do nó está disponível | HM | Informativo | 1 |
| 54432 | NodeHealthReportExpired | Um relatório de saúde de nó existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde de aplicação**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Foi criado um novo relatório de saúde de aplicações. Isto é para aplicações não implantadas. | HM | Informativo | 1 |
| 54426 | DeployedApplicationNewHealthReport | Foi criado um novo relatório de saúde de aplicações implementado | HM | Informativo | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Foi criado um novo relatório de saúde de serviço saqueado | HM | Informativo | 1 |
| 54434 | ApplicationHealthReportExpired | Um relatório de saúde de aplicação existente expirou | HM | Informativo | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Expirou um relatório de saúde de aplicações implementado existente | HM | Informativo | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Um relatório de saúde de serviço implantado existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde de serviço**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Foi criado um novo relatório de saúde de serviço | HM | Informativo | 1 |
| 54433 | ServiceHealthReportExpired | Um relatório de saúde de serviço existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde de partição**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Foi criado um novo relatório de saúde de partição | HM | Informativo | 1 |
| 54431 | PartitionHealthReportExpired | Um relatório de saúde de partição existente expirou | HM | Informativo | 1 |

**Replice eventos de relatório de saúde**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Foi criado um relatório de saúde de réplica audamental | HM | Informativo | 1 |
| 54430 | StatelessInstanceNewHealthReport | Foi criado um novo relatório de saúde apátrida | HM | Informativo | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Um relatório de saúde de réplica estatal já expirou | HM | Informativo | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Um relatório de saúde apátrida existente expirou | HM | Informativo | 1 |

## <a name="chaos-testing-events"></a>Eventos de teste do caos 

**Eventos de sessão de caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50021 | CaosIniciado | Uma sessão de testes do Caos começou | Testabilidade | Informativo | 1 |
| 50023 | Caos Parado | Uma sessão de testes do Caos parou | Testabilidade | Informativo | 1 |

**Eventos de nó caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Um nó tem programado para reiniciar como parte de uma sessão de testes do Caos | Testabilidade | Informativo | 1 |
| 50087 | ChaosNodeRestartCompleted | Um nó terminou de reiniciar como parte de uma sessão de testes do Caos | Testabilidade | Informativo | 1 |

**Eventos de aplicação do caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Foi agendado um reinício do pacote de código durante uma sessão de testes do Caos | Testabilidade | Informativo | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Um pacote de código reiniciado foi concluído durante uma sessão de testes do Caos | Testabilidade | Informativo | 1 |

**Eventos de partição do caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Uma partição primária tem programado para mover-se como parte de uma sessão de testes do Caos | Testabilidade | Informativo | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Uma partição secundária tem programado para se mover como parte de uma sessão de testes do Caos | Testabilidade | Informativo | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | A análise mais profunda do movimento primário de partição está disponível | Testabilidade | Informativo | 1 |

**Eventos de réplica do caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Foi agendada uma réplica como parte de uma sessão de testes do Caos. | Testabilidade | Informativo | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Uma remoção de réplicafoi agendada como parte de uma sessão de testes do Caos | Testabilidade | Informativo | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Uma remoção de réplicas completou como parte de uma sessão de testes do Caos | Testabilidade | Informativo | 1 |

## <a name="other-events"></a>Outros eventos

**Eventos de correlação**

| EventId | Nome | Descrição |Fonte (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Foi detetada uma correlação | Testabilidade | Informativo | 1 |

## <a name="events-prior-to-version-62"></a>Eventos anteriores à versão 6.2

Aqui está uma lista completa de eventos fornecidos pelo Service Fabric antes da versão 6.2.

| EventId | Nome | Fonte (Tarefa) | Nível |
| --- | --- | --- | --- |
| 25620 | NodeOpening | Nó de Tecido | Informativo |
| 25621 | NodeOpenedSuccess | Nó de Tecido | Informativo |
| 25622 | NodeOpenedFailed | Nó de Tecido | Informativo |
| 25623 | NodeClosing | Nó de Tecido | Informativo |
| 25624 | NodeClosed | Nó de Tecido | Informativo |
| 25625 | NodeAborting | Nó de Tecido | Informativo |
| 25626 | NodeAborted | Nó de Tecido | Informativo |
| 29627 | ClusterUpgradeStart | CM | Informativo |
| 29628 | ClusterUpgradeComplete | CM | Informativo |
| 29629 | ClusterUpgradeRollback | CM | Informativo |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativo |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativo |
| 23074 | ContentorAtivado | Alojamento | Informativo |
| 23075 | ContainerDeactivated | Alojamento | Informativo |
| 29620 | Aplicações Criadas | CM | Informativo |
| 29621 | UpgradeStart de aplicações | CM | Informativo |
| 29622 | ApplicationUpgradeComplete | CM | Informativo |
| 29623 | ApplicationUpgradeRollback | CM | Informativo |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativo |
| 29625 | AplicaçãoEliminada | CM | Informativo |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativo |
| 18566 | ServiceCreated | FM | Informativo |
| 18567 | ServiçoEliminado | FM | Informativo |

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma visão geral dos [diagnósticos em Tecido de Serviço](service-fabric-diagnostics-overview.md)
* Saiba mais sobre a EventStore em [Função Fabric Eventstore Overview](service-fabric-diagnostics-eventstore.md)
* Modificar a configuração [de Diagnóstico Sinuoso Para](service-fabric-diagnostics-event-aggregation-wad.md) recolher mais registos
* [Configuração de Insights de Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md) para ver os registos dos seus canais operacionais
