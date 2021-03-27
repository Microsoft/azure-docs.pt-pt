---
title: Lista de eventos de tecido de serviço Azure
description: Lista completa de eventos fornecidos pela Azure Service Fabric para ajudar a monitorizar os clusters.
ms.topic: reference
ms.date: 2/25/2019
ms.openlocfilehash: 3f65d7c3ddec8263ff6f115df9616b48814afc93
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628888"
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos de Tecido de Serviço 

A Service Fabric expõe um conjunto primário de eventos de cluster para o informar do estado do seu cluster como [Eventos de Tecido de Serviço.](service-fabric-diagnostics-events.md) Estas baseiam-se em ações realizadas pela Service Fabric nos seus nós e no seu cluster ou decisões de gestão tomadas por um proprietário/operador de cluster. Estes eventos podem ser acedidos configurando de várias formas, incluindo configurar [registos do Azure Monitor com](service-fabric-diagnostics-oms-setup.md)o seu cluster, ou consultar o [EventStore](service-fabric-diagnostics-eventstore.md). Nas máquinas Windows, estes eventos são introduzidos no EventLog - para que possa ver Eventos de Tecido de Serviço no Espectador de Eventos. 

Aqui estão algumas características destes eventos
* Cada evento está ligado a uma entidade específica no cluster, por exemplo, Aplicação, Serviço, Nó, Réplica.
* Cada evento contém um conjunto de campos comuns: EventInstanceId, EventName e Category.
* Cada evento contém campos que ligam o evento à entidade a que está associado. Por exemplo, o evento ApplicationCreated teria campos que identificassem o nome da aplicação criada.
* Os eventos são estruturados de forma a que possam ser consumidos numa variedade de ferramentas para fazer uma análise mais aprofundada. Além disso, detalhes relevantes para um evento são definidos como propriedades separadas em oposição a uma longa Corda. 
* Os eventos são escritos por diferentes subsistemas em Tecido de Serviço são identificados por Fonte(Tarefa) abaixo. Mais informações estão disponíveis nestes subsistemas em [Service Fabric Architecture](service-fabric-architecture.md) and Service Fabric Technical [Visão Geral](service-fabric-technical-overview.md).

Aqui está uma lista destes eventos de Tecido de Serviço organizados por entidade.

## <a name="cluster-events"></a>Eventos de cluster

**Eventos de upgrade de cluster**

Mais detalhes sobre upgrades de cluster podem ser encontrados [aqui.](service-fabric-cluster-upgrade-windows-server.md)

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Atualizar | Uma atualização de cluster começou | CM | Informativo |
| 29628 | ClusterUpgradeCompleted | Atualizar | Uma atualização de cluster concluída | CM | Informativo | 
| 29629 | ClusterUpgradeRollbackStarted | Atualizar | Uma atualização de cluster começou a reverter  | CM | Aviso | 
| 29630 | ClusterUpgradeRollbackCompleted | Atualizar | Uma atualização de cluster completou o reversão | CM | Aviso | 
| 29631 | ClusterUpgradeDomainCompleted | Atualizar | Um domínio de upgrade terminou a atualização durante uma atualização de cluster | CM | Informativo | 

## <a name="node-events"></a>Eventos de nó

**Eventos de ciclo de vida do nó** 

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NódedeactivadoCompleto | Transação Estatal | Desativação de um nó concluído | FM | Informativo | 
| 18603 | NodeUp | Transação Estatal | O cluster detetou um nó iniciado | FM | Informativo | 
| 18604 | NodeDown | Transação Estatal | O aglomerado detetou um nó desligado. Durante um recomeço do nó, você verá um evento NodeDown seguido de um evento NodeUp |  FM | Erro | 
| 18605 | NodeAddedToCluster | Transação Estatal |  Um novo nó foi adicionado ao cluster e o Tecido de Serviço pode implementar aplicações neste nó | FM | Informativo | 
| 18606 | NodeRemovedFromCluster | Transação Estatal |  Um nó foi removido do aglomerado. O Service Fabric deixará de implementar aplicações neste nó | FM | Informativo | 
| 18607 | NódedeactivadoStar | Transação Estatal |  A desativação de um nó começou | FM | Informativo | 
| 25621 | Não-Apensado | Transação Estatal |  Um nó começou com sucesso | FabricNode | Informativo | 
| 25622 | NodeOpenFailed | Transação Estatal |  Um nó falhou em começar e juntar-se ao anel | FabricNode | Erro | 
| 25624 | Nó Fechado | Transação Estatal |  Um nó desligado com sucesso | FabricNode | Informativo | 
| 25626 | NodeAborted | Transação Estatal |  Um nó fechou-se sem gravidade. | FabricNode | Erro | 

