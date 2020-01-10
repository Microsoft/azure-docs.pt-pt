---
title: Lista de eventos do Azure Service Fabric
description: Lista abrangente de eventos fornecidos pelo Azure Service Fabric para ajudar a monitorar clusters.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451766"
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos de Service Fabric 

Service Fabric expõe um conjunto primário de eventos de cluster para informá-lo sobre o status do cluster como [Service Fabric eventos](service-fabric-diagnostics-events.md). Elas se baseiam em ações executadas por Service Fabric em seus nós e nas decisões de gerenciamento ou cluster feitas por um operador/proprietário de cluster. Esses eventos podem ser acessados Configurando de várias maneiras, incluindo a configuração de [logs de Azure monitor com o cluster](service-fabric-diagnostics-oms-setup.md)ou a consulta do [EventStore](service-fabric-diagnostics-eventstore.md). Em computadores com Windows, esses eventos são inseridos no log de eventos, de modo que você pode ver Service Fabric evento no Visualizador de Eventos. 

Aqui estão algumas características desses eventos
* Cada evento é vinculado a uma entidade específica no cluster, por exemplo, aplicativo, serviço, nó, réplica.
* Cada evento contém um conjunto de campos comuns: EventInstanceId, EventName e Category.
* Cada evento contém campos que vinculam o evento à entidade à qual ele está associado. Por exemplo, o evento ApplicationCreated teria campos que identifiquem o nome do aplicativo criado.
* Os eventos são estruturados de forma que possam ser consumidos em várias ferramentas para realizar uma análise posterior. Além disso, os detalhes relevantes de um evento são definidos como propriedades separadas, em oposição a uma cadeia de caracteres longa. 
* Os eventos são gravados por diferentes subsistemas no Service Fabric são identificados pela origem (tarefa) abaixo. Mais informações estão disponíveis sobre esses subsistemas na [arquitetura de Service Fabric](service-fabric-architecture.md) e [Service Fabric visão geral técnica](service-fabric-technical-overview.md).

Aqui está uma lista desses Service Fabric eventos organizados por entidade.

## <a name="cluster-events"></a>Eventos de cluster

**Eventos de atualização de cluster**

