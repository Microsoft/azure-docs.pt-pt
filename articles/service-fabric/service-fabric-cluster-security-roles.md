---
title: 'Segurança de Cluster Service Fabric: funções de cliente | Microsoft Docs'
description: Este artigo descreve as duas funções de cliente e as permissões fornecidas para as funções.
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
ms.author: atsenthi
ms.openlocfilehash: 38656d286cae631cb5def0e0c8b171268e4cf428
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167255"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Controle de acesso baseado em função para clientes Service Fabric
O Azure Service Fabric dá suporte a dois tipos diferentes de controle de acesso para clientes que estão conectados a um Cluster Service Fabric: administrador e usuário. O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.  

**Os administradores** têm acesso completo aos recursos de gerenciamento (incluindo os recursos de leitura/gravação). Por padrão, **os usuários** só têm acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços.

Você especifica as duas funções de cliente (administrador e cliente) no momento da criação do cluster, fornecendo certificados separados para cada um. Consulte [Service Fabric segurança de cluster](service-fabric-cluster-security.md) para obter detalhes sobre como configurar um cluster de Service Fabric seguro.

## <a name="default-access-control-settings"></a>Configurações de controle de acesso padrão
O tipo de controle de acesso de administrador tem acesso completo a todas as APIs FabricClient. Ele pode executar qualquer leitura e gravação no Cluster Service Fabric, incluindo as seguintes operações:

### <a name="application-and-service-operations"></a>Operações de aplicativo e serviço
* **CreateService**: criação de serviço                             
* **CreateServiceFromTemplate**: criação de serviço do modelo                             
* **UpdateService**: atualizações de serviço                             
* **DeleteService**: exclusão de serviço                             
* **ProvisionApplicationType**: provisionamento de tipo de aplicativo                             
* **Createapplication**: criação de aplicativo                               
* **DeleteApplication**: exclusão de aplicativo                             
* **UpgradeApplication**: Iniciando ou interrompendo atualizações do aplicativo                             
* **UnprovisionApplicationType**: desprovisionamento de tipo de aplicativo                             
* **MoveNextUpgradeDomain**: retomando atualizações de aplicativo com um domínio de atualização explícito                             
* **ReportUpgradeHealth**: retomando atualizações de aplicativo com o andamento da atualização atual                             
* **ReportHealth**: integridade do relatório                             
* **PredeployPackageToNode**: API de pré-implantação                            
* **CodePackageControl**: reinicialização de pacotes de código                             
* **RecoverPartition**: Recuperando uma partição                             
* **RecoverPartitions**: Recuperando partições                             
* **RecoverServicePartitions**: Recuperando partições de serviço                             
* **RecoverSystemPartitions**: Recuperando partições de serviço do sistema                             

### <a name="cluster-operations"></a>Operações de cluster
* **ProvisionFabric**: MSI e/ou provisionamento de manifesto de cluster                             
* **UpgradeFabric**: Iniciando atualizações de cluster                             
* **UnprovisionFabric**: MSI e/ou desprovisionamento do manifesto do cluster                         
* **MoveNextFabricUpgradeDomain**: retomando atualizações de cluster com um domínio de atualização explícito                             
* **ReportFabricUpgradeHealth**: retomando atualizações de cluster com o andamento da atualização atual                             
* **StartInfrastructureTask**: Iniciando tarefas de infraestrutura                             
* **FinishInfrastructureTask**: concluindo tarefas de infraestrutura                             
* **InvokeInfrastructureCommand**: comandos de gerenciamento de tarefas de infraestrutura                              
* **ActivateNode**: ativando um nó                             
* **DeactivateNode**: desativando um nó                             
* **DeactivateNodesBatch**: desativando vários nós                             
* **RemoveNodeDeactivations**: revertendo a desativação em vários nós                             
* **GetNodeDeactivationStatus**: verificando o status da desativação                             
* **NodeStateRemoved**: estado do nó de relatório removido                             
* **ReportFault**: falha de relatório                             
* **FileContent**: transferência de arquivos do cliente do repositório de imagens (externo ao cluster)                             
* **FileDownload**: inicialização de download de arquivo do cliente de armazenamento de imagem (externo ao cluster)                             
* **InternalList**: operação de lista de arquivos do cliente de repositório de imagens (interno)                             
* **Excluir**: operação de exclusão do cliente do repositório de imagens                              
* **Upload**: operação de carregamento do cliente do repositório de imagens                             
* **NodeControl**: Iniciando, parando e reiniciando nós                             
* **MoveReplicaControl**: movendo réplicas de um nó para outro                             

### <a name="miscellaneous-operations"></a>Operações diversas
* **Ping**: pings do cliente                             
* **Consulta**: todas as consultas permitidas
* **NameExists**: nomenclatura de verificações de existência de URI                             

O tipo de controle de acesso do usuário é, por padrão, limitado às seguintes operações: 

* **EnumerateSubnames**: nomenclatura de enumeração de URI                             
* **Enumeraproperties**: nomenclatura de enumeração de propriedade                             
* **PropertyReadBatch**: nomenclatura de operações de leitura de propriedade                             
* **GetServiceDescription**: notificações de serviço de sondagem longa e descrições de serviço de leitura                             
* **ResolveService**: resolução de serviço baseada em reclamações                             
* **ResolveNameOwner**: resolvendo o proprietário do URI de nomenclatura                             
* **ResolvePartition**: Resolvendo serviços do sistema                             
* Serviços de **notificação**: notificações de serviço baseadas em evento                             
* **GetUpgradeStatus**: status de atualização do aplicativo de sondagem                             
* **GetFabricUpgradeStatus**: status de atualização do cluster de sondagem                             
* **InvokeInfrastructureQuery**: consultar tarefas de infraestrutura                             
* **Lista**: operação de lista de arquivos do cliente de repositório de imagens                             
* **ResetPartitionLoad**: redefinição de carga para uma unidade de failover                             
* **ToggleVerboseServicePlacementHealthReporting**: alternando relatórios de integridade de posicionamento de serviço detalhado                             

O controle de acesso de administrador também tem acesso às operações anteriores.

## <a name="changing-default-settings-for-client-roles"></a>Alterando as configurações padrão para funções de cliente
No arquivo de manifesto do cluster, você pode fornecer recursos de administrador para o cliente, se necessário. Você pode alterar os padrões acessando a opção **de configurações de malha** durante a criação do [cluster](service-fabric-cluster-creation-via-portal.md)e fornecendo as configurações anteriores nos campos **nome**, **administrador**, **usuário**e **valor** .

## <a name="next-steps"></a>Passos seguintes
[Segurança de Cluster Service Fabric](service-fabric-cluster-security.md)

[Criação de Cluster Service Fabric](service-fabric-cluster-creation-via-portal.md)