**Eventos de nó de nogger ktl** 

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level |
| --- | --- | --- | --- | --- | --- | 
| 50187 | SharedLogWriteThrottled | Saúde | As gravações para o registo partilhado estão a estrangular | KtlLoggerNode | Informativo | 
| 50188 | SharedLogWriteUnthrottled | Saúde | As gravações para o registo partilhado não têm sido destronar | KtlLoggerNode | Informativo | 

## <a name="application-events"></a>Eventos de aplicação

**Eventos de ciclo de vida da aplicação**

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | Inscrição de candidaturas | Ciclo de Vida | Foi criada uma nova aplicação | CM | Informativo | 
| 29625 | AplicaçãoDeletada | Ciclo de Vida | Um pedido existente foi eliminado | CM | Informativo | 
| 23083 | Processamento de aplicaçõesExited | Ciclo de Vida | Um processo dentro de uma aplicação saiu | Alojamento | Informativo | 

**Eventos de atualização de aplicações**

Mais detalhes sobre atualizações de aplicações podem ser encontrados [aqui.](service-fabric-application-upgrade.md)

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | AplicaçãoUpgradeStar | Atualizar | Uma atualização de aplicação já começou | CM | Informativo | 
| 29622 | AplicaçãoUpgradadoCompleto | Atualizar | Uma atualização de aplicações foi concluída | CM | Informativo | 
| 29623 | ApplicationUpgradeRollbackStarted | Atualizar | Uma atualização de aplicações começou a ser reversão |CM | Aviso | 
| 29624 | ApplicationUpgradeRollbackCompleted | Atualizar | Uma atualização de aplicações completou o reversão | CM | Aviso | 
| 29626 | ApplicationUpgradeDomainCompleted | Atualizar | Um domínio de upgrade terminou a atualização durante uma atualização de aplicação | CM | Informativo | 

## <a name="service-events"></a>Eventos de serviço

**Eventos de ciclo de vida de serviço**

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiçosCretados | Ciclo de Vida | Foi criado um novo serviço | FM | Informativo | 
| 18658 | ServiçoDeletado | Ciclo de Vida | Um serviço existente foi eliminado | FM | Informativo | 

## <a name="partition-events"></a>Eventos de partição

**Eventos de movimento de partição**

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartiçãoReconfigurada | Ciclo de Vida | Uma reconfiguração de partição terminou | RA | Informativo | 

## <a name="replica-events"></a>Eventos de réplica

**Réplica de eventos de ciclo de vida**

| EventId | Name | Categoria | Descrição |Fonte (Tarefa) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Ciclo de Vida | Dicionário fiável abriu | Distribuídoddicary | Informativo |
| 61702 | ReliableDictionaryClosed | Ciclo de Vida | Dicionário fiável fechou | Distribuídoddicary | Informativo |
| 61703 | ReliableDictionaryCheckpointRecovered | Ciclo de Vida | Dicionário fiável recuperou o seu ponto de verificação | Distribuídoddicary | Informativo |
| 61704 | ReliableDictionaryCheckpointFilessent | Ciclo de Vida | A réplica enviou ficheiros de verificação fiáveis do dicionário | Distribuídoddicary | Informativo |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Ciclo de Vida | A réplica recebeu ficheiros de checkpoint fiáveis do dicionário | Distribuídoddicary | Informativo |
| 61963 | ReliableQueueOpened | Ciclo de Vida | Fila fiável abriu | DistribuídoSSEue | Informativo |
| 61964 | ReliableQueueClosed | Ciclo de Vida | Fila fiável fechou | DistribuídoSSEue | Informativo |
| 61965 | ReliableQueueCheckpointRecovered | Ciclo de Vida | Fila fiável recuperou o seu posto de controlo | DistribuídoSSEue | Informativo |
| 61966 | ReliableQueueCheckpointFilessent | Ciclo de Vida | A réplica enviou ficheiros de checkpoint de fila fiáveis | DistribuídoSSEue | Informativo |
| 63647 | ReliableQueueCheckpointFilesReceived | Ciclo de Vida | A réplica recebeu ficheiros de checkpoint de fila fiáveis | DistribuídoSSEue | Informativo |
| 63648 | ReliableConcurrentQueueO adoção | Ciclo de Vida | Fila simultânea fiável abriu | ReliableConcurrentQueue | Informativo |
| 63649 | ReliableConcurrentQueueClosed | Ciclo de Vida | Fila simultânea fiável fechou | ReliableConcurrentQueue | Informativo |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Ciclo de Vida | Fila simultânea fiável recuperou o seu posto de controlo | ReliableConcurrentQueue | Informativo |
| 61687 | TStoreError | Falha | Coleção fiável recebeu um erro inesperado | TStore | Erro |
| 63831 | PrimárioFullCopyInitiado | Ciclo de Vida | A réplica primária iniciou uma cópia completa | TReplicator | Informativo |
| 63832 | PrimaryPartialCopyInitiated | Ciclo de Vida | Réplica primária iniciou uma cópia parcial | TReplicator | Informativo |
| 16831 | BuildIdleReplicaStarted | Ciclo de Vida | Réplica primária começou a construir réplica ociosa | Replicação | Informativo |
| 16832 | BuildIdleReplicaCompleted | Ciclo de Vida | Réplica primária completou a réplica ociosa do edifício | Replicação | Informativo |
| 16833 | BuildIdleReplicaFailed | Ciclo de Vida | Réplica primária falhou na réplica ociosa da construção | Replicação | Aviso |
| 16834 | PrimaryReplicationQueueFull | Saúde | A fila de replicação da réplica primária está cheia | Replicação | Aviso |
| 16835 | PrimaryReplicationQueueWarning | Saúde | A fila de replicação da réplica primária está quase cheia | Replicação | Aviso |
| 16836 | PrimaryReplicationQueueWarningMitigated | Saúde | A fila de replicação da réplica primária está bem. | Replicação | Informativo |
| 16837 | Respeção SecundáriaQueueFull | Saúde | A fila de replicação da réplica secundária está cheia | Replicação | Aviso |
| 16838 | Respritosesaqueue | Saúde | A fila de replicação da réplica secundária está quase cheia | Replicação | Aviso |
| 16839 | SegundaReplicaçãoQueueAaamitigada | Saúde | A fila de replicação da réplica secundária está bem. | Replicação | Informativo |
| 16840 | PrimárioFaultedSlowSecondary | Saúde | Réplica primária falhou uma réplica secundária lenta | Replicação | Aviso |
| 16841 | ReplicatorFaulted | Saúde | A réplica falhou | Replicação | Aviso |