Mais detalhes sobre atualizações de cluster podem ser encontrados [aqui](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Nome | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Atualização | Uma atualização de cluster foi iniciada | CM | Informativo |
| 29628 | ClusterUpgradeCompleted | Atualização | Atualização de cluster concluída | CM | Informativo | 
| 29629 | ClusterUpgradeRollbackStarted | Atualização | Uma atualização de cluster começou a ser revertida  | CM | Aviso | 
| 29630 | ClusterUpgradeRollbackCompleted | Atualização | Uma atualização de cluster concluiu A reversão | CM | Aviso | 
| 29631 | ClusterUpgradeDomainCompleted | Atualização | Um domínio de atualização concluiu a atualização durante uma atualização de cluster | CM | Informativo | 

## <a name="node-events"></a>Eventos de nó

**Eventos de ciclo de vida do nó** 

| EventId | Nome | Categoria | Descrição |Origem (tarefa) | Nível |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | A desativação de um nó foi concluída | FM | Informativo | 
| 18603 | NodeUp | StateTransition | O cluster detectou um nó iniciado | FM | Informativo | 
| 18604 | NodeDown | StateTransition | O cluster detectou que um nó foi desligado. Durante uma reinicialização de nó, você verá um evento NodeDown seguido por um evento NodeUp |  FM | Erro | 
| 18605 | NodeAddedToCluster | StateTransition |  Um novo nó foi adicionado ao cluster e Service Fabric pode implantar aplicativos nesse nó | FM | Informativo | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Um nó foi removido do cluster. Service Fabric não implantará mais aplicativos neste nó | FM | Informativo | 
| 18607 | NodeDeactivateStarted | StateTransition |  A desativação de um nó foi iniciada | FM | Informativo | 
| 25621 | NodeOpenSucceeded | StateTransition |  Um nó foi iniciado com êxito | FabricNode | Informativo | 
| 25622 | NodeOpenFailed | StateTransition |  Um nó falhou ao iniciar e ingressar no anel | FabricNode | Erro | 
| 25624 | NodeClosed | StateTransition |  Um nó foi desligado com êxito | FabricNode | Informativo | 
| 25626 | NodeAborted | StateTransition |  Um nó foi desligado innormalmente | FabricNode | Erro | 

## <a name="application-events"></a>Eventos de aplicativo

**Eventos do ciclo de vida do aplicativo**

| EventId | Nome | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | Um novo aplicativo foi criado | CM | Informativo | 
| 29625 | ApplicationDeleted | LifeCycle | Um aplicativo existente foi excluído | CM | Informativo | 
| 23083 | ApplicationProcessExited | LifeCycle | Um processo dentro de um aplicativo foi encerrado | Alojamento | Informativo | 

**Eventos de atualização de aplicativo**

Mais detalhes sobre atualizações de aplicativos podem ser encontrados [aqui](service-fabric-application-upgrade.md).

| EventId | Nome | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Atualização | Uma atualização de aplicativo foi iniciada | CM | Informativo | 
| 29622 | ApplicationUpgradeCompleted | Atualização | Uma atualização de aplicativo foi concluída | CM | Informativo | 
| 29623 | ApplicationUpgradeRollbackStarted | Atualização | Uma atualização de aplicativo começou a ser revertida |CM | Aviso | 
| 29624 | ApplicationUpgradeRollbackCompleted | Atualização | Uma atualização de aplicativo concluiu a reversão | CM | Aviso | 
| 29626 | ApplicationUpgradeDomainCompleted | Atualização | Um domínio de atualização concluiu a atualização durante uma atualização de aplicativo | CM | Informativo | 

## <a name="service-events"></a>Eventos de serviço

**Eventos de ciclo de vida do serviço**

| EventId | Nome | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | Um novo serviço foi criado | FM | Informativo | 
| 18658 | Excluído | LifeCycle | Um serviço existente foi excluído | FM | Informativo | 

## <a name="partition-events"></a>Eventos de partição

**Eventos de movimentação de partição**

| EventId | Nome | Categoria | Descrição |Origem (tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | Uma reconfiguração de partição foi concluída | EA | Informativo | 

## <a name="replica-events"></a>Eventos de réplica

**Eventos de ciclo de vida de réplica**

| EventId | Nome | Categoria | Descrição |Origem (tarefa) | Nível |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | O dicionário confiável foi aberto | DistributedDictionary | Informativo |
| 61702 | ReliableDictionaryClosed | LifeCycle | O dicionário confiável foi fechado | DistributedDictionary | Informativo |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | O dicionário confiável recuperou seu ponto de verificação | DistributedDictionary | Informativo |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | A réplica enviou arquivos de ponto de verificação de dicionário confiável | DistributedDictionary | Informativo |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | A réplica recebeu arquivos de ponto de verificação do dicionário confiável | DistributedDictionary | Informativo |
| 61963 | ReliableQueueOpened | LifeCycle | A fila confiável foi aberta | DistributedQueue | Informativo |
| 61964 | ReliableQueueClosed | LifeCycle | A fila confiável foi fechada | DistributedQueue | Informativo |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | A fila confiável recuperou seu ponto de verificação | DistributedQueue | Informativo |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | A réplica enviou arquivos de ponto de verificação da fila confiável | DistributedQueue | Informativo |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | A réplica recebeu arquivos de ponto de verificação da fila confiável | DistributedQueue | Informativo |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | A fila simultânea confiável foi aberta | ReliableConcurrentQueue | Informativo |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | A fila simultânea confiável foi fechada | ReliableConcurrentQueue | Informativo |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | A fila simultânea confiável recuperou seu ponto de verificação | ReliableConcurrentQueue | Informativo |
| 61687 | TStoreError | Falha | A coleta confiável recebeu um erro inesperado | TStore | Erro |
| 63831 | PrimaryFullCopyInitiated | LifeCycle | A réplica primária iniciou uma cópia completa | TReplicator | Informativo |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | A réplica primária iniciou uma cópia parcial | TReplicator | Informativo |
| 16831 | BuildIdleReplicaStarted | LifeCycle | A réplica primária começou a compilar a réplica ociosa | Replicação | Informativo |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | A réplica primária concluiu a compilação da réplica ociosa | Replicação | Informativo |
| 16833 | BuildIdleReplicaFailed | LifeCycle | A réplica primária falhou ao compilar a réplica ociosa | Replicação | Aviso |
| 16834 | PrimaryReplicationQueueFull | Saúde | A fila de replicação da réplica primária está cheia | Replicação | Aviso |
| 16835 | PrimaryReplicationQueueWarning | Saúde | A fila de replicação da réplica primária está quase cheia | Replicação | Aviso |
| 16836 | PrimaryReplicationQueueWarningMitigated | Saúde | A fila de replicação da réplica primária está ok | Replicação | Informativo |
| 16837 | SecondaryReplicationQueueFull | Saúde | A fila de replicação da réplica secundária está cheia | Replicação | Aviso |
| 16838 | SecondaryReplicationQueueWarning | Saúde | A fila de replicação da réplica secundária está quase cheia | Replicação | Aviso |
| 16839 | SecondaryReplicationQueueWarningMitigated | Saúde | A fila de replicação da réplica secundária está ok | Replicação | Informativo |
| 16840 | PrimaryFaultedSlowSecondary | Saúde | A réplica primária falhou em uma réplica secundária lenta | Replicação | Aviso |
| 16841 | ReplicatorFaulted | Saúde | Falha na réplica | Replicação | Aviso |

## <a name="container-events"></a>Eventos de contêiner

**Eventos do ciclo de vida do contêiner** 

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Um contêiner foi iniciado | Alojamento | Informativo | 1 |
| 23075 | ContainerDeactivated | Um contêiner foi interrompido | Alojamento | Informativo | 1 |
| 23082 | ContainerExited | Um contêiner foi encerrado-Verifique o sinalizador UnexpectedTermination | Alojamento | Informativo | 1 |

## <a name="health-reports"></a>Relatórios de integridade

O [modelo de integridade de Service Fabric](service-fabric-health-introduction.md) fornece uma avaliação de integridade avançada, flexível e extensível e relatórios. Iniciando Service Fabric versão 6,2, os dados de integridade são gravados como eventos de plataforma para fornecer registros históricos de integridade. Para manter o volume de eventos de integridade baixo, escrevemos o seguinte como Service Fabric eventos:

* Todos os relatórios de integridade de `Error` ou `Warning`
* `Ok` relatórios de integridade durante as transições
* Quando um evento de integridade de `Error` ou `Warning` expirar. Isso pode ser usado para determinar por quanto tempo uma entidade não estava íntegra

**Eventos do relatório de integridade do cluster**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Um novo relatório de integridade do cluster está disponível | HM | Informativo | 1 |
| 54437 | ClusterHealthReportExpired | Um relatório de integridade de cluster existente expirou | HM | Informativo | 1 |

**Eventos do relatório de integridade do nó**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Um novo relatório de integridade de nó está disponível | HM | Informativo | 1 |
| 54432 | NodeHealthReportExpired | Um relatório de integridade de nó existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade do aplicativo**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Um novo relatório de integridade do aplicativo foi criado. Isso é para aplicativos não implantados. | HM | Informativo | 1 |
| 54426 | DeployedApplicationNewHealthReport | Um novo relatório de integridade do aplicativo implantado foi criado | HM | Informativo | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Um novo relatório de integridade do serviço implantado foi criado | HM | Informativo | 1 |
| 54434 | ApplicationHealthReportExpired | Um relatório de integridade do aplicativo existente expirou | HM | Informativo | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Um relatório de integridade do aplicativo implantado existente expirou | HM | Informativo | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Um relatório de integridade do serviço implantado existente expirou | HM | Informativo | 1 |

**Eventos do relatório de integridade do serviço**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Um novo relatório de integridade do serviço foi criado | HM | Informativo | 1 |
| 54433 | ServiceHealthReportExpired | Um relatório de integridade do serviço existente expirou | HM | Informativo | 1 |

**Eventos do relatório de integridade da partição**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Um novo relatório de integridade de partição foi criado | HM | Informativo | 1 |
| 54431 | PartitionHealthReportExpired | Um relatório de integridade de partição existente expirou | HM | Informativo | 1 |

**Eventos do relatório de integridade da réplica**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Um relatório de integridade de réplica com estado foi criado | HM | Informativo | 1 |
| 54430 | StatelessInstanceNewHealthReport | Um novo relatório de integridade de instância sem estado foi criado | HM | Informativo | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Um relatório de integridade de réplica com estado existente expirou | HM | Informativo | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Um relatório de integridade de instância sem estado existente expirou | HM | Informativo | 1 |

## <a name="chaos-testing-events"></a>Eventos de teste de caos 

**Eventos de sessão de caos**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Uma sessão de teste de caos foi iniciada | Capacidade | Informativo | 1 |
| 50023 | ChaosStopped | Uma sessão de teste de caos parou | Capacidade | Informativo | 1 |

**Eventos de nó de caos**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Um nó agendou para reiniciar como parte de uma sessão de teste de caos | Capacidade | Informativo | 1 |
| 50087 | ChaosNodeRestartCompleted | Um nó concluiu a reinicialização como parte de uma sessão de teste de caos | Capacidade | Informativo | 1 |

**Eventos do aplicativo caos**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Uma reinicialização do pacote de código foi agendada durante uma sessão de teste de caos | Capacidade | Informativo | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Uma reinicialização do pacote de códigos foi concluída durante uma sessão de teste de caos | Capacidade | Informativo | 1 |

**Eventos de partição de caos**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Uma partição primária foi agendada para ser movida como parte de uma sessão de teste de caos | Capacidade | Informativo | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Uma partição secundária foi agendada para ser movida como parte de uma sessão de teste de caos | Capacidade | Informativo | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | A análise mais profunda da movimentação de partição primária está disponível | Capacidade | Informativo | 1 |

**Eventos de réplica de caos**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Uma reinicialização de réplica foi agendada como parte de uma sessão de teste de caos | Capacidade | Informativo | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Uma remoção de réplica foi agendada como parte de uma sessão de teste de caos | Capacidade | Informativo | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Uma remoção de réplica foi concluída como parte de uma sessão de teste de caos | Capacidade | Informativo | 1 |

## <a name="other-events"></a>Outros eventos

**Eventos de correlação**

| EventId | Nome | Descrição |Origem (tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Foi detectada uma correlação | Capacidade | Informativo | 1 |

## <a name="events-prior-to-version-62"></a>Eventos anteriores à versão 6,2

Aqui está uma lista abrangente de eventos fornecidos pelo Service Fabric antes da versão 6,2.

| EventId | Nome | Origem (tarefa) | Nível |
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
| 18567 | Excluído | FM | Informativo |

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma visão geral do [diagnóstico no Service Fabric](service-fabric-diagnostics-overview.md)
* Saiba mais sobre o EventStore na [visão geral do Service Fabric EventStore](service-fabric-diagnostics-eventstore.md)
* Modificando sua configuração de [diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para coletar mais logs
* [Configurando Application insights](service-fabric-diagnostics-event-analysis-appinsights.md) para ver seus logs de canal operacional
