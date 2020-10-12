---
title: 'Segurança do cluster de tecido de serviço: funções do cliente'
description: Este artigo descreve as duas funções do cliente e as permissões fornecidas às funções.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451888"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Controlo de acesso baseado em funções para clientes de Tecidos de Serviço
A Azure Service Fabric suporta dois tipos diferentes de controlo de acesso para clientes que estão ligados a um cluster de Tecido de Serviço: administrador e utilizador. O controlo de acesso permite ao administrador do cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais seguro.  

**Os administradores** têm acesso total às capacidades de gestão (incluindo capacidades de leitura/escrita). Por padrão, **os utilizadores** apenas leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver aplicações e serviços.

Especifica as duas funções de cliente (administrador e cliente) no momento da criação do cluster, fornecendo certificados separados para cada um. Consulte [a segurança do cluster de tecido de serviço](service-fabric-cluster-security.md) para obter detalhes sobre a configuração de um cluster de tecido de serviço seguro.

## <a name="default-access-control-settings"></a>Definições de controlo de acesso predefinido
O tipo de controlo de acesso ao administrador tem acesso total a todas as APIs doNcimiente. Pode efetuar quaisquer leituras e escritos no cluster de Tecidos de Serviço, incluindo as seguintes operações:

### <a name="application-and-service-operations"></a>Operações de aplicação e serviço
* **CreateService**: criação de serviços                             
* **CreateServiceFromTemplate**: criação de serviço a partir do modelo                             
* **UpdateService**: atualizações de serviço                             
* **DeleteService**: eliminação de serviços                             
* **ProvisionApplicationType**: provisionamento do tipo de aplicação                             
* **CreateApplication**: criação de aplicações                               
* **DeleteApplication**: exclusão de aplicações                             
* **UpgradeApplicação**: iniciar ou interromper atualizações de aplicações                             
* **UnprovisionApplicationType**: tipo de aplicação não prospeção                             
* **MoveNextUpgradeDomain**: retomar atualizações de aplicações com um domínio de atualização explícita                             
* **ReportUpgradeHealth**: retomar as atualizações de aplicações com o progresso atual da atualização                             
* **ReportHealth**: reportando saúde                             
* **PredeployPackageToNode**: predeployment API                            
* **CodePackageControl**: reiniciar pacotes de código                             
* **RecuperaçãoPartição:** recuperação de uma partição                             
* **RecuperaçãoPartitions**: recuperação de divisórias                             
* **RecuperaçãoServicePartitions**: recuperação de divisórias de serviço                             
* **Recuperação Sistemapartitions**: recuperação de divisórias de serviço do sistema                             

### <a name="cluster-operations"></a>Operações de cluster
* **ProvisionFabric**: MSI e/ou provisão manifesta de cluster                             
* **UpgradeFabric**: atualizações de cluster inicial                             
* **UnprovisionFabric**: MSI e/ou cluster manifestam-se sem provisão                         
* **MoveNextFabricUpgradeDomain**: retomando atualizações de cluster com um domínio de atualização explícita                             
* **ReportFabricUpgradeHealth**: retomar as atualizações do cluster com o progresso atual da atualização                             
* **StartInfrastructureTask**: tarefas de infraestrutura de início                             
* **AcabamentoInfrastructureTask:** conclusão de tarefas de infraestrutura                             
* **InvokeInfrastructureCommand**: comandos de gestão de tarefas de infraestrutura                              
* **Ativar Node**: ativar um nó                             
* **DesactivadoNode:** desativar um nó                             
* **DesactivaçãoNodesBatch:** desativar múltiplos nosdes                             
* **RemoveNodesactivações: desativação**revertida em múltiplos nosmos                             
* **GetNodeActivationStatus**: verificar o estado de desativação                             
* **NodeStateRemoved**: estado do nó reportado removido                             
* **ReportFault**: falha de reporte                             
* **FileContent**: transferência de ficheiros do cliente da loja de imagens (externa ao cluster)                             
* **FileDownload**: iniciação do download de ficheiros do cliente da loja de imagens (externo ao cluster)                             
* **InternalList**: operação da lista de ficheiros do cliente da loja de imagens (interna)                             
* **Excluir**: operação de exclusão do cliente da loja de imagens                              
* **Upload**: Operação de upload do cliente da loja de imagens                             
* **NodeControl**: iniciar, parar e reiniciar os nosdes                             
* **MoveReplicaControl**: réplicas móveis de um nó para outro                             

### <a name="miscellaneous-operations"></a>Operações diversas
* **Ping**: pings de cliente                             
* **Consulta:** todas as consultas permitidas
* **NomeExists**: nomear verificações de existência URI                             

O tipo de controlo de acesso ao utilizador limita-se, por padrão, às seguintes operações: 

* **EnumerateSubnames**: nomeação da enumeração URI                             
* **EnumeraçõesProperties**: enumeração de propriedade                             
* **PropertyReadBatch**: nomear operações de leitura de propriedade                             
* **GetServiceDescription**: notificações de serviço de sondagem longas e descrições do serviço de leitura                             
* **ResolveService**: resolução de serviços baseada em reclamações                             
* **ResolveNameOwner**: resolver o nome do proprietário da URI                             
* **ResolvePartition**: resolução de serviços de sistema                             
* **Notificação de serviços**: notificações de serviço baseadas em eventos                             
* **GetUpgradeStatus**: estado de atualização de aplicações de sondagens                             
* **GetFabricUpgradeStatus**: estado de atualização do cluster de sondagens                             
* **Invocar a Infraestruturas de Agricultura :** consulta de tarefas de infraestrutura                             
* **Lista**: Operação da lista de ficheiros do cliente da loja de imagens                             
* **ResetPartitionLoad**: reposição da carga para uma unidade de failover                             
* **ToggleVerboseServicePlacementHealthReporting:** toggling verbose service placement health reporting                             

O comando de acesso administrativo também tem acesso às operações anteriores.

## <a name="changing-default-settings-for-client-roles"></a>Alterar definições padrão para funções de cliente
No ficheiro manifesto do cluster, pode fornecer capacidades de administração ao cliente, se necessário. Pode alterar as predefinições indo para a opção **Definições** de Tecido durante a [criação](service-fabric-cluster-creation-via-portal.md)do cluster , e fornecendo as definições anteriores no **nome**, **administração,** **utilizador**, e campos **de valor.**

## <a name="next-steps"></a>Passos seguintes
[Segurança do cluster de tecido de serviço](service-fabric-cluster-security.md)

[Criação de cluster de tecido de serviço](service-fabric-cluster-creation-via-portal.md)