## <a name="container-events"></a>Eventos de contentores

**Eventos de ciclo de vida de contentores** 

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContentorEsActivados | Um contentor começou | Alojamento | Informativo | 1 |
| 23075 | ContentorEsaactivado | Um contentor parou | Alojamento | Informativo | 1 |
| 23082 | ContentorExited | Um contentor saiu - Verifique a bandeira de Terminação Inesperada | Alojamento | Informativo | 1 |

## <a name="health-reports"></a>Relatórios de saúde

O [Modelo de Saúde do Tecido](service-fabric-health-introduction.md) de Serviço proporciona uma avaliação e reporte de saúde rica, flexível e extensível. Início do Tecido de Serviço versão 6.2, os dados de saúde são escritos como eventos da Plataforma para fornecer registos históricos de saúde. Para manter o volume de eventos de saúde baixo, só escrevemos os seguintes como eventos de Service Fabric:

* Todos `Error` ou `Warning` relatórios de saúde
* `Ok` relatórios de saúde durante as transições
* Quando um `Error` evento de `Warning` saúde expirar. Isto pode ser usado para determinar quanto tempo uma entidade não era saudável

**Eventos de relatório de saúde de cluster**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Um novo relatório de saúde do cluster está disponível | HM | Informativo | 1 |
| 54437 | ClusterHealthReportExpired | Um relatório de saúde do cluster existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde do nó**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Um novo relatório de saúde do nó está disponível | HM | Informativo | 1 |
| 54432 | NodeHealthReportExpired | Um relatório de saúde do nó existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde de aplicação**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Foi criado um novo relatório de saúde sobre aplicações. Isto é para aplicações não desocupadas. | HM | Informativo | 1 |
| 54426 | ImplementadoApplicationNewHealthReport | Foi criado um novo relatório de saúde sobre aplicações | HM | Informativo | 1 |
| 54427 | Implementado ServiçoPackageNewHealthReport | Foi criado um novo relatório de saúde de serviços implantado | HM | Informativo | 1 |
| 54434 | AplicaçãoHealthReportExpired | Um relatório de saúde de aplicação existente expirou | HM | Informativo | 1 |
| 54435 | ImplementadoApplicationHealthReportExpired | Expirou um relatório de saúde de aplicação existente | HM | Informativo | 1 |
| 54436 | Implementado ServiçoPackageHealthReportExpired | Um relatório de saúde de serviço implementado existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde de serviço**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Foi criado um novo relatório de saúde de serviço | HM | Informativo | 1 |
| 54433 | ServiceHealthReportExpired | Um relatório de saúde de serviço existente expirou | HM | Informativo | 1 |

