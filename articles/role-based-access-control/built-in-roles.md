---
title: Funções internas para recursos do Azure | Microsoft Docs
description: Descreve as funções internas para RBAC (controle de acesso baseado em função) e recursos do Azure. Lista as ações, não ações, dataactions e NotDataActions.
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
ms.date: 08/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 4ea0ceed80875018ee4f6e4bbcdc2548a232e9e0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989950"
---
# <a name="built-in-roles-for-azure-resources"></a>Funções internas para recursos do Azure

O [RBAC (controle de acesso baseado em função)](overview.md) tem várias funções internas para recursos do Azure que você pode atribuir a usuários, grupos, entidades de serviço e identidades gerenciadas. As atribuições de função são a maneira como você controla o acesso aos recursos do Azure. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias [funções personalizadas para recursos do Azure](custom-roles.md).

Este artigo lista as funções internas para recursos do Azure, que estão sempre em evolução. Para obter as funções mais recentes, use a lista de [definições de função](/cli/azure/role/definition#az-role-definition-list) [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou AZ. Se você estiver procurando por funções de administrador para Azure Active Directory, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Descrições da função interna

A tabela a seguir fornece uma breve descrição de cada função interna. Clique no nome da função para ver a lista `Actions`de `NotActions`, `DataActions`, e `NotDataActions` para cada função. Para obter informações sobre o que essas ações significam e como elas se aplicam aos planos de gerenciamento e de dados, consulte [entender as definições de função dos recursos do Azure](role-definitions.md).


| Função interna | Descrição |
| --- | --- |
| [Proprietário](#owner) | Permite-lhe gerir tudo, incluindo o acesso aos recursos. |
| [Contribuidor](#contributor) | Permite-lhe gerir tudo, exceto o acesso aos recursos. |
| [Leitor](#reader) | Permite-lhe ver tudo, mas não efetuar alterações. |
| [AcrDelete](#acrdelete) | exclusão de ACR |
| [AcrImageSigner](#acrimagesigner) | signatário de imagem acr |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | leitor de dados de quarentena do acr |
| [AcrQuarantineWriter](#acrquarantinewriter) | escritor de dados de quarentena do acr |
| [Colaborador do serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerir o serviço e as APIs |
| [Função de operador de serviço de gerenciamento de API](#api-management-service-operator-role) | Pode gerir o serviço, mas não as APIs |
| [Função de leitor de serviço de gerenciamento de API](#api-management-service-reader-role) | Acesso só de leitura para o serviço e APIs |
| [Colaborador de componente Application Insights](#application-insights-component-contributor) | Permite gerir componentes do Application Insights |
| [Application Insights Depurador de Instantâneos](#application-insights-snapshot-debugger) | Concede permissão ao usuário para exibir e baixar instantâneos de depuração coletados com o Depurador de Instantâneos de Application Insights. Observe que essas permissões não estão incluídas nas funções de [proprietário](#owner) ou [colaborador](#contributor) . |
| [Operador de trabalho de automação](#automation-job-operator) | Criar e Gerir Tarefas através de Runbooks de Automatização. |
| [Operador de automação](#automation-operator) | Os Operadores de Automatização podem iniciar, parar, suspender e retomar tarefas |
| [Operador de runbook de automação](#automation-runbook-operator) | Ler as propriedades do Runbook para que possa criar Tarefas do runbook. |
| [Colaborador de avere](#avere-contributor) | Pode criar e gerenciar um cluster avere vFXT. |
| [Operador avere](#avere-operator) | Usado pelo cluster avere vFXT para gerenciar o cluster |
| [Proprietário de dados dos hubs de eventos do Azure (versão prévia)](#azure-event-hubs-data-owner-preview) | Permite acesso completo aos recursos dos hubs de eventos do Azure. |
| [Receptor de dados dos hubs de eventos do Azure (versão prévia)](#azure-event-hubs-data-receiver-preview) | Permite o acesso de recebimento aos recursos dos hubs de eventos do Azure. |
| [Remetente de dados dos hubs de eventos do Azure (versão prévia)](#azure-event-hubs-data-sender-preview) | Permite o acesso de envio aos recursos dos hubs de eventos do Azure. |
| [Função de administrador de cluster do serviço kubernetes do Azure](#azure-kubernetes-service-cluster-admin-role) | Listar a ação de credenciais de administrador do cluster. |
| [Função de usuário de cluster do serviço kubernetes do Azure](#azure-kubernetes-service-cluster-user-role) | Listar a ação de credenciais de utilizador do cluster. |
| [Leitor de dados do Azure Maps (versão prévia)](#azure-maps-data-reader-preview) | Concede acesso de leitura aos dados relacionados com o mapa dados a partir de uma conta de mapas do Azure. |
| [Proprietário de dados do barramento de serviço do Azure (versão prévia)](#azure-service-bus-data-owner-preview) | Permite acesso completo aos recursos do barramento de serviço do Azure. |
| [Receptor de dados do barramento de serviço do Azure (versão prévia)](#azure-service-bus-data-receiver-preview) | Permite o acesso de recebimento aos recursos do barramento de serviço do Azure. |
| [Remetente de dados do barramento de serviço do Azure (visualização)](#azure-service-bus-data-sender-preview) | Permite o acesso de envio aos recursos do barramento de serviço do Azure. |
| [Proprietário do registro de Azure Stack](#azure-stack-registration-owner) | Permite-lhe gerir os registos do Azure Stack. |
| [Colaborador de backup](#backup-contributor) | Permite que você gerencie o serviço de backup, mas não pode criar cofres e conceder acesso a outros |
| [Operador de backup](#backup-operator) | Permite-lhe gerir serviços de cópia de segurança, exceto de remoção de cópia de segurança, criação de cofre e concessão de acesso a outros |
| [Leitor de backup](#backup-reader) | Pode ver os serviços de cópia de segurança mas não pode efetuar alterações |
| [Leitor de cobrança](#billing-reader) | Permite o acesso de leitura para dados de faturação |
| [Colaborador do BizTalk](#biztalk-contributor) | Permite-lhe gerir serviços do BizTalk, mas não aceder-lhes. |
| [Acesso ao nó de membro Blockchain (visualização)](#blockchain-member-node-access-preview) | Permite o acesso a nós de Membro de Blockchain |
| [Colaborador do ponto de extremidade da CDN](#cdn-endpoint-contributor) | Pode gerir pontos finais de CDN, as não pode conceder o acesso a outros utilizadores. |
| [Leitor de ponto de extremidade CDN](#cdn-endpoint-reader) | Pode visualizar os pontos finais de CDN, mas não pode efetuar alterações. |
| [Colaborador do perfil CDN](#cdn-profile-contributor) | Pode gerir perfis de CDN e os respetivos pontos finais, mas não pode conceder o acesso a outros utilizadores. |
| [Leitor de perfil da CDN](#cdn-profile-reader) | Pode visualizar perfis de CDN e os respetivos pontos finais, mas não pode efetuar alterações. |
| [Contribuidor de Rede Clássica](#classic-network-contributor) | Permite-lhe gerir redes virtuais, mas não aceder-lhes. |
| [Colaborador da conta de armazenamento clássica](#classic-storage-account-contributor) | Permite-lhe gerir contas de armazenamento clássico, mas não aceder às mesmas. |
| [Função de serviço do operador chave da conta de armazenamento clássica](#classic-storage-account-key-operator-service-role) | Os Operadores de Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves em Contas de Armazenamento Clássicas |
| [Colaborador da máquina virtual clássica](#classic-virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais clássicas, mas não aceder-lhes, além de que não pode gerir a rede virtual ou conta de armazenamento às quais estão ligadas. |
| [Colaborador de serviços cognitivas](#cognitive-services-contributor) | Permite-lhe criar, ler, atualizar, eliminar e gerir chaves dos Serviços Cognitivos. |
| [Leitor de dados de serviços cognitivas (versão prévia)](#cognitive-services-data-reader-preview) | Permite-lhe ler dados dos Serviços Cognitivos. |
| [Usuário de serviços cognitivas](#cognitive-services-user) | Permite-lhe ler e listar chaves dos Serviços Cognitivos. |
| [Função de leitor de conta Cosmos DB](#cosmos-db-account-reader-role) | Pode ler Azure Cosmos DB dados da conta. Consulte [colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas de Azure Cosmos DB. |
| [Operador de Cosmos DB](#cosmos-db-operator) | Permite que você gerencie contas Azure Cosmos DB, mas não acesse os dados nelas. Impede o acesso a chaves de conta e cadeias de conexão. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Pode submeter um pedido de restauro para uma base de dados do Cosmos DB ou um contentor para uma conta |
| [Colaborador de gerenciamento de custos](#cost-management-contributor) | Pode ver os custos e gerir a configuração de custos (por exemplo, orçamentos, exportações) |
| [Leitor de gerenciamento de custos](#cost-management-reader) | Pode ver os dados de custos e a configuração (por exemplo, orçamentos, exportações) |
| [Colaborador de Data Box](#data-box-contributor) | Permite-lhe gerir tudo no Serviço Data Box, exceto dar acesso a outros utilizadores. |
| [Leitor de Data Box](#data-box-reader) | Permite-lhe gerir o Serviço Data Box, exceto criar uma ordem ou editar os detalhes de uma ordem e dar acesso a outros utilizadores. |
| [Colaborador de Data Factory](#data-factory-contributor) | Criar e gerir fábricas de dados, assim como os recursos subordinados dentro dos mesmos. |
| [Data Lake Analytics Developer](#data-lake-analytics-developer) | Permite-lhe submeter, monitorizar e gerir as suas tarefas, mas não criar ou eliminar as contas do Data Lake Analytics. |
| [Limpador de dados](#data-purger) | Pode remover dados de análise |
| [Usuário do DevTest Labs](#devtest-labs-user) | Permite-lhe ligar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. |
| [Colaborador de zona DNS](#dns-zone-contributor) | Permite-lhe gerir zonas DNS e conjuntos de registos no DNS do Azure, mas não lhe permite controlar quem tem acesso aos mesmos. |
| [Colaborador de conta do DocumentDB](#documentdb-account-contributor) | Pode gerenciar contas de Azure Cosmos DB. O Azure Cosmos DB é conhecido anteriormente como DocumentDB. |
| [Colaborador EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Permite-lhe gerir operações de subscrição de eventos do EventGrid. |
| [Leitor EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Permite-lhe ler as subscrições de eventos do EventGrid. |
| [Operador de cluster HDInsight](#hdinsight-cluster-operator) | Permite que você leia e modifique as configurações do cluster HDInsight. |
| [Colaborador dos serviços de domínio do HDInsight](#hdinsight-domain-services-contributor) | Pode Ler, Criar, Modificar e Eliminar operações relacionadas com os Serviços de Domínio necessárias para o Pacote de Segurança HDInsight Enterprise |
| [Colaborador de conta de sistemas inteligentes](#intelligent-systems-account-contributor) | Permite-lhe gerir contas de Sistemas Inteligentes, mas não aceder-lhes. |
| [Colaborador de Key Vault](#key-vault-contributor) | Permite-lhe gerir cofres de chaves, mas não o acesso aos mesmos. |
| [Criador de laboratório](#lab-creator) | Permite-lhe criar, gerir e eliminar os seus laboratórios geridos nas suas Contas do Azure Lab. |
| [Colaborador de Log Analytics](#log-analytics-contributor) | Log Analytics colaborador pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Editar configurações de monitoramento inclui adicionar a extensão de VM às VMs; leitura de chaves de conta de armazenamento para poder configurar a coleta de logs do armazenamento do Azure; Criando e Configurando contas de automação; adicionando soluções; e configurar o diagnóstico do Azure em todos os recursos do Azure. |
| [Leitor de Log Analytics](#log-analytics-reader) | O Leitor do Log Analytics pode visualizar e procurar todos os dados de monitorização assim como visualizar as definições de monitorização, incluindo a visualização da configuração de diagnósticos Azure em todos os recursos do Azure. |
| [Colaborador do aplicativo lógico](#logic-app-contributor) | Permite que você gerencie aplicativos lógicos, mas não altere o acesso a eles. |
| [Operador de aplicativo lógico](#logic-app-operator) | Permite ler, habilitar e desabilitar aplicativos lógicos, mas não editá-los ou atualizá-los. |
| [Função de operador de aplicativo gerenciado](#managed-application-operator-role) | Permite-lhe ler e executar ações relacionadas com os recursos da Aplicação Gerida |
| [Leitor de aplicativos gerenciados](#managed-applications-reader) | Permite-lhe ler recursos numa aplicação gerida e pedir acesso JIT. |
| [Colaborador de identidade gerenciada](#managed-identity-contributor) | Criar, Ler, Atualizar e Eliminar a Identidade Atribuída ao Utilizador |
| [Operador de identidade gerenciada](#managed-identity-operator) | Ler e Atribuir a Identidade Atribuída ao Utilizador |
| [Colaborador do grupo de gerenciamento](#management-group-contributor) | Função de Contribuinte do Grupo de Gestão |
| [Leitor do grupo de gerenciamento](#management-group-reader) | Função de Leitor de Grupo de Gestão |
| [Colaborador de monitoramento](#monitoring-contributor) | Pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Editor de métricas de monitoramento](#monitoring-metrics-publisher) | Permite publicar métricas a respeito dos recursos do Azure |
| [Leitor de monitoramento](#monitoring-reader) | Pode ler todos os dados de monitoramento (métricas, logs, etc.). Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Contribuidor de Rede](#network-contributor) | Permite-lhe gerir redes, mas não aceder-lhes. |
| [Novo colaborador de conta do Relic APM](#new-relic-apm-account-contributor) | Permite-lhe gerir contas e aplicações do New Relic Application Performance Management, mas não aceder-lhes. |
| [Leitor e acesso a dados](#reader-and-data-access) | Permite exibir tudo, mas não permitirá que você exclua ou crie uma conta de armazenamento ou recurso contido. Ele também permitirá acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio do acesso às chaves da conta de armazenamento. |
| [Colaborador do cache Redis](#redis-cache-contributor) | Permite-lhe gerir caches de Redis, mas não aceder-lhes. |
| [Colaborador de política de recurso (versão prévia)](#resource-policy-contributor-preview) | (Pré-visualização) Os utilizadores substituídos de EA, com direitos para criar/modificar a política de recurso, criam pedidos de suporte e leem recursos/hierarquia. |
| [Colaborador de coleções de trabalhos do Agendador](#scheduler-job-collections-contributor) | Permite-lhe gerir coleções de tarefas do Scheduler, mas não aceder-lhes. |
| [Colaborador de Serviço de Pesquisa](#search-service-contributor) | Permite-lhe gerir serviços de Pesquisa, mas não aceder-lhes. |
| [Administrador de segurança](#security-admin) | Somente na central de segurança: Pode exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações |
| [Gerenciador de segurança (Herdado)](#security-manager-legacy) | Esta é uma função herdada. Em vez disso, use o administrador de segurança |
| [Leitor de segurança](#security-reader) | Somente na central de segurança: Pode exibir recomendações e alertas, exibir políticas de segurança, exibir Estados de segurança, mas não pode fazer alterações |
| [Colaborador de Site Recovery](#site-recovery-contributor) | Permite-lhe gerir o serviço do Site Recovery exceto a criação do cofre e atribuição de funções |
| [Operador de Site Recovery](#site-recovery-operator) | Permite-lhe efetuar a ativação pós-falha mas não efetuar outras operações de gestão do Site Recovery |
| [Leitor de Site Recovery](#site-recovery-reader) | Permite-lhe ver o estado do Site Recovery mas não efetuar outras operações de gestão |
| [Colaborador da conta de âncoras espaciais](#spatial-anchors-account-contributor) | Permite-lhe gerir âncoras espaciais na sua conta, mas não eliminar as mesmas |
| [Proprietário da conta das âncoras espaciais](#spatial-anchors-account-owner) | Permite-lhe gerir âncoras espaciais na sua conta, incluindo a eliminação das mesmas |
| [Leitor de conta de âncoras espaciais](#spatial-anchors-account-reader) | Permite-lhe localizar e ler as propriedades de âncoras espaciais na sua conta |
| [Colaborador do BD SQL](#sql-db-contributor) | Permite que você gerencie bancos de dados SQL, mas não tem acesso a eles. Além disso, você não pode gerenciar suas políticas relacionadas à segurança ou seus servidores SQL pai. |
| [Colaborador do SQL Instância Gerenciada](#sql-managed-instance-contributor) | Permite que você gerencie instâncias gerenciadas do SQL e a configuração de rede necessária, mas não pode conceder acesso a outras pessoas. |
| [Gerenciador de segurança do SQL](#sql-security-manager) | Permite gerir as políticas de servidores e bases de dados SQL relacionadas com a segurança, mas não aceder às mesmas. |
| [Colaborador de SQL Server](#sql-server-contributor) | Permite gerir servidores e bases de dados SQL, mas não aceder aos mesmos nem às respetivas políticas relacionadas com a segurança. |
| [Contribuidor de Conta de Armazenamento](#storage-account-contributor) | Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada. |
| [Função de serviço do operador chave da conta de armazenamento](#storage-account-key-operator-service-role) | Permite listar e regenerar chaves de acesso da conta de armazenamento. |
| [Colaborador de dados do blob de armazenamento](#storage-blob-data-contributor) | Ler, gravar e excluir contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Proprietário de dados do blob de armazenamento](#storage-blob-data-owner) | Fornece acesso completo aos dados e contêineres de blob do armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Leitor de dados de blob de armazenamento](#storage-blob-data-reader) | Leia e liste contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Delegador de blob de armazenamento](#storage-blob-delegator) | Obtenha a chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou BLOB que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [criar uma SAS de delegação de usuário](https://docs.microsoft.com/rest/api/storageservices/create-a-user-delegation-sas). |
| [Colaborador de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-contributor) | Permite acesso de leitura, gravação e exclusão em compartilhamentos de arquivos de armazenamento do Azure via SMB |
| [Colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-elevated-contributor) | Permite ler, gravar, excluir e modificar o acesso de permissão NTFS em compartilhamentos de arquivos de armazenamento do Azure via SMB |
| [Leitor de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-reader) | Permite o acesso de leitura ao compartilhamento de arquivos do Azure por SMB |
| [Colaborador de dados da fila de armazenamento](#storage-queue-data-contributor) | Ler, gravar e excluir filas do armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Processador de mensagens de dados da fila de armazenamento](#storage-queue-data-message-processor) | Espiar, recuperar e excluir uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Remetente da mensagem de dados da fila de armazenamento](#storage-queue-data-message-sender) | Adicionar mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Leitor de dados da fila de armazenamento](#storage-queue-data-reader) | Leia e liste as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Colaborador de solicitação de suporte](#support-request-contributor) | Permite-lhe criar e gerir os pedidos de Suporte |
| [Colaborador do Gerenciador de tráfego](#traffic-manager-contributor) | Permite-lhe gerir perfis do Gestor de Tráfego, mas não lhe permite controlar quem tem acesso aos mesmos. |
| [Administrador de Acesso de Utilizador](#user-access-administrator) | Permite-lhe gerir o acesso de utilizador aos recursos do Azure. |
| [Logon de administrador da máquina virtual](#virtual-machine-administrator-login) | Veja Máquinas Virtuais no portal e inicie a sessão como administrador |
| [Contribuidor de Máquina Virtual](#virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais, mas não aceder-lhes, além de que não pode gerir a rede virtual ou conta de armazenamento às quais estão ligadas. |
| [Logon de usuário da máquina virtual](#virtual-machine-user-login) | Veja Máquinas virtuais no portal e inicie a sessão como um utilizador normal. |
| [Colaborador do plano da Web](#web-plan-contributor) | Permite-lhe gerir planos Web para sites, mas não aceder-lhes. |
| [Colaborador do site](#website-contributor) | Permite-lhe gerir sites (não planos Web), mas não aceder-lhes. |


## <a name="owner"></a>Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo, incluindo o acesso aos recursos. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="contributor"></a>Contribuinte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo, exceto o acesso aos recursos. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Excluir funções e atribuições de função |
> | Microsoft. Authorization/*/Write | Criar funções e atribuições de função |
> | Microsoft.Authorization/elevateAccess/Action | Garante ao chamador Administrador de Acesso dos Utilizadores acesso ao âmbito do inquilino |
> | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar artefactos de esquema |
> | Microsoft.Blueprint/blueprintAssignments/delete | Eliminar quaisquer artefactos de esquema |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="reader"></a>Leitor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ver tudo, mas não efetuar alterações. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | exclusão de ACR |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Exclua o artefato em um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | signatário de imagem acr |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Metadados de confiança de conteúdo de push/pull para um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | acr pull |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou obter imagens de um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | acr push |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou obter imagens de um registro de contêiner. |
> | Microsoft.ContainerRegistry/registries/push/write | Enviar por Push ou gravar imagens em um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | leitor de dados de quarentena do acr |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Efetuar pull ou obter imagens em quarentena do registro de contêiner |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | escritor de dados de quarentena do acr |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Efetuar pull ou obter imagens em quarentena do registro de contêiner |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Gravar/modificar o estado de quarentena de imagens em quarentena |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-contributor"></a>Contribuinte de Serviços de Gestão de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir o serviço e as APIs |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/* | Criar e gerenciar o serviço de gerenciamento de API |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-operator-role"></a>Função de Operador de Serviço de Gestão de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir o serviço, mas não as APIs |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/backup/action | Serviço de gerenciamento de API de backup para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft.ApiManagement/service/delete | Excluir instância do serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/read | Ler metadados para uma instância do serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/restore/action | Restaurar o serviço de gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carregar o certificado SSL para um serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizados para um serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/write | Criar uma nova instância do serviço de gerenciamento de API |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas ao usuário |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-reader-role"></a>Função do Leitor do Serviço de Gestão de API do Serviço de Gestão de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Acesso só de leitura para o serviço e APIs |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/read | Ler metadados para uma instância do serviço de gerenciamento de API |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas ao usuário |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="application-insights-component-contributor"></a>Contribuinte de Componentes do Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite gerir componentes do Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Insights/components/* | Criar e gerenciar componentes do insights |
> | Microsoft.Insights/webtests/* | Criar e gerenciar testes da Web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="application-insights-snapshot-debugger"></a>Snapshot Debugger do Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Concede permissão ao usuário para exibir e baixar instantâneos de depuração coletados com o Depurador de Instantâneos de Application Insights. Observe que essas permissões não estão incluídas nas funções de [proprietário](#owner) ou [colaborador](#contributor) . |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-job-operator"></a>Operador de Tarefas de Automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar e Gerir Tarefas através de Runbooks de Automatização. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê Hybrid Runbook Worker recursos |
> | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Para um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Cria um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém a saída de um trabalho |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-operator"></a>Operador de Automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Os Operadores de Automatização podem iniciar, parar, suspender e retomar tarefas |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê Hybrid Runbook Worker recursos |
> | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Para um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Cria um trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obter um plano de trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Cria um plano de trabalho de automação do Azure |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o espaço de trabalho vinculado à conta de automação |
> | Microsoft.Automation/automationAccounts/read | Obter uma conta de automação do Azure |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook de automação do Azure |
> | Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento de automação do Azure |
> | Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento de automação do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém a saída de um trabalho |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-runbook-operator"></a>Operador de Runbook de Automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler as propriedades do Runbook para que possa criar Tarefas do runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook de automação do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="avere-contributor"></a>Colaborador de avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode criar e gerenciar um cluster avere vFXT. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Obtém os recursos do grupo de recursos. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever um blob |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="avere-operator"></a>Operador avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Usado pelo cluster avere vFXT para gerenciar o cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Ações** |  |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Devolve o resultado da eliminação de um contentor |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve a lista de contentores |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de blob put |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever um blob |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-event-hubs-data-owner-preview"></a>Proprietário de dados dos hubs de eventos do Azure (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso completo aos recursos dos hubs de eventos do Azure. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Ações** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-event-hubs-data-receiver-preview"></a>Receptor de dados dos hubs de eventos do Azure (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de recebimento aos recursos dos hubs de eventos do Azure. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-event-hubs-data-sender-preview"></a>Remetente de dados dos hubs de eventos do Azure (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de envio aos recursos dos hubs de eventos do Azure. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Função de Administrador do Cluster do Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Listar a ação de credenciais de administrador do cluster. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerido |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Função de Utilizador do Cluster do Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Listar a ação de credenciais de utilizador do cluster. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-maps-data-reader-preview"></a>Leitor de Dados do Azure Maps (Pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Concede acesso de leitura aos dados relacionados com o mapa dados a partir de uma conta de mapas do Azure. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados a uma conta do Maps. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-service-bus-data-owner-preview"></a>Proprietário de dados do barramento de serviço do Azure (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso completo aos recursos do barramento de serviço do Azure. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Ações** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-service-bus-data-receiver-preview"></a>Receptor de dados do barramento de serviço do Azure (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de recebimento aos recursos do barramento de serviço do Azure. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Ações** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-service-bus-data-sender-preview"></a>Remetente de dados do barramento de serviço do Azure (visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de envio aos recursos do barramento de serviço do Azure. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Ações** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-stack-registration-owner"></a>Proprietário do Registo do Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os registos do Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Ações** |  |
> | Microsoft. AzureStack/registrations/Products/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Obter as propriedades de um produto Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Obtém as propriedades de um registro de Azure Stack |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-contributor"></a>Contribuidor de Cópia de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie o serviço de backup, mas não pode criar cofres e conceder acesso a outros |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gerenciar resultados da operação no gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Criar e gerenciar contêineres de backup dentro de malhas de backup do cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contêineres |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Criar e gerenciar metadados relacionados ao gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar resultados de operações de gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Criar e gerenciar políticas de backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens cujo backup pode ser feito |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Criar e gerenciar itens de backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Criar e gerenciar contêineres que mantêm itens de backup |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Criar e gerenciar certificados relacionados ao backup no cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft.RecoveryServices/Vaults/usages/* | Criar e gerenciar o uso do cofre dos serviços de recuperação |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no item protegido |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegíveis |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | A operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as tentativas de proteção de backup |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-operator"></a>Operador de Cópia de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir serviços de cópia de segurança, exceto de remoção de cópia de segurança, criação de cofre e concessão de acesso a outros |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retorna o status da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da Operação efetuada no Contentor de Proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executa a Cópia de Segurança do Item Protegido. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o Resultado da Operação Efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retorna os detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar a recuperação instantânea de item para o item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter Pontos de Recuperação de Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar Pontos de Recuperação de Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação instantânea de item para o item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retorna todos os contêineres registrados |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contêineres |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar resultados de operações de gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter Resultados da Operação de Política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retorna todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens cujo backup pode ser feito |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retorna todos os contêineres que pertencem à assinatura |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação registrar contêiner de serviço pode ser usada para registrar um contêiner com o serviço de recuperação. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no item protegido |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Cria um contêiner registrado |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Fazer consulta para cargas de trabalho dentro de um contêiner |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma intenção de proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegíveis |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | A operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as tentativas de proteção de backup |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-reader"></a>Leitor de Cópia de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os serviços de cópia de segurança mas não pode efetuar alterações |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retorna o status da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da Operação efetuada no Contentor de Proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o Resultado da Operação Efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação efetuada nos Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retorna os detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter Pontos de Recuperação de Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retorna todos os contêineres registrados |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devolve o Resultado da Operação de Tarefa. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Retorna todos os objetos de trabalho |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Devolve o Resultado da Operação da Cópia de Segurança do Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter Resultados da Operação de Política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retorna todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retorna todos os contêineres que pertencem à assinatura |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Retorna a configuração de armazenamento para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Retorna a configuração do cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma intenção de proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | A operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as tentativas de proteção de backup |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="billing-reader"></a>Leitor de Faturação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de leitura para dados de faturação |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Billing/*/read | Ler informações de cobrança |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="biztalk-contributor"></a>Contribuinte do BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir serviços do BizTalk, mas não aceder-lhes. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerenciar serviços BizTalk |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="blockchain-member-node-access-preview"></a>Acesso de Nó de Membro de Blockchain (Pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso a nós de Membro de Blockchain |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Ações** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Conecta-se a um nó de transação de membro Blockchain. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-endpoint-contributor"></a>Contribuidor de Ponto Final de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir pontos finais de CDN, as não pode conceder o acesso a outros utilizadores. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-endpoint-reader"></a>Leitor do Ponto Final de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode visualizar os pontos finais de CDN, mas não pode efetuar alterações. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-profile-contributor"></a>Contribuidor de Perfil de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir perfis de CDN e os respetivos pontos finais, mas não pode conceder o acesso a outros utilizadores. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-profile-reader"></a>Leitor de Perfil de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode visualizar perfis de CDN e os respetivos pontos finais, mas não pode efetuar alterações. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-network-contributor"></a>Contribuinte de Rede Clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir redes virtuais, mas não aceder-lhes. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.ClassicNetwork/* | Criar e gerenciar redes clássicas |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-storage-account-contributor"></a>Contribuinte de Conta de Armazenamento Clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas de armazenamento clássico, mas não aceder às mesmas. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.ClassicStorage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Função do Serviço de Operador de Chave da Conta de Armazenamento Clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Os Operadores de Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves em Contas de Armazenamento Clássicas |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Ações** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Volta a gerar as chaves de acesso existentes para a conta de armazenamento. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-virtual-machine-contributor"></a>Contribuinte de Máquina Virtual Clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir máquinas virtuais clássicas, mas não aceder-lhes, além de que não pode gerir a rede virtual ou conta de armazenamento às quais estão ligadas. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.ClassicCompute/domainNames/* | Criar e gerenciar nomes de domínio de computação clássicos |
> | Microsoft.ClassicCompute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Ligar um IP reservado |
> | Microsoft.ClassicNetwork/reservedIps/read | Obtém os IPs reservados |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Associa à rede virtual. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Obtém a rede virtual. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Devolve o disco de conta de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Retorna a imagem da conta de armazenamento. Preterido. Use ' Microsoft. ClassicStorage/storageAccounts/vmImages ') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devolve a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-contributor"></a>Contribuidor de Serviços Cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar, ler, atualizar, eliminar e gerir chaves dos Serviços Cognitivos. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Obtém as funcionalidades de uma subscrição. |
> | Microsoft.Features/providers/features/read | Obtém a funcionalidade de uma subscrição de um fornecedor de recursos específico. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a configuração de diagnóstico para Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Ler definições de registo |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métricas |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-data-reader-preview"></a>Leitor de Dados dos Serviços Cognitivos (Pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler dados dos Serviços Cognitivos. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-user"></a>Utilizador de Serviços Cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e listar chaves dos Serviços Cognitivos. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Ações** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Listar Chaves |
> | Microsoft.Insights/alertRules/read | Ler um alerta de métrica clássica |
> | Microsoft.Insights/diagnosticSettings/read | Ler uma definição de diagnóstico do recurso |
> | Microsoft.Insights/logDefinitions/read | Ler definições de registo |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métricas |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cosmos-db-account-reader-role"></a>Função do Leitor da Conta do Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler Azure Cosmos DB dados da conta. Consulte [colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas de Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função, pode ler permissões dadas a cada usuário |
> | Microsoft.DocumentDB/*/read | Ler qualquer coleção |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lê as chaves somente leitura da conta do banco de dados. |
> | Microsoft.Insights/MetricDefinitions/read | Ler definições de métricas |
> | Microsoft.Insights/Metrics/read | Ler métricas |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cosmos-db-operator"></a>Operador de Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie contas Azure Cosmos DB, mas não acesse os dados nelas. Impede o acesso a chaves de conta e cadeias de conexão. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Ações** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode submeter um pedido de restauro para uma base de dados do Cosmos DB ou um contentor para uma conta |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Ações** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Enviar uma solicitação para configurar o backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Enviar uma solicitação de restauração |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cost-management-contributor"></a>Contribuidor da Gestão de Custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os custos e gerir a configuração de custos (por exemplo, orçamentos, exportações) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Ações** |  |
> | Microsoft. consumo/* |  |
> | Microsoft. CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Lista os períodos de cobrança disponíveis |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Leituras de recomendações |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cost-management-reader"></a>Leitor da Gestão de Custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os dados de custos e a configuração (por exemplo, orçamentos, exportações) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Ações** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Lista os períodos de cobrança disponíveis |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Leituras de recomendações |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-box-contributor"></a>Contribuidor de Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo no Serviço Data Box, exceto dar acesso a outros utilizadores. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Data Box/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-box-reader"></a>Leitor de Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o Serviço Data Box, exceto criar uma ordem ou editar os detalhes de uma ordem e dar acesso a outros utilizadores. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft. Data Box/*/Read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Lista as credenciais não encriptadas relativas à encomenda. |
> | Microsoft.Databox/locations/availableSkus/action | Este método devolve a lista de SKUs disponíveis. |
> | Microsoft.Databox/locations/validateAddress/action | Valida o endereço de envio e fornece endereços alternativos, caso existam. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-factory-contributor"></a>Contribuinte do Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar e gerir fábricas de dados, assim como os recursos subordinados dentro dos mesmos. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.DataFactory/dataFactories/* | Crie e gerencie fábricas de dados e recursos filho dentro deles. |
> | Microsoft.DataFactory/factories/* | Crie e gerencie fábricas de dados e recursos filho dentro deles. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-lake-analytics-developer"></a>Programador do Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe submeter, monitorizar e gerir as suas tarefas, mas não criar ou eliminar as contas do Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Excluir uma conta do DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceda permissões para cancelar trabalhos enviados por outros usuários. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Criar ou atualizar uma conta do DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta do DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desvincular uma conta do DataLakeStore de uma conta do DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Desvincular uma conta de armazenamento de uma conta do DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Excluir uma política de computação. |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-purger"></a>Eliminação de Dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode remover dados de análise |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Ações** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | A remover os dados do Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Excluir dados especificados do espaço de trabalho |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="devtest-labs-user"></a>Utilizador do DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ligar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Microsoft.Compute/virtualMachines/*/read | Ler as propriedades de uma máquina virtual (tamanhos de VM, status de tempo de execução, extensões de VM, etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Desliga a máquina virtual e liberta os recursos de computação |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Microsoft.DevTestLab/*/read | Ler as propriedades de um laboratório |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Declare uma máquina virtual de declaração aleatória no laboratório. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Crie máquinas virtuais em um laboratório. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Verifique se o usuário atual tem um perfil válido no laboratório. |
> | Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avalia a política de laboratório. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Apropriar-se de uma máquina virtual existente |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Lista as agendas de início/parada aplicáveis, se houver. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/networkInterfaces/*/read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga dos quais a interface de rede faz parte) |
> | Microsoft.Network/networkInterfaces/join/action | Une uma máquina virtual a uma interface de rede. Não é possível alertá-lo. |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Ler as propriedades de um endereço IP público |
> | Microsoft.Network/publicIPAddresses/join/action | Une um endereço IP público. Não é possível alertá-lo. |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/deployments/read | Obtém ou lista implementações. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Lista os tamanhos disponíveis para os quais a máquina virtual pode ser atualizada |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="dns-zone-contributor"></a>Contribuidor da Zona DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir zonas DNS e conjuntos de registos no DNS do Azure, mas não lhe permite controlar quem tem acesso aos mesmos. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/dnsZones/* | Criar e gerenciar registros e zonas DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="documentdb-account-contributor"></a>Contribuidor de Conta do DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar contas de Azure Cosmos DB. O Azure Cosmos DB é conhecido anteriormente como DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.DocumentDb/databaseAccounts/* | Criar e gerenciar contas de Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Contribuinte de EventSubscription EventGrid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir operações de subscrição de eventos do EventGrid. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar assinaturas de evento global por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listar assinaturas de evento regional por topictype |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Leitor de EventSubscription EventGrid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler as subscrições de eventos do EventGrid. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar assinaturas de evento global por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listar assinaturas de evento regional por topictype |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="hdinsight-cluster-operator"></a>Operador de cluster HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você leia e modifique as configurações do cluster HDInsight. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Ações** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obter configurações de gateway para o cluster HDInsight |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Atualizar as configurações do gateway para o cluster HDInsight |
> | Microsoft. HDInsight/clusters/Configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="hdinsight-domain-services-contributor"></a>Contribuidor de Serviços de Domínio HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode Ler, Criar, Modificar e Eliminar operações relacionadas com os Serviços de Domínio necessárias para o Pacote de Segurança HDInsight Enterprise |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Ações** |  |
> | Microsoft. AAD/*/Read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft. AAD/DomainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="intelligent-systems-account-contributor"></a>Contribuinte de Conta de Sistemas Inteligentes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas de Sistemas Inteligentes, mas não aceder-lhes. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.IntelligentSystems/accounts/* | Criar e gerenciar contas de sistemas inteligentes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="key-vault-contributor"></a>Contribuidor do Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir cofres de chaves, mas não o acesso aos mesmos. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Remover um cofre de chaves eliminado de forma recuperável |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="lab-creator"></a>Criador do Laboratório
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar, gerir e eliminar os seus laboratórios geridos nas suas Contas do Azure Lab. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crie um laboratório em uma conta de laboratório. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Obter informações de disponibilidade regional para cada categoria de tamanho configurada em uma conta de laboratório |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Log Analytics colaborador pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Editar configurações de monitoramento inclui adicionar a extensão de VM às VMs; leitura de chaves de conta de armazenamento para poder configurar a coleta de logs do armazenamento do Azure; Criando e Configurando contas de automação; adicionando soluções; e configurar o diagnóstico do Azure em todos os recursos do Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a configuração de diagnóstico para Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="log-analytics-reader"></a>Leitor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | O Leitor do Log Analytics pode visualizar e procurar todos os dados de monitorização assim como visualizar as definições de monitorização, incluindo a visualização da configuração de diagnósticos Azure em todos os recursos do Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="logic-app-contributor"></a>Contribuidor da Aplicação Lógica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie aplicativos lógicos, mas não altere o acesso a eles. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devolve a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a configuração de diagnóstico para Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Essa permissão é necessária para os usuários que precisam acessar os logs de atividade por meio do Portal. Listar categorias de log no log de atividades. |
> | Microsoft.Insights/metricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft.Logic/* | Gerencia recursos de aplicativos lógicos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/connectionGateways/* | Criar e gerenciar um gateway de conexão. |
> | Microsoft.Web/connections/* | Criar e gerenciar uma conexão. |
> | Microsoft.Web/customApis/* | Cria e gerencia uma API personalizada. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um plano do serviço de aplicativo |
> | Microsoft.Web/sites/functions/listSecrets/action | Listar segredos funções de aplicativos Web. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="logic-app-operator"></a>Operador de Aplicação Lógica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler, habilitar e desabilitar aplicativos lógicos, mas não editá-los ou atualizá-los. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/*/read | Ler regras de alerta do insights |
> | Microsoft.Insights/diagnosticSettings/*/read | Obter configurações de diagnóstico para aplicativos lógicos |
> | Microsoft.Insights/metricDefinitions/*/read | Obtém as métricas disponíveis para aplicativos lógicos. |
> | Microsoft.Logic/*/read | Lê os recursos dos aplicativos lógicos. |
> | Microsoft.Logic/workflows/disable/action | Desativa o fluxo de trabalho. |
> | Microsoft.Logic/workflows/enable/action | Ativa o fluxo de trabalho. |
> | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/connectionGateways/*/read | Ler gateways de conexão. |
> | Microsoft.Web/connections/*/read | Ler conexões. |
> | Microsoft.Web/customApis/*/read | Ler API personalizada. |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um plano do serviço de aplicativo |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-application-operator-role"></a>Função de Operador de Aplicações Geridas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e executar ações relacionadas com os recursos da Aplicação Gerida |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Solutions/applications/read | Obtém uma lista de aplicações. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-applications-reader"></a>Leitor de Aplicações Geridas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler recursos numa aplicação gerida e pedir acesso JIT. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-identity-contributor"></a>Contribuidor de Identidade Gerida
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar, Ler, Atualizar e Eliminar a Identidade Atribuída ao Utilizador |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtém uma identidade atribuída ao usuário existente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Cria uma nova identidade atribuída ao usuário ou atualiza as marcas associadas a uma identidade atribuída ao usuário existente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Exclui uma identidade atribuída pelo usuário existente |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-identity-operator"></a>Operador de Identidade Gerida
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler e Atribuir a Identidade Atribuída ao Utilizador |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="management-group-contributor"></a>Contribuidor do Grupo de Gestão
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Função de Contribuinte do Grupo de Gestão |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/delete | Excluir grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Desassocia a assinatura do grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associa a assinatura existente ao grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gerenciamento. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="management-group-reader"></a>Leitor de Grupo de Gestão
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Função de Leitor de Grupo de Gestão |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-contributor"></a>Contribuidor de Monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Regras de alerta de leitura/gravação/exclusão. |
> | Microsoft.Insights/components/* | Ler/gravar/excluir Application Insights componentes. |
> | Microsoft.Insights/DiagnosticSettings/* | Configurações de diagnóstico de leitura/gravação/exclusão. |
> | Microsoft.Insights/eventtypes/* | Listar eventos do log de atividades (eventos de gerenciamento) em uma assinatura. Essa permissão é aplicável a acesso de portal e programático ao log de atividades. |
> | Microsoft.Insights/LogDefinitions/* | Essa permissão é necessária para os usuários que precisam acessar os logs de atividade por meio do Portal. Listar categorias de log no log de atividades. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft.Insights/Metrics/* | Ler métricas para um recurso. |
> | Microsoft.Insights/Register/Action | Registar o fornecedor do Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Ler/gravar/excluir Application Insights testes da Web. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Ler/gravar/excluir pacotes de solução do log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Ler/gravar/excluir pesquisas salvas do log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Ler/gravar/excluir configurações de insights de armazenamento do log Analytics. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. WorkloadMonitor/monitores/* |  |
> | Microsoft. WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-metrics-publisher"></a>Publicador de Métricas de Monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite publicar métricas a respeito dos recursos do Azure |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Ações** |  |
> | Microsoft.Insights/Register/Action | Registar o fornecedor do Microsoft Insights |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Escrever métricas |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-reader"></a>Leitor de Monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitoramento (métricas, logs, etc.). Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="network-contributor"></a>Contribuinte de Rede
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir redes, mas não aceder-lhes. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/* | Criar e gerenciar redes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="new-relic-apm-account-contributor"></a>Contribuidor de Conta APM do New Relic
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas e aplicações do New Relic Application Performance Management, mas não aceder-lhes. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="reader-and-data-access"></a>Acesso de Dados e Leitor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite exibir tudo, mas não permitirá que você exclua ou crie uma conta de armazenamento ou recurso contido. Ele também permitirá acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio do acesso às chaves da conta de armazenamento. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Devolve o token de SAS de Conta para a conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="redis-cache-contributor"></a>Contribuinte de Cache de Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir caches de Redis, mas não aceder-lhes. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cache/redis/* | Criar e gerenciar caches Redis |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="resource-policy-contributor-preview"></a>Contribuinte da Política de Recursos (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | (Pré-visualização) Os utilizadores substituídos de EA, com direitos para criar/modificar a política de recurso, criam pedidos de suporte e leem recursos/hierarquia. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. Authorization/policyassignments/* | Criar e gerenciar atribuições de política |
> | Microsoft. Authorization/PolicyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft.Authorization/policysetdefinitions/* | Criar e gerenciar conjuntos de políticas |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="scheduler-job-collections-contributor"></a>Contribuinte de Coleções de Tarefas do Agendador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir coleções de tarefas do Scheduler, mas não aceder-lhes. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Scheduler/jobcollections/* | Criar e gerenciar coleções de trabalhos |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="search-service-contributor"></a>Contribuinte de Serviços de Pesquisa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir serviços de Pesquisa, mas não aceder-lhes. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Search/searchServices/* | Criar e gerenciar serviços de pesquisa |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-admin"></a>Administrador de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Somente na central de segurança: Pode exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerenciar atribuições de política |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft.Authorization/policySetDefinitions/* | Criar e gerenciar conjuntos de políticas |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.operationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-manager-legacy"></a>Gestor de Segurança (Legado)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Esta é uma função herdada. Em vez disso, use o administrador de segurança |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicCompute/*/read | Ler informações de configuração das máquinas virtuais clássicas |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Gravar configuração para máquinas virtuais clássicas |
> | Microsoft.ClassicNetwork/*/read | Ler informações de configuração sobre a rede clássica |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Security/* | Criar e gerenciar componentes e políticas de segurança |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-reader"></a>Leitor de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Somente na central de segurança: Pode exibir recomendações e alertas, exibir políticas de segurança, exibir Estados de segurança, mas não pode fazer alterações |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.operationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Security/*/read | Ler componentes e políticas de segurança |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-contributor"></a>Contribuidor do Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o serviço do Site Recovery exceto a criação do cofre e atribuição de funções |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Criar ou atualizar configurações de alerta de replicação |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Criar e gerenciar malhas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Criar e gerenciar políticas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Criar e gerenciar planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Criar e gerenciar a configuração de armazenamento do cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Ler alertas para o cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-operator"></a>Operador do Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe efetuar a ativação pós-falha mas não efetuar outras operações de gestão do Site Recovery |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler as configurações de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica a Consistência dos Recursos de infraestrutura |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler malhas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar certificado para malha |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler contêineres de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler itens protegíveis |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Ativação Pós-falha Planeada |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger novamente o item protegido |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Ativação Pós-falha de Teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do failover de teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Ativação pós-falha |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar serviço de mobilidade |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler mapeamentos de contêiner de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler provedores de serviços de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler qualquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler planos de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Reproteger plano de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plano de recuperação de failover de teste |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plano de recuperação de limpeza do failover de teste |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Ler alertas para o cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-reader"></a>Leitor do Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ver o estado do Site Recovery mas não efetuar outras operações de gestão |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler as configurações de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler malhas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler contêineres de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler itens protegíveis |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler mapeamentos de contêiner de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler provedores de serviços de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler qualquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler trabalhos |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-contributor"></a>Contribuinte de Conta de Spatial Anchors
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir âncoras espaciais na sua conta, mas não eliminar as mesmas |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir âncoras espaciais próximas |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-owner"></a>Proprietário da Conta de Spatial Anchors
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir âncoras espaciais na sua conta, incluindo a eliminação das mesmas |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Excluir âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir âncoras espaciais próximas |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-reader"></a>Leitor de Conta de Spatial Anchors
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe localizar e ler as propriedades de âncoras espaciais na sua conta |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir âncoras espaciais próximas |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-db-contributor"></a>Contribuinte de Base de Dados SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie bancos de dados SQL, mas não tem acesso a eles. Além disso, você não pode gerenciar suas políticas relacionadas à segurança ou seus servidores SQL pai. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Criar e gerenciar bancos de dados SQL |
> | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métricas |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar configurações de auditoria |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria de blob de banco de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar políticas de conexão |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento de dados |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métricas de segurança |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-managed-instance-contributor"></a>Colaborador do SQL Instância Gerenciada
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie instâncias gerenciadas do SQL e a configuração de rede necessária, mas não pode conceder acesso a outras pessoas. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Ações** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métricas |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-security-manager"></a>Gestor de Segurança SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite gerir as políticas de servidores e bases de dados SQL relacionadas com a segurança, mas não aceder às mesmas. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler a autorização da Microsoft |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Criar e gerenciar políticas de auditoria do SQL Server |
> | Microsoft.Sql/servers/auditingSettings/* | Criar e gerenciar a configuração de auditoria do SQL Server |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerenciar políticas de auditoria de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Ler registros de auditoria |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerenciar políticas de conexão de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerenciar as políticas de mascaramento de dados do SQL Server Database |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades do banco de dados especificado. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Obtenha um esquema de banco de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Obter uma tabela de banco de dados. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerenciar métricas de segurança de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do SQL Server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-server-contributor"></a>Contribuinte do SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite gerir servidores e bases de dados SQL, mas não aceder aos mesmos nem às respetivas políticas relacionadas com a segurança. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Criar e gerenciar servidores SQL |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Insights/metrics/read | Ler métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métricas |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Editar políticas de auditoria do SQL Server |
> | Microsoft.Sql/servers/auditingSettings/* | Editar configurações de auditoria do SQL Server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar configurações de auditoria do banco de dados SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Ler registros de auditoria |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar políticas de conexão de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar as políticas de mascaramento de dados do SQL Server Database |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança do banco de dados SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métricas de segurança de banco de dados do SQL Server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Editar políticas de alerta de segurança do SQL Server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-account-contributor"></a>Contribuinte de Conta de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler todas as autorizações |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Insights/diagnosticSettings/* | Gerenciar configurações de diagnóstico |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Storage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-account-key-operator-service-role"></a>Função do Serviço de Operador de Chave da Conta de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite listar e regenerar chaves de acesso da conta de armazenamento. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-contributor"></a>Contribuinte de Dados do Armazenamento de Blobs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler, gravar e excluir contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Excluir um contêiner. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retornar um contêiner ou uma lista de contêineres. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modifique os metadados ou as propriedades de um contêiner. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Excluir um blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retornar um BLOB ou uma lista de BLOBs. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Gravar em um blob. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-owner"></a>Proprietário dos Dados do Armazenamento de Blobs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Fornece acesso completo aos dados e contêineres de blob do armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Permissões totais em contêineres. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Permissões totais em BLOBs. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-reader"></a>Leitor de Dados do Armazenamento de Blobs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia e liste contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retornar um contêiner ou uma lista de contêineres. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retornar um BLOB ou uma lista de BLOBs. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-delegator"></a>Delegador de blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Obtenha uma chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou BLOB que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [criar uma SAS de delegação de usuário](https://docs.microsoft.com/rest/api/storageservices/create-a-user-delegation-sas). |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Colaborador de compartilhamento SMB de dados de arquivo de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso de leitura, gravação e exclusão em compartilhamentos de arquivos de armazenamento do Azure via SMB |
> | **Id** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retorna o resultado da exclusão de um arquivo/pasta. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler, gravar, excluir e modificar o acesso de permissão NTFS em compartilhamentos de arquivos de armazenamento do Azure via SMB |
> | **Id** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retorna o resultado da exclusão de um arquivo/pasta. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retorna o resultado da permissão de modificação em um arquivo/pasta. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-file-data-smb-share-reader"></a>Leitor de compartilhamento SMB de dados de arquivo de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de leitura ao compartilhamento de arquivos do Azure por SMB |
> | **Id** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-contributor"></a>Contribuinte de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler, gravar e excluir filas do armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Excluir uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retornar uma fila ou uma lista de filas. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modificar metadados ou propriedades da fila. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Excluir uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-message-processor"></a>Processador de Mensagens de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Espiar, recuperar e excluir uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Inspecionar uma mensagem. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Recuperar e excluir uma mensagem. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-message-sender"></a>Remetente da Mensagem de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Adicionar mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-reader"></a>Leitor de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia e liste as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="support-request-contributor"></a>Contribuidor de Pedido de Suporte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar e gerir os pedidos de Suporte |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="traffic-manager-contributor"></a>Contribuidor do Gestor de Tráfego
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir perfis do Gestor de Tráfego, mas não lhe permite controlar quem tem acesso aos mesmos. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="user-access-administrator"></a>Administrador de Acesso dos Utilizadores
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o acesso de utilizador aos recursos do Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. Authorization/* | Gerenciar autorização |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-administrator-login"></a>Início de Sessão de Administrador na Máquina Virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Veja Máquinas Virtuais no portal e inicie a sessão como administrador |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Iniciar sessão numa máquina virtual como um utilizador normal |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Inicie sessão para uma máquina virtual com o administrador do Windows ou privilégios de utilizador de raiz do Linux |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-contributor"></a>Contribuinte de Máquina Virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir máquinas virtuais, mas não aceder-lhes, além de que não pode gerir a rede virtual ou conta de armazenamento às quais estão ligadas. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.Compute/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
> | Microsoft.Compute/locations/* | Criar e gerenciar locais de computação |
> | Microsoft.Compute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de dimensionamento de máquinas virtuais |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Une um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Une um pool de NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/probes/join/action | Permite o uso de investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode fazer referência à investigação. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/locations/* | Criar e gerenciar locais de rede |
> | Microsoft.Network/networkInterfaces/* | Criar e gerenciar interfaces de rede |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft.Network/networkSecurityGroups/read | Obter uma definição de grupo de segurança de rede |
> | Microsoft.Network/publicIPAddresses/join/action | Une um endereço IP público. Não é possível alertá-lo. |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retorna os detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retorna todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Criar política de proteção |
> | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-user-login"></a>Início de Sessão de Utilizador de Máquina Virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Veja Máquinas virtuais no portal e inicie a sessão como um utilizador normal. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Iniciar sessão numa máquina virtual como um utilizador normal |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="web-plan-contributor"></a>Contribuinte de Plano Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir planos Web para sites, mas não aceder-lhes. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/serverFarms/* | Criar e gerenciar farms de servidores |
> | Microsoft.Web/hostingEnvironments/Join/Action | Une um Ambiente do Serviço de Aplicativo |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="website-contributor"></a>Contribuinte de Web site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir sites (não planos Web), mas não aceder-lhes. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leitura de autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft.Insights/components/* | Criar e gerenciar componentes do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/certificates/* | Criar e gerenciar certificados de site |
> | Microsoft.Web/listSitesAssignedToHostName/read | Obter nomes de sites atribuídos ao nome do host. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um plano do serviço de aplicativo |
> | Microsoft.Web/sites/* | Criar e gerenciar sites (a criação de site também requer permissões de gravação para o plano do serviço de aplicativo associado) |
> | **NotActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="next-steps"></a>Passos seguintes

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [Permissions in Azure Security Center](../security-center/security-center-permissions.md) (Permissões no Centro de Segurança do Azure)
