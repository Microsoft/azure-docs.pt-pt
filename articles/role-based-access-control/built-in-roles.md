---
title: Funções incorporadas para recursos do Azure | Documentos da Microsoft
description: Descreve as funções incorporadas para controlo de acesso baseado em funções (RBAC) e recursos do Azure. Lista de ações, NotActions, DataActions e NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b92bc0a6c5d51ad26e069a363619edbdf0daa7c0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442877"
---
# <a name="built-in-roles-for-azure-resources"></a>Funções incorporadas para recursos do Azure

[Controlo de acesso baseado em funções (RBAC)](overview.md) tem várias funções incorporadas para recursos do Azure que pode atribuir aos utilizadores, grupos, os principais de serviço e identidades geridas. Atribuições de funções são a forma de controlar o acesso aos recursos do Azure. Se as funções incorporadas não atenderem às necessidades específicas da sua organização, pode criar a sua própria [funções personalizadas para recursos do Azure](custom-roles.md).

Este artigo lista as funções incorporadas para recursos do Azure, que estão sempre a evoluir. Para obter as funções mais recente, utilize [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [lista de definições de função de az](/cli/azure/role/definition#az-role-definition-list). Se estiver procurando por funções de administrador do Azure Active Directory, veja [permissões da função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Descrições de função incorporada

A tabela seguinte fornece uma breve descrição de cada função incorporada. Clique no nome de função para ver a lista de `Actions`, `NotActions`, `DataActions`, e `NotDataActions` para cada função. Para obter informações sobre o que significa que estas ações e como eles se aplicam para os planos de gestão e os dados, consulte [compreender as definições de funções para recursos do Azure](role-definitions.md).


| Função incorporada | Descrição |
| --- | --- |
| [Proprietário](#owner) | Permite-lhe gerir tudo, incluindo o acesso aos recursos. |
| [Contribuidor](#contributor) | Permite-lhe gerir tudo, exceto o acesso aos recursos. |
| [Leitor](#reader) | Permite-lhe ver tudo, mas não faça quaisquer alterações. |
| [AcrDelete](#acrdelete) | Acr delete |
| [AcrImageSigner](#acrimagesigner) | signatário de imagem ACR |
| [AcrPull](#acrpull) | Extração de ACR |
| [AcrPush](#acrpush) | push do ACR |
| [AcrQuarantineReader](#acrquarantinereader) | leitor de dados de quarentena do ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | Escritor de dados de quarentena do ACR |
| [Contribuinte de serviços de gestão de API](#api-management-service-contributor) | Pode gerir o serviço e as APIs |
| [Função de operador de serviço de gestão de API](#api-management-service-operator-role) | Pode gerir o serviço, mas não as APIs |
| [Função de leitor de serviço de gestão de API](#api-management-service-reader-role) | Acesso só de leitura para o serviço e APIs |
| [Contribuinte de componente do Application Insights](#application-insights-component-contributor) | Pode gerir os componentes do Application Insights |
| [Snapshot Debugger do Application Insights](#application-insights-snapshot-debugger) | Dá permissão ao utilizador para ver e transferir os instantâneos de depuração recolhidos com o Snapshot Debugger do Application Insights. Tenha em atenção que estas permissões não estão incluídas no [proprietário](#owner) ou [contribuinte](#contributor) funções. |
| [Operador de tarefas de automatização](#automation-job-operator) | Criar e gerir tarefas através de Runbooks de automatização. |
| [Operador de automatização](#automation-operator) | Operadores de automatização são capazes de iniciar, parar, suspender e retomar tarefas |
| [Operador de Runbook de automatização](#automation-runbook-operator) | Ler as propriedades de Runbook - para poder criar tarefas do runbook. |
| [Contribuinte de Avere](#avere-contributor) | Pode criar e gerir um cluster de vFXT Avere. |
| [Operador de Avere](#avere-operator) | Utilizado pelo cluster de vFXT Avere para gerir o cluster |
| [Proprietário de dados de Hubs de eventos do Azure (pré-visualização)](#azure-event-hubs-data-owner-preview) | Permite acesso total aos recursos dos Hubs de eventos do Azure. |
| [Receptor de dados de Hubs de eventos do Azure (pré-visualização)](#azure-event-hubs-data-receiver-preview) | Permite que tenha acesso a recursos de Event Hubs do Azure. |
| [Remetente de dados de Hubs de eventos do Azure (pré-visualização)](#azure-event-hubs-data-sender-preview) | Permite o acesso de envio para os recursos de Event Hubs do Azure. |
| [Função de administrador de Cluster do serviço Kubernetes do Azure](#azure-kubernetes-service-cluster-admin-role) | Ação de credencial de administrador de cluster de lista. |
| [Função de utilizador de Cluster do serviço Kubernetes do Azure](#azure-kubernetes-service-cluster-user-role) | Listar a ação de credenciais de utilizador do cluster. |
| [Leitor de dados de mapas do Azure (pré-visualização)](#azure-maps-data-reader-preview) | Concede acesso a leitura de mapear dados relacionados de uma conta do Azure maps. |
| [Proprietário de dados de barramento de serviço do Azure (pré-visualização)](#azure-service-bus-data-owner-preview) | Permite acesso total aos recursos do Azure Service Bus. |
| [Recetor de dados de barramento de serviço do Azure (pré-visualização)](#azure-service-bus-data-receiver-preview) | Permite a receber acesso aos recursos do Azure Service Bus. |
| [Remetente de dados de barramento de serviço do Azure (pré-visualização)](#azure-service-bus-data-sender-preview) | Permite para envio de acesso aos recursos do Azure Service Bus. |
| [Proprietário do registo do Azure Stack](#azure-stack-registration-owner) | Permite-lhe gerir os registos do Azure Stack. |
| [Contribuidor de cópia de segurança](#backup-contributor) | Permite-lhe gerir serviço de cópia de segurança, mas não é possível criar cofres e conceder acesso a outras pessoas |
| [Operador de cópia de segurança](#backup-operator) | Permite-lhe gerir serviços de cópia de segurança, exceto de remoção de cópia de segurança, a criação do cofre e a fornecer acesso a outras pessoas |
| [Leitor de cópia de segurança](#backup-reader) | Pode ver os serviços de cópia de segurança, mas não pode efetuar alterações |
| [Leitor de faturação](#billing-reader) | Permite o acesso de leitura aos dados de faturação |
| [Contribuinte do BizTalk](#biztalk-contributor) | Permite-lhe gerir os serviços do BizTalk, mas não aceder-lhes. |
| [Acesso de nó de membro de Blockchain (pré-visualização)](#blockchain-member-node-access-preview) | Permite acesso a nós do membro de Blockchain |
| [Contribuidor de ponto final CDN](#cdn-endpoint-contributor) | Pode gerir pontos finais da CDN, mas não é possível conceder acesso a outros utilizadores. |
| [Leitor do ponto final CDN](#cdn-endpoint-reader) | Pode ver pontos finais da CDN, mas não pode fazer alterações. |
| [Contribuidor de perfil de CDN](#cdn-profile-contributor) | Pode gerir perfis de CDN e os respetivos pontos finais, mas não é possível conceder acesso a outros utilizadores. |
| [Leitor de perfil CDN](#cdn-profile-reader) | Pode visualizar perfis de CDN e os respetivos pontos finais, mas não pode fazer alterações. |
| [Contribuidor de Rede Clássica](#classic-network-contributor) | Permite-lhe gerir redes virtuais, mas não aceder-lhes. |
| [Contribuinte de conta de armazenamento clássicas](#classic-storage-account-contributor) | Permite-lhe gerir contas de armazenamento clássicas, mas não aceder-lhes. |
| [Função do serviço de operador de chave de conta de armazenamento clássicas](#classic-storage-account-key-operator-service-role) | Operadores de chave de conta de armazenamento clássica têm permissão para listar e regenerar chaves em contas de armazenamento clássicas |
| [Contribuinte de Máquina Virtual clássica](#classic-virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais clássicas, mas não aceder-lhes e não a rede virtual ou conta de armazenamento, que eles estão conectados. |
| [Contribuinte de serviços cognitivos](#cognitive-services-contributor) | Permite-lhe criar, ler, atualizar, eliminar e gerir as chaves dos serviços cognitivos. |
| [Leitor de dados de serviços cognitivos (pré-visualização)](#cognitive-services-data-reader-preview) | Permite-lhe ler dados de serviços cognitivos. |
| [Utilizador dos serviços cognitivos](#cognitive-services-user) | Permite-lhe ler e lista as chaves dos serviços cognitivos. |
| [Função de leitor de conta do cosmos DB](#cosmos-db-account-reader-role) | Pode ler os dados da conta do Azure Cosmos DB. Ver [contribuinte de conta do DocumentDB](#documentdb-account-contributor) para a gestão de contas do Azure Cosmos DB. |
| [Operador de cosmos DB](#cosmos-db-operator) | Permite-lhe gerir contas do Azure Cosmos DB, mas não aceder a dados nos mesmos. Impede o acesso a chaves de conta e as cadeias de ligação. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Pode submeter o pedido de restauro para um banco de dados do Cosmos DB ou um contentor para uma conta |
| [Contribuinte de gestão de custos](#cost-management-contributor) | Pode ver os custos e gerir a configuração de custo (por exemplo, orçamentos, exportações) |
| [Leitor de gestão de custos](#cost-management-reader) | Pode ver os dados de custo e a configuração (por exemplo, orçamentos, exportações) |
| [Contribuinte de caixa de dados](#data-box-contributor) | Permite-lhe gerir tudo no serviço do Data Box, exceto que lhe dá acesso a outras pessoas. |
| [Leitor de dados de caixa](#data-box-reader) | Permite-lhe gerir o serviço do Data Box, exceto a ordem de criação ou edição de detalhes do pedido e que lhe dá acesso a outras pessoas. |
| [Contribuinte do Data Factory](#data-factory-contributor) | Criar e gerir fábricas de dados, bem como recursos subordinados dentro dos mesmos. |
| [Programador do Data Lake Analytics](#data-lake-analytics-developer) | Permite-lhe submeter, monitorizar e gerir as suas tarefas, mas não criar ou eliminar contas do Data Lake Analytics. |
| [Data Purger](#data-purger) | Pode remover os dados de análise |
| [Utilizador de DevTest Labs](#devtest-labs-user) | Permite-lhe ligar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. |
| [Contribuidor da zona DNS](#dns-zone-contributor) | Permite-lhe gerir zonas DNS e conjuntos de registos no DNS do Azure, mas não lhe permite controlar quem tem acesso aos mesmos. |
| [Contribuinte de conta do DocumentDB](#documentdb-account-contributor) | Pode gerir contas do Azure Cosmos DB. O Azure Cosmos DB anteriormente é conhecido como o DocumentDB. |
| [Contribuinte de EventSubscription EventGrid](#eventgrid-eventsubscription-contributor) | Permite-lhe gerir operações de subscrição de evento EventGrid. |
| [Leitor de EventSubscription EventGrid](#eventgrid-eventsubscription-reader) | Permite que leia EventGrid subscrições de eventos. |
| [Operador de Cluster do HDInsight](#hdinsight-cluster-operator) | Permite-lhe ler e modificar configurações de cluster do HDInsight. |
| [Contribuinte de serviços de domínio do HDInsight](#hdinsight-domain-services-contributor) | Pode ler, criar, modificar e eliminar serviços de domínio operações relacionadas necessários para o HDInsight Enterprise Security Package |
| [Contribuinte de conta de sistemas inteligentes](#intelligent-systems-account-contributor) | Permite-lhe gerir contas de sistemas inteligentes, mas não aceder-lhes. |
| [Contribuinte do Cofre de chaves](#key-vault-contributor) | Permite-lhe gerir cofres de chaves, mas não aceder-lhes. |
| [Criador do laboratório](#lab-creator) | Permite-lhe criar, gerir e eliminar os seus laboratórios geridos nas suas contas de laboratório do Azure. |
| [Contribuidor do log Analytics](#log-analytics-contributor) | Contribuidor do log Analytics pode ler todos os dados de monitorização e editar as definições de monitorização. Editar definições de monitorização inclui adicionar a extensão VM para VMs; ler as chaves de conta de armazenamento para poder configurar a recolha de registos do armazenamento do Azure; criar e configurar contas de automatização; Adicionar soluções; e configurar os diagnósticos do Azure em todos os recursos do Azure. |
| [Leitor do log Analytics](#log-analytics-reader) | Leitor do log Analytics pode ver e procurar todos os dados de monitorização e como e vista de definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure de monitorização. |
| [Contribuidor da aplicação lógica](#logic-app-contributor) | Permite-lhe gerir aplicações lógicas, mas não alterar o acesso aos mesmos. |
| [Operador de aplicação lógica](#logic-app-operator) | Permite-lhe ler, ativar e desativar aplicações lógicas, mas não editar ou atualizá-los. |
| [Função de operador de aplicação gerida](#managed-application-operator-role) | Permite-lhe ler e executar ações nos recursos de aplicação gerida |
| [Leitor de aplicativos gerenciados](#managed-applications-reader) | Permite-lhe ler os recursos de acesso JIT gerido de aplicação e a pedido. |
| [Contribuidor de identidade gerido](#managed-identity-contributor) | Criar, ler, atualizar e eliminar a identidade atribuída ao utilizador |
| [Operador de identidade gerido](#managed-identity-operator) | Ler e atribuir a identidade atribuída ao utilizador |
| [Contribuinte do grupo de gestão](#management-group-contributor) | Função de contribuinte do grupo de gestão |
| [Leitor do grupo de gestão](#management-group-reader) | Função de leitor de grupo de gestão |
| [Contribuidor de monitorização](#monitoring-contributor) | Pode ler todos os dados de monitorização e editar as definições de monitorização. Consulte também [começar com as funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitorização de métricas de publicador](#monitoring-metrics-publisher) | Permite a publicação de métricas em recursos do Azure |
| [Leitor de monitorização](#monitoring-reader) | Pode ler todos os dados de monitorização (métricas, registos, etc.). Consulte também [começar com as funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Contribuidor de Rede](#network-contributor) | Permite-lhe gerir redes, mas não aceder-lhes. |
| [Contribuinte de conta APM do novo Relic](#new-relic-apm-account-contributor) | Permite-lhe gerir contas de gestão de desempenho de aplicações do New Relic e aplicativos, mas não aceder-lhes. |
| [Leitor e acesso a dados](#reader-and-data-access) | Permite-lhe ver tudo, mas não lhe permitirá eliminar ou criar uma conta de armazenamento ou recurso contido. Também irá permitir acesso de leitura/gravação para todos os dados contidos numa conta de armazenamento através do acesso às chaves de conta de armazenamento. |
| [Contribuinte de Cache de redis](#redis-cache-contributor) | Permite-lhe gerir caches de Redis, mas não aceder-lhes. |
| [Contribuinte de política de recurso (pré-visualização)](#resource-policy-contributor-preview) | (Pré-visualização) Os utilizadores substituídos de EA, com direitos para criar/modificar a política de recursos, criar pedido de suporte e leem recursos/hierarquia. |
| [Contribuinte de coleções de tarefa do Scheduler](#scheduler-job-collections-contributor) | Permite-lhe gerir coleções de tarefas do Scheduler, mas não aceder-lhes. |
| [Contribuinte de serviços de pesquisa](#search-service-contributor) | Permite-lhe gerir os serviços de pesquisa, mas não aceder-lhes. |
| [Administrador de segurança](#security-admin) | No Centro de segurança apenas: Pode ver as políticas de segurança, visualizar os Estados de segurança, editar as políticas de segurança, ver alertas e recomendações, dispensar alertas e recomendações |
| [Gestor de segurança (Legado)](#security-manager-legacy) | Esta é uma função legada. Utilize em vez disso, o administrador de segurança |
| [Leitor de segurança](#security-reader) | No Centro de segurança apenas: Pode ver recomendações e alertas, políticas de segurança, visualizar os Estados de segurança, mas não pode fazer alterações de vista |
| [Contribuinte do site Recovery](#site-recovery-contributor) | Permite-lhe gerir o serviço de recuperação de sites, exceto a criação do cofre e atribuição de função |
| [Operador de recuperação de site](#site-recovery-operator) | Permite-lhe a ativação pós-falha e reativação pós-falha mas não efetuar outras operações de gestão do Site Recovery |
| [Leitor do site Recovery](#site-recovery-reader) | Permite-lhe ver estado do Site Recovery mas não efetuar outras operações de gestão |
| [Contribuinte de conta de âncoras espaciais](#spatial-anchors-account-contributor) | Permite gerir as âncoras geográficos na sua conta, mas não excluí-las |
| [Proprietário da conta de âncoras espaciais](#spatial-anchors-account-owner) | Permite-lhe gerir as âncoras geográficos na sua conta, incluindo excluí-los |
| [Leitor de conta de âncoras espaciais](#spatial-anchors-account-reader) | Permite-lhe localizar e ler as propriedades de âncoras geográficos na sua conta |
| [Contribuinte da BD SQL](#sql-db-contributor) | Permite-lhe gerir bases de dados SQL, mas não aceder-lhes. Além disso, não é possível gerir as respetivas políticas relacionadas com segurança ou os respetivos servidores SQL principais. |
| [Contribuinte de instância gerida de SQL](#sql-managed-instance-contributor) | Permite-lhe gerir instâncias de geridas de SQL e necessária configuração de rede, mas não é possível conceder acesso a outras pessoas. |
| [Gestor de segurança SQL](#sql-security-manager) | Permite-lhe gerir as políticas relacionadas com a segurança de servidores do SQL Server e bases de dados, mas não aceder-lhes. |
| [Contribuinte do SQL Server](#sql-server-contributor) | Permite gerir bases de dados e os servidores SQL, mas não acedê-los e não a segurança dos mesmos-relacionadas com as políticas. |
| [Contribuidor de Conta de Armazenamento](#storage-account-contributor) | Permite a gestão de contas de armazenamento. Isso não concede acesso a dados na conta de armazenamento. |
| [Função do serviço de operador de chave de conta de armazenamento](#storage-account-key-operator-service-role) | Permite que listar e regenerar chaves de acesso de conta de armazenamento. |
| [Contribuinte de dados de Blob de armazenamento](#storage-blob-data-contributor) | Ler, escrever e eliminar os contentores de armazenamento do Azure e blobs. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Proprietário de dados de Blob de armazenamento](#storage-blob-data-owner) | Fornece acesso total aos dados, incluindo a atribuição de controlo de acesso POSIX e contentores de BLOBs de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Leitor de dados de Blob de armazenamento](#storage-blob-data-reader) | Ler e listar blobs e contentores de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Contribuinte de dados de fila de armazenamento](#storage-queue-data-contributor) | Ler, escrever e eliminar filas e mensagens de filas de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Processador de mensagens de dados de fila de armazenamento](#storage-queue-data-message-processor) | Pré-visualização, obter e eliminar uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Remetente de mensagem de dados de fila de armazenamento](#storage-queue-data-message-sender) | Adicione mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Leitor de dados de fila de armazenamento](#storage-queue-data-reader) | Ler e listar e mensagens de filas de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Contribuidor de pedido de suporte](#support-request-contributor) | Permite-lhe criar e gerir pedidos de suporte |
| [Contribuidor do Gestor de tráfego](#traffic-manager-contributor) | Permite-lhe gerir perfis do Gestor de tráfego, mas não lhe permite controlar quem tem acesso aos mesmos. |
| [Administrador de Acesso de Utilizador](#user-access-administrator) | Permite-lhe gerir o acesso dos utilizadores aos recursos do Azure. |
| [Início de sessão de administrador de máquinas virtuais](#virtual-machine-administrator-login) | Ver máquinas de virtuais no portal e inicie sessão como administrador |
| [Contribuidor de Máquina Virtual](#virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais, mas não aceder-lhes e não a rede virtual ou conta de armazenamento, que eles estão conectados. |
| [Início de sessão de utilizador de máquina virtual](#virtual-machine-user-login) | Ver as máquinas virtuais no portal e inicie sessão como um utilizador normal. |
| [Contribuinte de plano Web](#web-plan-contributor) | Permite-lhe gerir planos web para Web sites, mas não aceder-lhes. |
| [Contribuinte de Web site](#website-contributor) | Permite-lhe gerir sites (não planos web), mas não aceder-lhes. |


## <a name="owner"></a>Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo, incluindo o acesso aos recursos. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Ações** |  |
> | * | Criar e gerir recursos de todos os tipos |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="contributor"></a>Contribuinte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo, exceto o acesso aos recursos. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Ações** |  |
> | * | Criar e gerir recursos de todos os tipos |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Eliminar funções e as atribuições de funções |
> | Microsoft.Authorization/*/Write | Crie funções e as atribuições de funções |
> | Microsoft.Authorization/elevateAccess/Action | Garante ao chamador acesso de administrador de acesso de utilizador no âmbito do inquilino |
> | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar quaisquer artefactos de esquema |
> | Microsoft.Blueprint/blueprintAssignments/delete | Eliminar quaisquer artefactos de esquema |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="reader"></a>Leitor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ver tudo, mas não faça quaisquer alterações. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Acr delete |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Elimine artefactos num registo de contentor. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | signatário de imagem ACR |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Push/Pull metadados de confiança de conteúdo para um registo de contentor. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Extração de ACR |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Solicitar ou obter imagens a partir de um registo de contentor. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | push do ACR |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Solicitar ou obter imagens a partir de um registo de contentor. |
> | Microsoft.ContainerRegistry/registries/push/write | Push ou escrever as imagens para um registo de contentor. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | leitor de dados de quarentena do ACR |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Solicitar ou obter imagens em quarentena do container registry |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Escritor de dados de quarentena do ACR |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Solicitar ou obter imagens em quarentena do container registry |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Escrita/modificar o estado de quarentena de imagens em quarentena |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="api-management-service-contributor"></a>Contribuinte de serviços de gestão de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir o serviço e as APIs |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/* | Criar e gerir o serviço de gestão de API |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="api-management-service-operator-role"></a>Função de operador de serviço de gestão de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir o serviço, mas não as APIs |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Instâncias do serviço de gestão de API de leitura |
> | Microsoft.ApiManagement/service/backup/action | Serviço de gestão de API de cópia de segurança no contentor especificado num utilizador forneceu a conta de armazenamento |
> | Microsoft.ApiManagement/service/delete | Eliminar a instância de serviço de gestão de API |
> | Microsoft.ApiManagement/service/managedeployments/action | Alterar o SKU/unidades, adicionar ou remover implementações regionais do serviço de gestão de API |
> | Microsoft.ApiManagement/service/read | Ler metadados para uma instância de serviço de gestão de API |
> | Microsoft.ApiManagement/service/restore/action | Restaurar o serviço de gestão de API do contêiner especificado um utilizador fornecido a conta de armazenamento |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carregar o certificado SSL para um serviço de gestão de API |
> | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um serviço de gestão de API |
> | Microsoft.ApiManagement/service/write | Criar uma nova instância do serviço de gestão de API |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas a utilizador |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="api-management-service-reader-role"></a>Função de leitor de serviço de gestão de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Acesso só de leitura para o serviço e APIs |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Instâncias do serviço de gestão de API de leitura |
> | Microsoft.ApiManagement/service/read | Ler metadados para uma instância de serviço de gestão de API |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas a utilizador |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="application-insights-component-contributor"></a>Contribuinte de componente do Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir os componentes do Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Insights/components/* | Criar e gerir os componentes de informações |
> | Microsoft.Insights/webtests/* | Criar e gerir os testes web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="application-insights-snapshot-debugger"></a>Snapshot Debugger do Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Dá permissão ao utilizador para ver e transferir os instantâneos de depuração recolhidos com o Snapshot Debugger do Application Insights. Tenha em atenção que estas permissões não estão incluídas no [proprietário](#owner) ou [contribuinte](#contributor) funções. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="automation-job-operator"></a>Operador de tarefas de automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar e gerir tarefas através de Runbooks de automatização. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê recursos de trabalho de Runbook híbrida |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtém uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Para uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de tarefas de automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Cria uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém o resultado de uma tarefa |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="automation-operator"></a>Operador de Automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Operadores de automatização são capazes de iniciar, parar, suspender e retomar tarefas |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê recursos de trabalho de Runbook híbrida |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtém uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Para uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de tarefas de automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Cria uma tarefa da automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtém um plano de trabalho de automatização do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Cria um plano de trabalho de automatização do Azure |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém a área de trabalho ligada à conta de automatização |
> | Microsoft.Automation/automationAccounts/read | Obtém uma conta de automatização do Azure |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtém um runbook da automatização do Azure |
> | Microsoft.Automation/automationAccounts/schedules/read | Obtém um recurso de agenda da automatização do Azure |
> | Microsoft.Automation/automationAccounts/schedules/write | Cria ou atualiza um recurso de agenda da automatização do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém o resultado de uma tarefa |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="automation-runbook-operator"></a>Operador de Runbook de automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler as propriedades de Runbook - para poder criar tarefas do runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtém um runbook da automatização do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="avere-contributor"></a>Contribuinte de Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode criar e gerir um cluster de vFXT Avere. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obtém uma definição de sub-rede de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não. o alerta. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Associa o recurso, como a conta de armazenamento ou base de dados SQL a uma sub-rede. Não. o alerta. |
> | Microsoft.Network/networkSecurityGroups/join/action | Associa um grupo de segurança de rede. Não. o alerta. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Obtém os recursos para o grupo de recursos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever um blob |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="avere-operator"></a>Operador de Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Utilizado pelo cluster de vFXT Avere para gerir o cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Ações** |  |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obtém uma definição de sub-rede de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não. o alerta. |
> | Microsoft.Network/networkSecurityGroups/join/action | Associa um grupo de segurança de rede. Não. o alerta. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Devolve o resultado da eliminação de um contentor |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve a lista de contentores |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Devolve o resultado do contentor de BLOBs colocado |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever um blob |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-event-hubs-data-owner-preview"></a>Proprietário de dados de Hubs de eventos do Azure (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso total aos recursos dos Hubs de eventos do Azure. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Ações** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-event-hubs-data-receiver-preview"></a>Receptor de dados de Hubs de eventos do Azure (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que tenha acesso a recursos de Event Hubs do Azure. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-event-hubs-data-sender-preview"></a>Remetente de dados de Hubs de eventos do Azure (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de envio para os recursos de Event Hubs do Azure. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Função de administrador de Cluster do serviço Kubernetes do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ação de credencial de administrador de cluster de lista. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial de clusterAdmin de um cluster gerido |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Função de utilizador de Cluster do serviço Kubernetes do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Listar a ação de credenciais de utilizador do cluster. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial de clusterUser de um cluster gerido |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-maps-data-reader-preview"></a>Leitor de dados de mapas do Azure (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Concede acesso a leitura de mapear dados relacionados de uma conta do Azure maps. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados para uma conta de mapas. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-service-bus-data-owner-preview"></a>Proprietário de dados de barramento de serviço do Azure (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso total aos recursos do Azure Service Bus. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Ações** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-service-bus-data-receiver-preview"></a>Recetor de dados de barramento de serviço do Azure (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a receber acesso aos recursos do Azure Service Bus. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Ações** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-service-bus-data-sender-preview"></a>Remetente de dados de barramento de serviço do Azure (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite para envio de acesso aos recursos do Azure Service Bus. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Ações** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-stack-registration-owner"></a>Proprietário do registo do Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os registos do Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Ações** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Obtém estendido detalhes para um produto do Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/products/read | Obtém as propriedades de um produto do Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Obtém as propriedades de um registo do Azure Stack |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="backup-contributor"></a>Contribuidor de cópia de segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir cópia de segurança de serviço, mas não é possível criar cofres e conceder acesso a outras pessoas |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gerir os resultados da operação na gestão de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Criar e gerir contentores de cópia de segurança dentro de recursos de infraestrutura de cópia de segurança do cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contentores |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerir tarefas de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar tarefas |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Criar e gerir os metadados relacionados com a gestão de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerir resultados de operações de gestão de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Criar e gerir políticas de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser uma cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Criar e gerir itens de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Criar e gerir contentores que contém itens de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumos de utilização dos itens e servidores protegidos para dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Criar e gerir certificados relacionados com a cópia de segurança no cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerir as informações expandidas relacionadas para o Cofre |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter alertas para o Cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
> | Microsoft.RecoveryServices/Vaults/usages/* | Criar e gerir a utilização do cofre dos serviços de recuperação |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar a operação no Item protegido |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve a operação de cópia de segurança Cofre de serviços de estado de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contentores protegíveis |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o estado de cópia de segurança para cofres dos serviços de recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar funcionalidades |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de operações para um fornecedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o estado da operação a uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista de todos os objetivos de proteção de cópia de segurança |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="backup-operator"></a>Operador de cópia de segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir serviços de cópia de segurança, exceto de remoção de cópia de segurança, a criação do cofre e a fornecer acesso a outras pessoas |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devolve o estado da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da Operação efetuada no Contentor de Proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executa a Cópia de Segurança do Item Protegido. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o Resultado da Operação Efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve os detalhes de objeto do Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprovisionar recuperação de Item instantâneo para Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter Pontos de Recuperação de Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar Pontos de Recuperação de Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar recuperação de Item instantâneo para Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um Item de cópia de segurança protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devolve todos os contentores registados |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contentores |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerir tarefas de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar tarefas |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerir resultados de operações de gestão de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter Resultados da Operação de Política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser uma cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devolve todos os contentores pertencentes à subscrição |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumos de utilização dos itens e servidores protegidos para dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação atualizar certificado do recurso atualiza o certificado de credencial do recurso/cofre. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter alertas para o Cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser utilizada obter o estado da operação e o resultado da operação submetida assincronamente |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A obter contentores pode ser utilizada a operação obter os contentores registados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação registar contentor de serviços pode ser utilizada para registar um contentor com o serviço de recuperação. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar a operação no Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve a operação de cópia de segurança Cofre de serviços de estado de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o estado da operação da política. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Cria um contentor registado |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Execute a consulta para cargas de trabalho dentro de um contêiner |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obtenha uma intenção de proteção de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contentores protegíveis |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens num contentor |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o estado de cópia de segurança para cofres dos serviços de recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar funcionalidades |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de operações para um fornecedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o estado da operação a uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista de todos os objetivos de proteção de cópia de segurança |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="backup-reader"></a>Leitor de cópia de segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os serviços de cópia de segurança, mas não pode efetuar alterações |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devolve o estado da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da Operação efetuada no Contentor de Proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o Resultado da Operação Efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve os detalhes de objeto do Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter Pontos de Recuperação de Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devolve todos os contentores registados |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devolve o Resultado da Operação de Tarefa. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Devolve todos os objetos de trabalho |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar tarefas |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Devolve o Resultado da Operação da Cópia de Segurança do Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter Resultados da Operação de Política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devolve todos os contentores pertencentes à subscrição |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumos de utilização dos itens e servidores protegidos para dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter alertas para o Cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser utilizada obter o estado da operação e o resultado da operação submetida assincronamente |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A obter contentores pode ser utilizada a operação obter os contentores registados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Devolve a configuração de armazenamento para a recuperação Cofre de serviços. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Devolve a configuração para a recuperação Cofre de serviços. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve a operação de cópia de segurança Cofre de serviços de estado de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o estado da operação da política. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obtenha uma intenção de proteção de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens num contentor |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o estado de cópia de segurança para cofres dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de operações para um fornecedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o estado da operação a uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista de todos os objetivos de proteção de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="billing-reader"></a>Leitor de Faturação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de leitura aos dados de faturação |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Billing/*/read | Ler as informações de faturação |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Lista os grupos de gestão para o usuário autenticado. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="biztalk-contributor"></a>Contribuinte do BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os serviços do BizTalk, mas não aceder-lhes. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerir os serviços BizTalk |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="blockchain-member-node-access-preview"></a>Acesso de nó de membro de Blockchain (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso a nós do membro de Blockchain |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Ações** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista de nós de transação de membro de Blockchain existente. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Liga-se a um nó de transação de membro de Blockchain. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-endpoint-contributor"></a>Contribuidor de ponto final CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir pontos finais da CDN, mas não é possível conceder acesso a outros utilizadores. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-endpoint-reader"></a>Leitor do ponto final CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver pontos finais da CDN, mas não pode fazer alterações. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-profile-contributor"></a>Contribuidor de perfil de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir perfis de CDN e os respetivos pontos finais, mas não é possível conceder acesso a outros utilizadores. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-profile-reader"></a>Leitor de perfil CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode visualizar perfis de CDN e os respetivos pontos finais, mas não pode fazer alterações. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-network-contributor"></a>Contribuinte de rede clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir redes virtuais, mas não aceder-lhes. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.ClassicNetwork/* | Criar e gerir redes clássicas |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-storage-account-contributor"></a>Contribuinte de conta de armazenamento clássicas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas de armazenamento clássicas, mas não aceder-lhes. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.ClassicStorage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Função do serviço de operador de chave de conta de armazenamento clássicas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Operadores de chave de conta de armazenamento clássica têm permissão para listar e regenerar chaves em contas de armazenamento clássicas |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Ações** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-virtual-machine-contributor"></a>Contribuinte de Máquina Virtual clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir máquinas virtuais clássicas, mas não aceder-lhes e não a rede virtual ou conta de armazenamento, que eles estão conectados. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.ClassicCompute/domainNames/* | Criar e gerir nomes de domínio de computação clássica |
> | Microsoft.ClassicCompute/virtualMachines/* | Criar e gerir máquinas virtuais |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Ligar um Ip reservado |
> | Microsoft.ClassicNetwork/reservedIps/read | Obtém os Ips reservados |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Associa a rede virtual. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Obtenha a rede virtual. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Devolve o disco de conta de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Devolve a imagem de conta de armazenamento. (Preterido. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devolva a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cognitive-services-contributor"></a>Contribuinte de serviços cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar, ler, atualizar, eliminar e gerir as chaves dos serviços cognitivos. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Obtém as funcionalidades de uma subscrição. |
> | Microsoft.Features/providers/features/read | Obtém a funcionalidade de uma subscrição no fornecedor de recursos específico. |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Ler definições de registo |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cognitive-services-data-reader-preview"></a>Leitor de dados de serviços cognitivos (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler dados de serviços cognitivos. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cognitive-services-user"></a>Utilizador dos serviços cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e lista as chaves dos serviços cognitivos. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Ações** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Listar chaves |
> | Microsoft.Insights/alertRules/read | Ler um alerta de métrica clássico |
> | Microsoft.Insights/diagnosticSettings/read | Ler uma definição de diagnóstico de recursos |
> | Microsoft.Insights/logDefinitions/read | Ler definições de registo |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cosmos-db-account-reader-role"></a>Função de leitor de conta do cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler os dados da conta do Azure Cosmos DB. Ver [contribuinte de conta do DocumentDB](#documentdb-account-contributor) para a gestão de contas do Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções, pode ler permissões concedidas a cada utilizador |
> | Microsoft.DocumentDB/*/read | Ler qualquer coleção |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lê a base de dados de chaves da conta só de leitura. |
> | Microsoft.Insights/MetricDefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/Metrics/read | Ler métricas |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cosmos-db-operator"></a>Operador de cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas do Azure Cosmos DB, mas não aceder a dados nos mesmos. Impede o acesso a chaves de conta e as cadeias de ligação. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Ações** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode submeter o pedido de restauro para um banco de dados do Cosmos DB ou um contentor para uma conta |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Ações** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Submeter um pedido para configurar a cópia de segurança |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Submeter um pedido de restauro |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cost-management-contributor"></a>Contribuinte de gestão de custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os custos e gerir a configuração de custo (por exemplo, orçamentos, exportações) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Ações** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Apresenta uma lista de períodos de faturação disponíveis |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Recomendações de leituras |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cost-management-reader"></a>Leitor de gestão de custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os dados de custo e a configuração (por exemplo, orçamentos, exportações) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Ações** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Apresenta uma lista de períodos de faturação disponíveis |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Recomendações de leituras |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-box-contributor"></a>Contribuinte de caixa de dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo no serviço do Data Box, exceto que lhe dá acesso a outras pessoas. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-box-reader"></a>Leitor de dados de caixa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o serviço do Data Box, exceto a ordem de criação ou edição de detalhes do pedido e que lhe dá acesso a outras pessoas. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Lista as credenciais não encriptadas relacionadas à encomenda. |
> | Microsoft.Databox/locations/availableSkus/action | Este método devolve a lista de skus disponíveis. |
> | Microsoft.Databox/locations/validateAddress/action | Valida o endereço de envio e fornece endereços alternativos, caso existam. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-factory-contributor"></a>Contribuinte do Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar e gerir fábricas de dados, bem como recursos subordinados dentro dos mesmos. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.DataFactory/dataFactories/* | Criar e gerir fábricas de dados e recursos subordinados dentro dos mesmos. |
> | Microsoft.DataFactory/factories/* | Criar e gerir fábricas de dados e recursos subordinados dentro dos mesmos. |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-lake-analytics-developer"></a>Programador do Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe submeter, monitorizar e gerir as suas tarefas, mas não criar ou eliminar contas do Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Elimine uma conta no DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar as tarefas submetidas por outros utilizadores. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Criar ou atualizar uma conta no DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta ligada do DataLakeStore de uma conta no DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desassocie a uma conta de DataLakeStore de uma conta no DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento ligada de uma conta no DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Desassocie a uma conta de armazenamento de uma conta no DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Elimine uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Elimine uma política de computação. |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-purger"></a>Purger de dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode remover os dados de análise |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Ações** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Remover os dados do Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Eliminar dados especificados a partir da área de trabalho |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="devtest-labs-user"></a>Utilizador de DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ligar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Microsoft.Compute/virtualMachines/*/read | Ler as propriedades de uma máquina virtual (tamanhos de VM, o estado de runtime, extensões de VM, etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Desliga a máquina virtual e liberta os recursos de computação |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Microsoft.DevTestLab/*/read | Ler as propriedades de um laboratório |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Declarar uma máquina de virtual reclamáveis aleatória no laboratório. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Crie máquinas virtuais num laboratório. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Certifique-se de que o utilizador atual tem um perfil válido no laboratório. |
> | Microsoft.DevTestLab/labs/formulas/delete | Elimine as fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/read | Leia fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/write | Adiciona ou modifica as fórmulas. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avalia a política de laboratório. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Obter a propriedade de uma máquina virtual existente |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Lista as agendas de iniciar/parar aplicável, se aplicável. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do ficheiro RDP para a máquina virtual |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Associa um conjunto de endereços de back-end de Balanceador de carga. Não. o alerta. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Associa uma regra de nat de entrada do Balanceador de carga. Não. o alerta. |
> | Microsoft.Network/networkInterfaces/*/read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga que a interface de rede é uma parte do) |
> | Microsoft.Network/networkInterfaces/join/action | Associa uma Máquina Virtual a uma interface de rede. Não. o alerta. |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Ler as propriedades de um endereço IP público |
> | Microsoft.Network/publicIPAddresses/join/action | Associa um endereço ip público. Não. o alerta. |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço ip público. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não. o alerta. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/deployments/read | Obtém ou lista implementações. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso de conta de armazenamento especificada. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Apresenta uma lista de tamanhos disponíveis, que a máquina virtual pode ser atualizada para |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="dns-zone-contributor"></a>Contribuidor da zona DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir zonas DNS e conjuntos de registos no DNS do Azure, mas não lhe permite controlar quem tem acesso aos mesmos. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Network/dnsZones/* | Criar e gerir zonas e registos DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="documentdb-account-contributor"></a>Contribuinte de conta do DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir contas do Azure Cosmos DB. O Azure Cosmos DB anteriormente é conhecido como o DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.DocumentDb/databaseAccounts/* | Criar e gerir contas do Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Contribuinte de EventSubscription EventGrid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir operações de subscrição de evento EventGrid. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Lista de subscrições de eventos global por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Subscrições de eventos regionais de lista |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Subscrições de eventos regionais de lista por topictype |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Leitor de EventSubscription EventGrid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que leia EventGrid subscrições de eventos. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Lista de subscrições de eventos global por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Subscrições de eventos regionais de lista |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Subscrições de eventos regionais de lista por topictype |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="hdinsight-cluster-operator"></a>Operador de Cluster do HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e modificar configurações de cluster do HDInsight. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Ações** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obter as definições do gateway para o Cluster do HDInsight |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Atualizar as definições do gateway para o Cluster do HDInsight |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="hdinsight-domain-services-contributor"></a>Contribuinte de serviços de domínio do HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler, criar, modificar e eliminar serviços de domínio operações relacionadas necessários para o HDInsight Enterprise Security Package |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Ações** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="intelligent-systems-account-contributor"></a>Contribuinte de conta de sistemas inteligentes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas de sistemas inteligentes, mas não aceder-lhes. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.IntelligentSystems/accounts/* | Criar e gerir contas de sistemas inteligentes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="key-vault-contributor"></a>Contribuinte do Cofre de chaves
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir cofres de chaves, mas não aceder-lhes. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Remover um cofre de chaves eliminado forma recuperável |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="lab-creator"></a>Criador do laboratório
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar, gerir e eliminar os seus laboratórios geridos nas suas contas de laboratório do Azure. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crie um laboratório numa conta do laboratório. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Obtenha informações de disponibilidade regional para cada categoria de tamanho configurada com uma conta de laboratório |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Contribuidor do log Analytics pode ler todos os dados de monitorização e editar as definições de monitorização. Editar definições de monitorização inclui adicionar a extensão VM para VMs; ler as chaves de conta de armazenamento para poder configurar a recolha de registos do armazenamento do Azure; criar e configurar contas de automatização; Adicionar soluções; e configurar os diagnósticos do Azure em todos os recursos do Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso de conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="log-analytics-reader"></a>Leitor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leitor do log Analytics pode ver e procurar todos os dados de monitorização e como e vista de definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure de monitorização. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Usando o novo mecanismo de pesquisa. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="logic-app-contributor"></a>Contribuidor da aplicação lógica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir aplicações lógicas, mas não alterar o acesso aos mesmos. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devolva a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de Atividades através do portal. Lista as categorias de registo no registo de atividades. |
> | Microsoft.Insights/metricDefinitions/* | Ler definições de métrica (lista de tipos de métricas disponíveis para um recurso). |
> | Microsoft.Logic/* | Gere os recursos de aplicações lógicas. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso de conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Web/connectionGateways/* | Criar e gere um Gateway de ligação. |
> | Microsoft.Web/connections/* | Criar e gere uma ligação. |
> | Microsoft.Web/customApis/* | Cria e gere uma API personalizada. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades num plano do serviço de aplicações |
> | Microsoft.Web/sites/functions/listSecrets/action | Funções de aplicações de Web de segredos de lista. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="logic-app-operator"></a>Operador de aplicação lógica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler, ativar e desativar aplicações lógicas, mas não editar ou atualizá-los. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/*/read | Regras de alerta de informações de leitura |
> | Microsoft.Insights/diagnosticSettings/*/read | Obtém as definições de diagnóstico para aplicações lógicas |
> | Microsoft.Insights/metricDefinitions/*/read | Obtém as métricas disponíveis para o Logic Apps. |
> | Microsoft.Logic/*/read | Lê recursos de aplicações lógicas. |
> | Microsoft.Logic/workflows/disable/action | Desativa o fluxo de trabalho. |
> | Microsoft.Logic/workflows/enable/action | Permite que o fluxo de trabalho. |
> | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Web/connectionGateways/*/read | Leia os Gateways de ligação. |
> | Microsoft.Web/connections/*/read | Ligações de leitura. |
> | Microsoft.Web/customApis/*/read | Leia a API personalizada. |
> | Microsoft.Web/serverFarms/read | Obter as propriedades num plano do serviço de aplicações |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-application-operator-role"></a>Função de operador de aplicação gerida
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e executar ações nos recursos de aplicação gerida |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Solutions/applications/read | Obtém uma lista de aplicativos. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-applications-reader"></a>Leitor de aplicativos gerenciados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler os recursos de acesso JIT gerido de aplicação e a pedido. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-identity-contributor"></a>Contribuidor de identidade gerido
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar, ler, atualizar e eliminar a identidade atribuída ao utilizador |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtém um identidade atribuída ao utilizador existente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Cria um nova identidade atribuída ao utilizador ou atualiza as marcas associadas com um identidade atribuída ao utilizador existente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Elimina um identidade atribuída ao utilizador existente |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-identity-operator"></a>Operador de identidade gerido
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler e atribuir a identidade atribuída ao utilizador |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="management-group-contributor"></a>Contribuinte do grupo de gestão
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Função de contribuinte do grupo de gestão |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/delete | Elimine grupo de gestão. |
> | Microsoft.Management/managementGroups/read | Lista os grupos de gestão para o usuário autenticado. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Associa anular a subscrição do grupo de gestão. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associa existente subscrição com o grupo de gestão. |
> | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gestão. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="management-group-reader"></a>Leitor do grupo de gestão
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Função de leitor de grupo de gestão |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/read | Lista os grupos de gestão para o usuário autenticado. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="monitoring-contributor"></a>Contribuidor de monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitorização e editar as definições de monitorização. Consulte também [começar com as funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Regras de alerta de leitura/escrita/eliminar. |
> | Microsoft.Insights/components/* | Componentes do Application Insights de leitura/escrita/eliminar. |
> | Microsoft.Insights/DiagnosticSettings/* | Definições de diagnóstico de leitura/escrita/eliminar. |
> | Microsoft.Insights/eventtypes/* | Lista de eventos de registo de Atividades (eventos de gestão) numa subscrição. Esta permissão é aplicável ao portal e programático acesso ao registo de atividades. |
> | Microsoft.Insights/LogDefinitions/* | Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de Atividades através do portal. Lista as categorias de registo no registo de atividades. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Ler definições de métrica (lista de tipos de métricas disponíveis para um recurso). |
> | Microsoft.Insights/Metrics/* | Ler métricas para um recurso. |
> | Microsoft.Insights/Register/Action | Registar o fornecedor de Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Testes web do Application Insights de leitura/escrita/eliminar. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Pacotes de soluções de análise de registo de leitura/escrita/eliminar. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Análise de registo de leitura/escrita/eliminar procuras guardadas. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Configurações de informações de armazenamento de análise de registo de leitura/escrita/eliminar. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="monitoring-metrics-publisher"></a>Monitorização de métricas de publicador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a publicação de métricas em recursos do Azure |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Ações** |  |
> | Microsoft.Insights/Register/Action | Registar o fornecedor de Microsoft Insights |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Escrever métricas |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="monitoring-reader"></a>Leitor de monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitorização (métricas, registos, etc.). Consulte também [começar com as funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="network-contributor"></a>Contribuinte de rede
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir redes, mas não aceder-lhes. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Network/* | Criar e gerir redes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="new-relic-apm-account-contributor"></a>Contribuinte de conta APM do novo Relic
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas de gestão de desempenho de aplicações do New Relic e aplicativos, mas não aceder-lhes. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="reader-and-data-access"></a>Leitor e acesso a dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ver tudo, mas não lhe permitirá eliminar ou criar uma conta de armazenamento ou recurso contido. Também irá permitir acesso de leitura/gravação para todos os dados contidos numa conta de armazenamento através do acesso às chaves de conta de armazenamento. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso de conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Devolve o token de SAS de conta para a conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="redis-cache-contributor"></a>Contribuinte de Cache de redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir caches de Redis, mas não aceder-lhes. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.Cache/redis/* | Criar e gerir as caches de Redis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="resource-policy-contributor-preview"></a>Contribuinte de política de recurso (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | (Pré-visualização) Os utilizadores substituídos de EA, com direitos para criar/modificar a política de recursos, criar pedido de suporte e leem recursos/hierarquia. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Authorization/policyassignments/* | Criar e gerir atribuições de política |
> | Microsoft.Authorization/policydefinitions/* | Criar e gerir definições de política |
> | Microsoft.Authorization/policysetdefinitions/* | Criar e gerir conjuntos de políticas |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="scheduler-job-collections-contributor"></a>Contribuinte de coleções de tarefa do Scheduler
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir coleções de tarefas do Scheduler, mas não aceder-lhes. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Scheduler/jobcollections/* | Criar e gerir coleções de tarefas |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="search-service-contributor"></a>Contribuinte de serviços de pesquisa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os serviços de pesquisa, mas não aceder-lhes. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Search/searchServices/* | Criar e gerir os serviços de pesquisa |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="security-admin"></a>Administrador de segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | No Centro de segurança apenas: Pode ver as políticas de segurança, visualizar os Estados de segurança, editar as políticas de segurança, ver alertas e recomendações, dispensar alertas e recomendações |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerir atribuições de política |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerir definições de política |
> | Microsoft.Authorization/policySetDefinitions/* | Criar e gerir conjuntos de políticas |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Management/managementGroups/read | Lista os grupos de gestão para o usuário autenticado. |
> | Microsoft.operationalInsights/workspaces/*/read | Ver dados do log analytics |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="security-manager-legacy"></a>Gestor de segurança (Legado)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Esta é uma função legada. Utilize em vez disso, o administrador de segurança |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.ClassicCompute/*/read | Ler informações de configuração de máquinas virtuais clássicas |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Escrita da configuração para máquinas virtuais clássicas |
> | Microsoft.ClassicNetwork/*/read | Ler informações de configuração sobre rede clássica |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Security/* | Criar e gerir os componentes de segurança e políticas |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="security-reader"></a>Leitor de segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | No Centro de segurança apenas: Pode ver recomendações e alertas, políticas de segurança, visualizar os Estados de segurança, mas não pode fazer alterações de vista |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.operationalInsights/workspaces/*/read | Ver dados do log analytics |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Security/*/read | Componentes de segurança de leitura e de políticas |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Management/managementGroups/read | Lista os grupos de gestão para o usuário autenticado. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="site-recovery-contributor"></a>Contribuinte do site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o serviço de recuperação de sites, exceto a criação do cofre e atribuição de função |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação atualizar certificado do recurso atualiza o certificado de credencial do recurso/cofre. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerir as informações expandidas relacionadas para o Cofre |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Criar ou atualizar as definições de alerta de replicação |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Criar e gerir recursos de infraestrutura de replicação |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerir tarefas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Criar e gerir políticas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Criar e gerir planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Criar e gerir a configuração de armazenamento do cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de Token de cofre pode ser utilizada para obter o Token de cofre para operações de back-end ao nível do cofre. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Alertas de leitura para o Cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="site-recovery-operator"></a>Operador de recuperação de site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe a ativação pós-falha e reativação pós-falha mas não efetuar outras operações de gestão do Site Recovery |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser utilizada obter o estado da operação e o resultado da operação submetida assincronamente |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A obter contentores pode ser utilizada a operação obter os contentores registados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler quaisquer definições de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica a Consistência dos Recursos de infraestrutura |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer recursos de infraestrutura |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar Gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar o certificado para recursos de infraestrutura |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contentores de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler todos os itens susceptíveis de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar o ponto de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Consolidação de ativação pós-falha |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Ativação pós-falha planeada |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler todos os itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Voltar a proteger o Item protegido |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Ativação pós-falha de teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza da ativação pós-falha de teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Ativação pós-falha |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualização do serviço de mobilidade |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos do contentor de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler quaisquer fornecedores de serviços de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar fornecedor |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler qualquer classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler qualquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerir tarefas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler todas as políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de consolidação de ativação pós-falha |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de ativação pós-falha planeada |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Voltar a proteger o plano de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plano de recuperação de ativação pós-falha de teste |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plano de recuperação de limpeza da ativação pós-falha de teste |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de ativação pós-falha |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Alertas de leitura para o Cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de Token de cofre pode ser utilizada para obter o Token de cofre para operações de back-end ao nível do cofre. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="site-recovery-reader"></a>Leitor do site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ver estado do Site Recovery mas não efetuar outras operações de gestão |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter alertas para o Cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser utilizada obter o estado da operação e o resultado da operação submetida assincronamente |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A obter contentores pode ser utilizada a operação obter os contentores registados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler quaisquer definições de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer recursos de infraestrutura |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contentores de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler todos os itens susceptíveis de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler todos os itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos do contentor de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler quaisquer fornecedores de serviços de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler qualquer classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler qualquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todas as tarefas |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler todas as políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de Token de cofre pode ser utilizada para obter o Token de cofre para operações de back-end ao nível do cofre. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="spatial-anchors-account-contributor"></a>Contribuinte de conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite gerir as âncoras geográficos na sua conta, mas não excluí-las |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Detetar próximas âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="spatial-anchors-account-owner"></a>Proprietário da conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as âncoras geográficos na sua conta, incluindo excluí-los |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Eliminar as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Detetar próximas âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="spatial-anchors-account-reader"></a>Leitor de conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe localizar e ler as propriedades de âncoras geográficos na sua conta |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Detetar próximas âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="sql-db-contributor"></a>Contribuinte da BD SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir bases de dados SQL, mas não aceder-lhes. Além disso, não é possível gerir as respetivas políticas relacionadas com segurança ou os respetivos servidores SQL principais. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Criar e gerir bases de dados SQL |
> | Microsoft.Sql/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar as políticas de auditoria |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar definições de auditoria |
> | Microsoft.Sql/servers/databases/auditRecords/read | Obter os registos de auditoria de Blobs do banco de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar as políticas de ligação |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar as políticas de máscara de dados |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar as políticas de alerta de segurança |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar as métricas de segurança |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="sql-managed-instance-contributor"></a>Contribuinte de instância gerida de SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir instâncias de geridas de SQL e necessária configuração de rede, mas não é possível conceder acesso a outras pessoas. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Ações** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="sql-security-manager"></a>Gestor de segurança SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as políticas relacionadas com a segurança de servidores do SQL Server e bases de dados, mas não aceder-lhes. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização da Microsoft de leitura |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Associa o recurso, como a conta de armazenamento ou base de dados SQL a uma sub-rede. Não. o alerta. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Criar e gerir políticas de auditorias do SQL server |
> | Microsoft.Sql/servers/auditingSettings/* | Criar e gerir a definição de auditoria do SQL server |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Obter os detalhes do blob de servidor expandida configurada num determinado servidor de política de auditoria |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerir políticas de auditoria de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerir definições de auditorias de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Registos de auditoria de leitura |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerir políticas de ligação de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerir políticas de máscara de dados do SQL server da base de dados |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Obter os detalhes da política de auditoria de blob expandido configurados numa determinada base de dados |
> | Microsoft.Sql/servers/databases/read | Devolve a lista de bases de dados ou obtém as propriedades da base de dados especificado. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Obter um esquema de banco de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obtenha uma coluna de base de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Obter uma tabela de base de dados. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerir políticas alerta de segurança da base de dados de servidor SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerir a métrica de segurança de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerir políticas de alerta de segurança do SQL server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="sql-server-contributor"></a>Contribuinte do SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite gerir bases de dados e os servidores SQL, mas não acedê-los e não a segurança dos mesmos-relacionadas com as políticas. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Criar e gerir servidores SQL |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Editar as políticas de auditoria do SQL server |
> | Microsoft.Sql/servers/auditingSettings/* | Editar definições de auditorias do SQL server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar as políticas de auditoria de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar definições de auditorias de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Registos de auditoria de leitura |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar as políticas de ligação de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar as políticas de máscara de dados do SQL server da base de dados |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas alerta de segurança da base de dados de servidor SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métrica de segurança de base de dados do SQL server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Editar as políticas de alerta de segurança do SQL server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-account-contributor"></a>Contribuinte de conta de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a gestão de contas de armazenamento. Fornece acesso à chave de conta, que pode ser utilizado para aceder a dados por meio de autorização de chave partilhada. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler todos os de autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Insights/diagnosticSettings/* | Gerir definições de diagnóstico |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Associa o recurso, como a conta de armazenamento ou base de dados SQL a uma sub-rede. Não. o alerta. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-account-key-operator-service-role"></a>Função do serviço de operador de chave de conta de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que listar e regenerar chaves de acesso de conta de armazenamento. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devolva as chaves de acesso para a conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenere as chaves de acesso para a conta de armazenamento especificada. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-blob-data-contributor"></a>Contribuinte de dados de Blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler, escrever e eliminar os contentores de armazenamento do Azure e blobs. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Elimine um contentor. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve um contentor ou uma lista de contentores. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modificar metadados ou as propriedades de um contentor. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Elimine um blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Escreva para um blob. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-blob-data-owner"></a>Proprietário de dados de Blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Fornece acesso total aos dados, incluindo a atribuição de controlo de acesso POSIX e contentores de BLOBs de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Todas as permissões em contentores. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Todas as permissões nos blobs. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-blob-data-reader"></a>Leitor de dados de Blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler e listar blobs e contentores de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve um contentor ou uma lista de contentores. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-contributor"></a>Contribuinte de dados de fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler, escrever e eliminar filas e mensagens de filas de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Elimine uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modificar os metadados de fila ou propriedades. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Elimine uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Observar ou obter uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Adicione uma mensagem numa fila. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-message-processor"></a>Processador de mensagens de dados de fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pré-visualização, obter e eliminar uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Observar uma mensagem. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Obter e eliminar uma mensagem. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-message-sender"></a>Remetente de mensagem de dados de fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Adicione mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Adicione uma mensagem numa fila. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-reader"></a>Leitor de dados de fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler e listar e mensagens de filas de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados fornecido, veja [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Observar ou obter uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="support-request-contributor"></a>Contribuidor de pedido de suporte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar e gerir pedidos de suporte |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="traffic-manager-contributor"></a>Contribuidor do Gestor de tráfego
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir perfis do Gestor de tráfego, mas não lhe permite controlar quem tem acesso aos mesmos. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="user-access-administrator"></a>Administrador de Acesso de Utilizador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o acesso dos utilizadores aos recursos do Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Ações** |  |
> | \* / leitura | Ler os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Authorization/* | Gerir a autorização |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="virtual-machine-administrator-login"></a>Início de sessão de administrador de máquinas virtuais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ver máquinas de virtuais no portal e inicie sessão como administrador |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço ip público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obtém uma definição de Balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Iniciar sessão a uma máquina virtual como um usuário normal |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Iniciar sessão a uma máquina virtual com o administrador do Windows ou Linux privilégios de utilizador de raiz |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="virtual-machine-contributor"></a>Contribuinte de máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir máquinas virtuais, mas não aceder-lhes e não a rede virtual ou conta de armazenamento, que eles estão conectados. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.Compute/availabilitySets/* | Criar e gerir conjuntos de disponibilidade de computação |
> | Microsoft.Compute/locations/* | Criar e gerir as localizações de computação |
> | Microsoft.Compute/virtualMachines/* | Criar e gerir máquinas virtuais |
> | Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerir conjuntos de dimensionamento de máquinas virtuais |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Associa um conjunto de endereços de back-end de gateway de aplicação. Não. o alerta. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Associa um conjunto de endereços de back-end de Balanceador de carga. Não. o alerta. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | É associado um balanceador de carga conjunto NAT de entrada. Não. o alerta. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Associa uma regra de nat de entrada do Balanceador de carga. Não. o alerta. |
> | Microsoft.Network/loadBalancers/probes/join/action | Permite o uso de sondas do Balanceador de carga. Por exemplo, com esta propriedade healthProbe de permissão de dimensionamento de VMS conjunto pode referenciar a sonda. Não. o alerta. |
> | Microsoft.Network/loadBalancers/read | Obtém uma definição de Balanceador de carga |
> | Microsoft.Network/locations/* | Criar e gerir as localizações de rede |
> | Microsoft.Network/networkInterfaces/* | Criar e gerir as interfaces de rede |
> | Microsoft.Network/networkSecurityGroups/join/action | Associa um grupo de segurança de rede. Não. o alerta. |
> | Microsoft.Network/networkSecurityGroups/read | Obtém uma definição de grupo de segurança de rede |
> | Microsoft.Network/publicIPAddresses/join/action | Associa um endereço ip público. Não. o alerta. |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço ip público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não. o alerta. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de cópia de segurança |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve os detalhes de objeto do Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um Item de cópia de segurança protegido |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Cria a política de proteção |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso de conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="virtual-machine-user-login"></a>Início de sessão de utilizador de máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ver as máquinas virtuais no portal e inicie sessão como um utilizador normal. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço ip público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obtém uma definição de Balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Iniciar sessão a uma máquina virtual como um usuário normal |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="web-plan-contributor"></a>Contribuinte de plano Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir planos web para Web sites, mas não aceder-lhes. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Web/serverFarms/* | Criar e gerir os farms de servidores |
> | Microsoft.Web/hostingEnvironments/Join/Action | Associa um ambiente de serviço de aplicações |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="website-contributor"></a>Contribuinte de Web site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir sites (não planos web), mas não aceder-lhes. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Autorização de leitura |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta de Insights |
> | Microsoft.Insights/components/* | Criar e gerir os componentes de informações |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém os Estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir pedidos de suporte |
> | Microsoft.Web/certificates/* | Criar e gerir certificados de Web site |
> | Microsoft.Web/listSitesAssignedToHostName/read | Obter nomes de sites atribuídos ao nome de anfitrião. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades num plano do serviço de aplicações |
> | Microsoft.Web/sites/* | Criar e gerir Web sites (criação de site também requer permissões de escrita para o plano de serviço de aplicações associado) |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="next-steps"></a>Passos Seguintes

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [Permissions in Azure Security Center](../security-center/security-center-permissions.md) (Permissões no Centro de Segurança do Azure)