**Eventos de relatório de saúde de partição**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Foi criado um novo relatório de saúde de partição | HM | Informativo | 1 |
| 54431 | PartitionHealthReportExpired | Um relatório de saúde de partição existente expirou | HM | Informativo | 1 |

**Réplica de eventos de relatório de saúde**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Foi criado um relatório de saúde de réplica imponente | HM | Informativo | 1 |
| 54430 | Reportagem de Atimância NovaHealthReport | Foi criado um novo relatório de saúde apátrida | HM | Informativo | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Um relatório de saúde de réplica imponente existente expirou | HM | Informativo | 1 |
| 54439 | AaulessInstanceHealthReportExpired | Um relatório de saúde de caso apátrida existente expirou | HM | Informativo | 1 |

## <a name="chaos-testing-events"></a>Eventos de teste do caos 

**Eventos de sessão de caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 50021 | CaosStared | Começou uma sessão de testes caos | Capacidade de teste | Informativo | 1 |
| 50023 | Caos coberto | Uma sessão de testes do Caos parou | Capacidade de teste | Informativo | 1 |

**Eventos de nó caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 50033 | CaosNodeRestartscheed | Um nó tem programado para reiniciar como parte de uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |
| 50087 | CaosNodeRestartCompleted | Um nó terminou de reiniciar como parte de uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |

**Eventos de aplicação caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 50053 | CaosCodePackagerestartScheded | Foi agendado um reinício de um pacote de código durante uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |
| 50101 | CaosCodePackageRestartCompleted | Um pacote de código reiniciou durante uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |

**Eventos de partição do caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 50069 | CaosPartitionPrimaryMoveScheduled | Uma partição primária tem programado mover-se como parte de uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |
| 50077 | CaosPartitionSecondaryMoveScheduled | Uma partição secundária tem programado mover-se como parte de uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | A análise mais profunda do movimento de partição primária está disponível | Capacidade de teste | Informativo | 1 |

**Eventos de réplica do caos**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 50047 | CaosReplicarestartsa agendado | Foi agendado um reinício de réplica como parte de uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |
| 50051 | CaosReplicaRemovalSchedado | Foi agendada uma remoção de réplicas como parte de uma sessão de testes do Caos. | Capacidade de teste | Informativo | 1 |
| 50093 | CaosReplicaRemovalCompleted | Uma remoção de réplica concluída como parte de uma sessão de testes do Caos | Capacidade de teste | Informativo | 1 |

## <a name="other-events"></a>Outros eventos

**Eventos de correlação**

| EventId | Nome | Descrição |Fonte (Tarefa) | Level | Versão |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelaçãoOperacional | Foi detetada uma correlação | Capacidade de teste | Informativo | 1 |

## <a name="events-prior-to-version-62"></a>Eventos antes da versão 6.2

Aqui está uma lista completa de eventos fornecidos pela Service Fabric antes da versão 6.2.

| EventId | Name | Fonte (Tarefa) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informativo |
| 25621 | NodeOedSuccess | FabricNode | Informativo |
| 25622 | Nódoa adea | FabricNode | Informativo |
| 25623 | NodeClosing | FabricNode | Informativo |
| 25624 | Nó Fechado | FabricNode | Informativo |
| 25625 | NodeAborting | FabricNode | Informativo |
| 25626 | NodeAborted | FabricNode | Informativo |
| 29627 | ClusterUpgradeStart | CM | Informativo |
| 29628 | ClusterUpgradeComplete | CM | Informativo |
| 29629 | ClusterUpgradeRollback | CM | Informativo |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativo |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativo |
| 23074 | ContentorEsActivados | Alojamento | Informativo |
| 23075 | ContentorEsaactivado | Alojamento | Informativo |
| 29620 | Inscrição de candidaturas | CM | Informativo |
| 29621 | ApplicationUpgradeStart | CM | Informativo |
| 29622 | ApplicationUpgradeComplete | CM | Informativo |
| 29623 | AplicaçãoUpgradeRollback | CM | Informativo |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativo |
| 29625 | AplicaçãoDeletada | CM | Informativo |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativo |
| 18566 | ServiçosCretados | FM | Informativo |
| 18567 | ServiçoDeletado | FM | Informativo |

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma visão geral dos [diagnósticos em Tecido de Serviço](service-fabric-diagnostics-overview.md)
* Saiba mais sobre o EventStore in [Service Fabric Eventstore Overview](service-fabric-diagnostics-eventstore.md)
* Modificando a sua configuração [de Diagnóstico Azure](service-fabric-diagnostics-event-aggregation-wad.md) para recolher mais registos
* [Configuração de Insights de Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md) para ver os seus registos de canais operacionais
