---
title: 'Segurança do cluster do Service Fabric: funções do cliente | Documentos da Microsoft'
description: Este artigo descreve as funções do cliente de dois e as permissões fornecidas para as funções.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: ''
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ed000dc4be1ae45382d688d4a596ec745c69d0bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711163"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Controlo de acesso baseado em funções para clientes do Service Fabric
O Azure Service Fabric suporta dois tipos de controle de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e usuário. Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários, fazendo com que o cluster mais seguro.  

**Os administradores** ter acesso total às capacidades de gestão (incluindo recursos de leitura/gravação). Por predefinição, **utilizadores** apenas têm acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver a aplicações e serviços.

Especifique as funções do cliente de dois (cliente e de administrador) no momento da criação do cluster ao fornecer certificados separados para cada um. Ver [segurança de cluster do Service Fabric](service-fabric-cluster-security.md) para obter detalhes sobre como configurar um cluster do Service Fabric seguro.

## <a name="default-access-control-settings"></a>Definições de controlo de acesso predefinida
O tipo de controlo de acesso de administrador tem acesso total a todas as APIs FabricClient. Pode efetuar qualquer leituras e gravações no cluster do Service Fabric, incluindo as seguintes operações:

### <a name="application-and-service-operations"></a>Aplicação e as operações de serviço
* **CreateService**: a criação do serviço                             
* **CreateServiceFromTemplate**: criação do modelo de serviço                             
* **UpdateService**: as atualizações de serviço                             
* **DeleteService**: eliminação de serviço                             
* **ProvisionApplicationType**: aprovisionamento de tipo de aplicação                             
* **CreateApplication**: criação de aplicativos                               
* **DeleteApplication**: eliminação de aplicação                             
* **UpgradeApplication**: a iniciar ou interromper as atualizações de aplicações                             
* **UnprovisionApplicationType**: anulação do aprovisionamento tipo de aplicação                             
* **MoveNextUpgradeDomain**: a retomar as atualizações de aplicações com um domínio de atualização explícita                             
* **ReportUpgradeHealth**: a retomar as atualizações de aplicações com o progresso da atualização atual                             
* **ReportHealth**: relatório de estado de funcionamento                             
* **PredeployPackageToNode**: API da pré-implantação                            
* **CodePackageControl**: reiniciar pacotes do código                             
* **RecoverPartition**: recuperação de uma partição                             
* **RecoverPartitions**: recuperar partições                             
* **RecoverServicePartitions**: as partições do serviço de recuperação                             
* **RecoverSystemPartitions**: recuperar partições de serviço do sistema                             

### <a name="cluster-operations"></a>Operações de cluster
* **ProvisionFabric**: Cluster / MSI do manifesto de aprovisionamento                             
* **UpgradeFabric**: a partir de atualizações de cluster                             
* **UnprovisionFabric**: Cluster / MSI manifesto a anulação do aprovisionamento                         
* **MoveNextFabricUpgradeDomain**: a retomar atualizações de cluster com um domínio de atualização explícita                             
* **ReportFabricUpgradeHealth**: a retomar atualizações de cluster com o progresso da atualização atual                             
* **StartInfrastructureTask**: iniciar tarefas de infraestrutura                             
* **FinishInfrastructureTask**: a terminar tarefas de infraestrutura                             
* **InvokeInfrastructureCommand**: comandos de gestão de tarefas de infraestrutura                              
* **ActivateNode**: ativação de um nó                             
* **DeactivateNode**: desativar um nó                             
* **DeactivateNodesBatch**: desativar vários nós                             
* **RemoveNodeDeactivations**: a reverter desativação em vários nós                             
* **GetNodeDeactivationStatus**: a verificar o estado de Desativação                             
* **NodeStateRemoved**: relatório de estado do nó removido                             
* **ReportFault**: falha de geração de relatórios                             
* **FileContent**: a imagem de transferência de ficheiros de cliente do arquivo (externamente em relação ao cluster)                             
* **FileDownload**: a imagem de arquivo cliente ficheiro download inicialização (externo ao cluster)                             
* **InternalList**: armazenamento de arquivo lista operação do cliente (interno) de imagem                             
* **Eliminar**: operação de eliminação de cliente do arquivo de imagem                              
* **Carregar**: operação de carregamento de cliente do arquivo de imagem                             
* **NodeControl**: a iniciar, parar e reiniciar nós                             
* **MoveReplicaControl**: mover as réplicas de um nó para outro                             

### <a name="miscellaneous-operations"></a>Operações diversas
* **Ping**: pings de cliente                             
* **Consulta**: todas as consultas permitidas
* **NameExists**: verificações de existência do URI de nomenclatura                             

O tipo de controlo de acesso de utilizador é, por predefinição, limitada para as seguintes operações: 

* **EnumerateSubnames**: atribuição de nomes de enumeração de URI                             
* **EnumerateProperties**: atribuição de nomes de enumeração de propriedades                             
* **PropertyReadBatch**: atribuição de nomes de propriedade de operações de leitura                             
* **GetServiceDescription**: descrições de serviço de notificações do serviço de pesquisa de longa e leitura                             
* **ResolveService**: Resolução de serviço com base em conformidade                             
* **ResolveNameOwner**: Resolução proprietário URI de nomenclatura                             
* **ResolvePartition**: Resolução de serviços do sistema                             
* **ServiceNotifications**: notificações de serviço baseado em evento                             
* **GetUpgradeStatus**: Estado de atualização de aplicação de consulta                             
* **GetFabricUpgradeStatus**: consulta de estado de atualização de cluster                             
* **InvokeInfrastructureQuery**: consultar tarefas de infraestrutura                             
* **Lista**: operação de lista de ficheiros de cliente do arquivo de imagem                             
* **ResetPartitionLoad**: repor a carga para uma unidade de ativação pós-falha                             
* **ToggleVerboseServicePlacementHealthReporting**: a alteração de relatórios de estado de funcionamento do serviço verboso colocação                             

O controlo de acesso de administrador também tem acesso às operações anteriores.

## <a name="changing-default-settings-for-client-roles"></a>Alterar as configurações padrão para funções do cliente
O ficheiro de manifesto do cluster, pode fornecer capacidades de administrador para o cliente se for necessário. Pode alterar as predefinições ao aceder a **definições de recursos de infraestrutura** opção durante [criação de cluster](service-fabric-cluster-creation-via-portal.md)e fornecer as definições anteriores no **nome**,  **Admin**, **usuário**, e **valor** campos.

## <a name="next-steps"></a>Passos Seguintes
[Segurança do cluster do Service Fabric](service-fabric-cluster-security.md)

[Criação do cluster do Service Fabric](service-fabric-cluster-creation-via-portal.md)

