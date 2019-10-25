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
ms.date: 10/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 31e19034f6a2c6f5ab52cbc34d8b3f6e0a1051bc
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803583"
---
# <a name="built-in-roles-for-azure-resources"></a>Funções internas para recursos do Azure

O [RBAC (controle de acesso baseado em função)](overview.md) tem várias funções internas para recursos do Azure que você pode atribuir a usuários, grupos, entidades de serviço e identidades gerenciadas. As atribuições de função são a maneira como você controla o acesso aos recursos do Azure. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias [funções personalizadas para recursos do Azure](custom-roles.md).

Este artigo lista as funções internas para recursos do Azure, que estão sempre em evolução. Para obter as funções mais recentes, use a lista de [definições de função](/cli/azure/role/definition#az-role-definition-list) [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou AZ. Se você estiver procurando por funções de administrador para Azure Active Directory, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Descrições da função interna

A tabela a seguir fornece uma breve descrição de cada função interna. Clique no nome da função para ver a lista de `Actions`, `NotActions`, `DataActions`e `NotDataActions` para cada função. Para obter informações sobre o que essas ações significam e como elas se aplicam aos planos de gerenciamento e de dados, consulte [entender as definições de função dos recursos do Azure](role-definitions.md).


| Função interna | Descrição |
| --- | --- |
| [Proprietário](#owner) | Permite que você gerencie tudo, incluindo o acesso aos recursos. |
| [Contribuidor](#contributor) | Permite que você gerencie tudo, exceto conceder acesso aos recursos. |
| [Leitor](#reader) | Permite exibir tudo, mas não fazer nenhuma alteração. |
| [AcrDelete](#acrdelete) | exclusão de ACR |
| [AcrImageSigner](#acrimagesigner) | signatário de imagem ACR |
| [AcrPull](#acrpull) | pull de ACR |
| [AcrPush](#acrpush) | push de ACR |
| [AcrQuarantineReader](#acrquarantinereader) | leitor de dados de quarentena do ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | gravador de dados de quarentena do ACR |
| [Colaborador do serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerenciar o serviço e as APIs |
| [Função de operador de serviço de gerenciamento de API](#api-management-service-operator-role) | Pode gerenciar o serviço, mas não as APIs |
| [Função de leitor de serviço de gerenciamento de API](#api-management-service-reader-role) | Acesso somente leitura ao serviço e às APIs |
| [Colaborador de componente Application Insights](#application-insights-component-contributor) | Pode gerenciar componentes de Application Insights |
| [Application Insights Depurador de Instantâneos](#application-insights-snapshot-debugger) | Concede permissão ao usuário para exibir e baixar instantâneos de depuração coletados com o Depurador de Instantâneos de Application Insights. Observe que essas permissões não estão incluídas nas funções de [proprietário](#owner) ou [colaborador](#contributor) . |
| [Operador de trabalho de automação](#automation-job-operator) | Criar e gerenciar trabalhos usando Runbooks de automação. |
| [Operador de automação](#automation-operator) | Os operadores de automação são capazes de iniciar, parar, suspender e retomar trabalhos |
| [Operador de runbook de automação](#automation-runbook-operator) | Ler propriedades do runbook – para poder criar trabalhos do runbook. |
| [Colaborador de avere](#avere-contributor) | Pode criar e gerenciar um cluster avere vFXT. |
| [Operador avere](#avere-operator) | Usado pelo cluster avere vFXT para gerenciar o cluster |
| [Proprietário de dados dos hubs de eventos do Azure](#azure-event-hubs-data-owner) | Permite acesso completo aos recursos dos hubs de eventos do Azure. |
| [Receptor de dados dos hubs de eventos do Azure](#azure-event-hubs-data-receiver) | Permite o acesso de recebimento aos recursos dos hubs de eventos do Azure. |
| [Remetente de dados dos hubs de eventos do Azure](#azure-event-hubs-data-sender) | Permite o acesso de envio aos recursos dos hubs de eventos do Azure. |
| [Função de administrador de cluster do serviço kubernetes do Azure](#azure-kubernetes-service-cluster-admin-role) | Listar ação de credencial de administrador do cluster. |
| [Função de usuário de cluster do serviço kubernetes do Azure](#azure-kubernetes-service-cluster-user-role) | Listar ação de credencial de usuário de cluster. |
| [Leitor de dados do Azure Maps (versão prévia)](#azure-maps-data-reader-preview) | Concede acesso para ler dados relacionados ao mapa de uma conta do Azure Maps. |
| [Colaborador do Azure Sentinel](#azure-sentinel-contributor) | Colaborador do Azure Sentinel |
| [Leitor do Azure Sentinel](#azure-sentinel-reader) | Leitor do Azure Sentinel |
| [Respondente do Azure Sentinel](#azure-sentinel-responder) | Respondente do Azure Sentinel |
| [Proprietário de dados do barramento de serviço do Azure](#azure-service-bus-data-owner) | Permite acesso completo aos recursos do barramento de serviço do Azure. |
| [Receptor de dados do barramento de serviço do Azure](#azure-service-bus-data-receiver) | Permite o acesso de recebimento aos recursos do barramento de serviço do Azure. |
| [Remetente de dados do barramento de serviço do Azure](#azure-service-bus-data-sender) | Permite o acesso de envio aos recursos do barramento de serviço do Azure. |
| [Proprietário do registro de Azure Stack](#azure-stack-registration-owner) | Permite que você gerencie registros de Azure Stack. |
| [Colaborador de backup](#backup-contributor) | Permite que você gerencie o serviço de backup, mas não pode criar cofres e conceder acesso a outros |
| [Operador de backup](#backup-operator) | Permite que você gerencie serviços de backup, exceto remoção de backup, criação de cofre e concessão de acesso a outros |
| [Leitor de backup](#backup-reader) | Pode exibir serviços de backup, mas não pode fazer alterações |
| [Leitor de cobrança](#billing-reader) | Permite o acesso de leitura aos dados de cobrança |
| [Colaborador do BizTalk](#biztalk-contributor) | Permite que você gerencie serviços BizTalk, mas não tem acesso a eles. |
| [Acesso ao nó de membro Blockchain (visualização)](#blockchain-member-node-access-preview) | Permite o acesso a nós membro Blockchain |
| [Colaborador do Blueprint](#blueprint-contributor) | Pode gerenciar definições de plantas, mas não atribuí-las. |
| [Operador Blueprint](#blueprint-operator) | Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. Observação: isso só funcionará se a atribuição for feita com uma identidade gerenciada atribuída pelo usuário. |
| [Colaborador do ponto de extremidade da CDN](#cdn-endpoint-contributor) | Pode gerenciar pontos de extremidade CDN, mas não pode conceder acesso a outros usuários. |
| [Leitor de ponto de extremidade CDN](#cdn-endpoint-reader) | Pode exibir pontos de extremidade CDN, mas não pode fazer alterações. |
| [Colaborador do perfil CDN](#cdn-profile-contributor) | Pode gerenciar perfis CDN e seus pontos de extremidade, mas não pode conceder acesso a outros usuários. |
| [Leitor de perfil da CDN](#cdn-profile-reader) | Pode exibir perfis CDN e seus pontos de extremidade, mas não pode fazer alterações. |
| [Contribuidor de Rede Clássica](#classic-network-contributor) | Permite que você gerencie redes clássicas, mas não tem acesso a elas. |
| [Colaborador da conta de armazenamento clássica](#classic-storage-account-contributor) | Permite que você gerencie contas de armazenamento clássicas, mas não tem acesso a elas. |
| [Função de serviço do operador chave da conta de armazenamento clássica](#classic-storage-account-key-operator-service-role) | Os operadores de chave de conta de armazenamento clássico têm permissão para listar e regenerar chaves em contas de armazenamento clássicas |
| [Colaborador da máquina virtual clássica](#classic-virtual-machine-contributor) | Permite que você gerencie máquinas virtuais clássicas, mas não tem acesso a elas, e não à rede virtual ou à conta de armazenamento à qual elas estão conectadas. |
| [Colaborador de serviços cognitivas](#cognitive-services-contributor) | Permite criar, ler, atualizar, excluir e gerenciar chaves de serviços cognitivas. |
| [Leitor de dados de serviços cognitivas (versão prévia)](#cognitive-services-data-reader-preview) | Permite que você leia dados de serviços cognitivas. |
| [Usuário de serviços cognitivas](#cognitive-services-user) | Permite que você leia e liste chaves de serviços cognitivas. |
| [Função de leitor de conta Cosmos DB](#cosmos-db-account-reader-role) | Pode ler Azure Cosmos DB dados da conta. Consulte [colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas de Azure Cosmos DB. |
| [Operador de Cosmos DB](#cosmos-db-operator) | Permite que você gerencie contas Azure Cosmos DB, mas não acesse os dados nelas. Impede o acesso a chaves de conta e cadeias de conexão. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Pode enviar solicitação de restauração para um banco de dados Cosmos DB ou um contêiner para uma conta |
| [Colaborador de gerenciamento de custos](#cost-management-contributor) | Pode exibir custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações) |
| [Leitor de gerenciamento de custos](#cost-management-reader) | Pode exibir dados de custo e configuração (por exemplo, orçamentos, exportações) |
| [Colaborador de Data Box](#data-box-contributor) | Permite que você gerencie tudo em Data Box serviço, exceto conceder acesso a outras pessoas. |
| [Leitor de Data Box](#data-box-reader) | Permite que você gerencie Data Box serviço, exceto criar detalhes da ordem ou edição, e conceder acesso a outras pessoas. |
| [Colaborador de Data Factory](#data-factory-contributor) | Crie e gerencie fábricas de dados, bem como recursos filho dentro deles. |
| [Data Lake Analytics Developer](#data-lake-analytics-developer) | Permite que você envie, monitore e gerencie seus próprios trabalhos, mas não crie nem exclua contas de Data Lake Analytics. |
| [Limpador de dados](#data-purger) | Pode limpar dados de análise |
| [Usuário do DevTest Labs](#devtest-labs-user) | Permite que você conecte, inicie, reinicie e desligue suas máquinas virtuais no seu Azure DevTest Labs. |
| [Colaborador de zona DNS](#dns-zone-contributor) | Permite que você gerencie zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. |
| [Colaborador de conta do DocumentDB](#documentdb-account-contributor) | Pode gerenciar contas de Azure Cosmos DB. O Azure Cosmos DB é conhecido anteriormente como DocumentDB. |
| [Colaborador EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Permite que você gerencie operações de assinatura de evento EventGrid. |
| [Leitor EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Permite que você leia as assinaturas de evento do EventGrid. |
| [Operador de cluster HDInsight](#hdinsight-cluster-operator) | Permite que você leia e modifique as configurações do cluster HDInsight. |
| [Colaborador dos serviços de domínio do HDInsight](#hdinsight-domain-services-contributor) | Pode ler, criar, modificar e excluir as operações relacionadas ao serviço de domínio necessárias para o HDInsight Enterprise Security Package |
| [Colaborador de conta de sistemas inteligentes](#intelligent-systems-account-contributor) | Permite que você gerencie contas de sistemas inteligentes, mas não tem acesso a eles. |
| [Colaborador de Key Vault](#key-vault-contributor) | Permite que você gerencie cofres de chaves, mas não o acesso a eles. |
| [Criador de laboratório](#lab-creator) | Permite criar, gerenciar e excluir seus laboratórios gerenciados em suas contas de laboratório do Azure. |
| [Colaborador de Log Analytics](#log-analytics-contributor) | Log Analytics colaborador pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Editar configurações de monitoramento inclui adicionar a extensão de VM às VMs; leitura de chaves de conta de armazenamento para poder configurar a coleta de logs do armazenamento do Azure; Criando e Configurando contas de automação; adicionando soluções; e configurar o diagnóstico do Azure em todos os recursos do Azure. |
| [Leitor de Log Analytics](#log-analytics-reader) | Log Analytics leitor pode exibir e Pesquisar todos os dados de monitoramento, bem como exibir configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. |
| [Colaborador do aplicativo lógico](#logic-app-contributor) | Permite que você gerencie aplicativos lógicos, mas não altere o acesso a eles. |
| [Operador de aplicativo lógico](#logic-app-operator) | Permite ler, habilitar e desabilitar aplicativos lógicos, mas não editá-los ou atualizá-los. |
| [Função de operador de aplicativo gerenciado](#managed-application-operator-role) | Permite que você leia e execute ações em recursos de aplicativos gerenciados |
| [Leitor de aplicativos gerenciados](#managed-applications-reader) | Permite que você leia recursos em um aplicativo gerenciado e solicite acesso JIT. |
| [Colaborador de identidade gerenciada](#managed-identity-contributor) | Criar, ler, atualizar e excluir a identidade atribuída ao usuário |
| [Operador de identidade gerenciada](#managed-identity-operator) | Ler e atribuir identidade atribuída ao usuário |
| [Colaborador do grupo de gerenciamento](#management-group-contributor) | Função colaborador do grupo de gerenciamento |
| [Leitor do grupo de gerenciamento](#management-group-reader) | Função de leitor do grupo de gerenciamento |
| [Colaborador de monitoramento](#monitoring-contributor) | Pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Editor de métricas de monitoramento](#monitoring-metrics-publisher) | Permite a publicação de métricas em relação aos recursos do Azure |
| [Leitor de monitoramento](#monitoring-reader) | Pode ler todos os dados de monitoramento (métricas, logs, etc.). Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Contribuidor de Rede](#network-contributor) | Permite que você gerencie redes, mas não tem acesso a elas. |
| [Novo colaborador de conta do Relic APM](#new-relic-apm-account-contributor) | Permite que você gerencie contas e aplicativos Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles. |
| [Leitor e acesso a dados](#reader-and-data-access) | Permite exibir tudo, mas não permitirá que você exclua ou crie uma conta de armazenamento ou recurso contido. Ele também permitirá acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio do acesso às chaves da conta de armazenamento. |
| [Colaborador do cache Redis](#redis-cache-contributor) | Permite que você gerencie caches Redis, mas não tem acesso a eles. |
| [Colaborador de política de recurso (versão prévia)](#resource-policy-contributor-preview) | Apresentação Usuários com preenchimento de EA, com direitos para criar/modificar a política de recursos, criar tíquete de suporte e ler recursos/hierarquia. |
| [Colaborador de coleções de trabalhos do Agendador](#scheduler-job-collections-contributor) | Permite que você gerencie coleções de trabalhos do Agendador, mas não tem acesso a elas. |
| [Colaborador de Serviço de Pesquisa](#search-service-contributor) | Permite que você gerencie serviços de pesquisa, mas não tem acesso a eles. |
| [Administrador de segurança](#security-admin) | Somente na central de segurança: pode exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações |
| [Gerenciador de segurança (Herdado)](#security-manager-legacy) | Esta é uma função herdada. Em vez disso, use o administrador de segurança |
| [Leitor de segurança](#security-reader) | Somente na central de segurança: pode exibir recomendações e alertas, exibir políticas de segurança, exibir Estados de segurança, mas não pode fazer alterações |
| [Colaborador de Site Recovery](#site-recovery-contributor) | Permite que você gerencie Site Recovery serviço, exceto criação de cofre e atribuição de função |
| [Operador de Site Recovery](#site-recovery-operator) | Permite failover e failback, mas não executa outras operações de gerenciamento de Site Recovery |
| [Leitor de Site Recovery](#site-recovery-reader) | Permite exibir Site Recovery status, mas não executar outras operações de gerenciamento |
| [Colaborador da conta de âncoras espaciais](#spatial-anchors-account-contributor) | Permite gerenciar âncoras espaciais em sua conta, mas não excluí-las |
| [Proprietário da conta das âncoras espaciais](#spatial-anchors-account-owner) | Permite que você gerencie âncoras espaciais em sua conta, incluindo excluí-las |
| [Leitor de conta de âncoras espaciais](#spatial-anchors-account-reader) | Permite localizar e ler propriedades de âncoras espaciais em sua conta |
| [Colaborador do BD SQL](#sql-db-contributor) | Permite que você gerencie bancos de dados SQL, mas não tem acesso a eles. Além disso, você não pode gerenciar suas políticas relacionadas à segurança ou seus servidores SQL pai. |
| [Colaborador do SQL Instância Gerenciada](#sql-managed-instance-contributor) | Permite que você gerencie instâncias gerenciadas do SQL e a configuração de rede necessária, mas não pode conceder acesso a outras pessoas. |
| [Gerenciador de segurança do SQL](#sql-security-manager) | Permite que você gerencie as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acesso a eles. |
| [Colaborador de SQL Server](#sql-server-contributor) | Permite gerenciar servidores e bancos de dados SQL, mas não acessá-los, nem suas políticas relacionadas à segurança. |
| [Contribuidor de Conta de Armazenamento](#storage-account-contributor) | Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada. |
| [Função de serviço do operador chave da conta de armazenamento](#storage-account-key-operator-service-role) | Permite listar e regenerar chaves de acesso da conta de armazenamento. |
| [Colaborador de dados do blob de armazenamento](#storage-blob-data-contributor) | Ler, gravar e excluir contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Proprietário de dados do blob de armazenamento](#storage-blob-data-owner) | Fornece acesso completo aos dados e contêineres de blob do armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Leitor de dados de blob de armazenamento](#storage-blob-data-reader) | Leia e liste contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Delegador de blob de armazenamento](#storage-blob-delegator) | Obtenha uma chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou BLOB que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [criar uma SAS de delegação de usuário](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
| [Colaborador de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-contributor) | Permite acesso de leitura, gravação e exclusão em compartilhamentos de arquivos de armazenamento do Azure via SMB |
| [Colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-elevated-contributor) | Permite ler, gravar, excluir e modificar o acesso de permissão NTFS em compartilhamentos de arquivos de armazenamento do Azure via SMB |
| [Leitor de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-reader) | Permite o acesso de leitura ao compartilhamento de arquivos do Azure por SMB |
| [Colaborador de dados da fila de armazenamento](#storage-queue-data-contributor) | Ler, gravar e excluir filas do armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Processador de mensagens de dados da fila de armazenamento](#storage-queue-data-message-processor) | Espiar, recuperar e excluir uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Remetente da mensagem de dados da fila de armazenamento](#storage-queue-data-message-sender) | Adicionar mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Leitor de dados da fila de armazenamento](#storage-queue-data-reader) | Leia e liste as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Colaborador de solicitação de suporte](#support-request-contributor) | Permite criar e gerenciar solicitações de suporte |
| [Colaborador do Gerenciador de tráfego](#traffic-manager-contributor) | Permite que você gerencie perfis do Gerenciador de tráfego, mas não permite que você controle quem tem acesso a eles. |
| [Administrador de Acesso de Utilizador](#user-access-administrator) | Permite que você gerencie o acesso do usuário aos recursos do Azure. |
| [Logon de administrador da máquina virtual](#virtual-machine-administrator-login) | Exibir máquinas virtuais no portal e fazer logon como administrador |
| [Contribuidor de Máquina Virtual](#virtual-machine-contributor) | Permite que você gerencie máquinas virtuais, mas não tem acesso a elas, e não à rede virtual ou à conta de armazenamento à qual elas estão conectadas. |
| [Logon de usuário da máquina virtual](#virtual-machine-user-login) | Exiba as máquinas virtuais no portal e faça logon como um usuário normal. |
| [Colaborador do plano da Web](#web-plan-contributor) | Permite que você gerencie os planos da Web para sites, mas não acesso a eles. |
| [Colaborador do site](#website-contributor) | Permite que você gerencie sites (não planos da Web), mas não tem acesso a eles. |


## <a name="owner"></a>Proprietário
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie tudo, incluindo o acesso aos recursos. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="contributor"></a>Contribuinte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie tudo, exceto conceder acesso aos recursos. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | Microsoft. Authorization/*/Delete | Excluir funções, atribuições de política, definições de política e definições de conjunto de políticas |
> | Microsoft. Authorization/*/Write | Criar funções, atribuições de função, atribuições de política, definições de política e definições de conjunto de políticas |
> | Microsoft. Authorization/elevateAccess/Action | Concede ao usuário do chamador acesso de administrador no escopo do locatário |
> | Microsoft. Blueprint/blueprintAssignments/Write | Criar ou atualizar quaisquer atribuições de Blueprint |
> | Microsoft. Blueprint/blueprintAssignments/Delete | Excluir quaisquer atribuições de Blueprint |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="reader"></a>Leitor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite exibir tudo, mas não fazer nenhuma alteração. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
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
> | Microsoft. ContainerRegistry/registros/artefatos/excluir | Exclua o artefato em um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | signatário de imagem ACR |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/sinal/gravação | Metadados de confiança de conteúdo de push/pull para um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | pull de ACR |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/pull/leitura | Efetuar pull ou obter imagens de um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | push de ACR |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/pull/leitura | Efetuar pull ou obter imagens de um registro de contêiner. |
> | Microsoft. ContainerRegistry/registros/Push/gravação | Enviar por Push ou gravar imagens em um registro de contêiner. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | leitor de dados de quarentena do ACR |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/quarentena/leitura | Efetuar pull ou obter imagens em quarentena do registro de contêiner |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | gravador de dados de quarentena do ACR |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/quarentena/leitura | Efetuar pull ou obter imagens em quarentena do registro de contêiner |
> | Microsoft. ContainerRegistry/registros/quarentena/gravação | Gravar/modificar o estado de quarentena de imagens em quarentena |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-contributor"></a>Colaborador do serviço de gerenciamento de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar o serviço e as APIs |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Ações** |  |
> | Microsoft. ApiManagement/Service/* | Criar e gerenciar o serviço de gerenciamento de API |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-operator-role"></a>Função de operador de serviço de gerenciamento de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar o serviço, mas não as APIs |
> | **Id** | e022efe7-F5BA-4159-bbe4-b44f577e9b61 |
> | **Ações** |  |
> | Microsoft. ApiManagement/Service/*/Read | Ler instâncias do serviço de gerenciamento de API |
> | Microsoft. ApiManagement/Service/backup/Action | Serviço de gerenciamento de API de backup para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft. ApiManagement/Service/Delete | Excluir instância do serviço de gerenciamento de API |
> | Microsoft. ApiManagement/Service/managedeployments/Action | Alterar SKU/unidades, adicionar/remover implantações regionais do serviço de gerenciamento de API |
> | Microsoft. ApiManagement/serviço/leitura | Ler metadados para uma instância do serviço de gerenciamento de API |
> | Microsoft. ApiManagement/Service/Restore/Action | Restaurar o serviço de gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft. ApiManagement/Service/updatecertificate/Action | Carregar o certificado SSL para um serviço de gerenciamento de API |
> | Microsoft. ApiManagement/Service/updatehostname/Action | Configurar, atualizar ou remover nomes de domínio personalizados para um serviço de gerenciamento de API |
> | Microsoft. ApiManagement/Service/Write | Criar uma nova instância do serviço de gerenciamento de API |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft. ApiManagement/Service/Users/Keys/Read | Obter chaves associadas ao usuário |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-reader-role"></a>Função de leitor de serviço de gerenciamento de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Acesso somente leitura ao serviço e às APIs |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Ações** |  |
> | Microsoft. ApiManagement/Service/*/Read | Ler instâncias do serviço de gerenciamento de API |
> | Microsoft. ApiManagement/serviço/leitura | Ler metadados para uma instância do serviço de gerenciamento de API |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft. ApiManagement/Service/Users/Keys/Read | Obter chaves associadas ao usuário |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="application-insights-component-contributor"></a>Colaborador de componente Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar componentes de Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. insights/Components/* | Criar e gerenciar componentes do insights |
> | Microsoft. insights/webtests/* | Criar e gerenciar testes da Web |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Depurador de Instantâneos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Concede permissão ao usuário para exibir e baixar instantâneos de depuração coletados com o Depurador de Instantâneos de Application Insights. Observe que essas permissões não estão incluídas nas funções de [proprietário](#owner) ou [colaborador](#contributor) . |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. insights/Components/*/Read |  |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-job-operator"></a>Operador de trabalho de automação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar e gerenciar trabalhos usando Runbooks de automação. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Lê Hybrid Runbook Worker recursos |
> | Microsoft. Automation/automationAccounts/Jobs/ler | Obter um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/retomar/ação | Retoma um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/parar/ação | Para um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/streams/Read | Obter um fluxo de trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/suspender/ação | Suspende um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Cria um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/saída/leitura | Obtém a saída de um trabalho |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-operator"></a>Operador de Automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Os operadores de automação são capazes de iniciar, parar, suspender e retomar trabalhos |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Lê Hybrid Runbook Worker recursos |
> | Microsoft. Automation/automationAccounts/Jobs/ler | Obter um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/retomar/ação | Retoma um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/parar/ação | Para um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/streams/Read | Obter um fluxo de trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/suspender/ação | Suspende um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Cria um trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/jobSchedules/Read | Obter um plano de trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/jobSchedules/Write | Cria um plano de trabalho de automação do Azure |
> | Microsoft. Automation/automationAccounts/linkedWorkspace/Read | Obtém o espaço de trabalho vinculado à conta de automação |
> | Microsoft. Automation/automationAccounts/Read | Obter uma conta de automação do Azure |
> | Microsoft. Automation/automationAccounts/runbooks/ler | Obter um runbook de automação do Azure |
> | Microsoft. Automation/automationAccounts/Schedules/Read | Obter um ativo de agendamento de automação do Azure |
> | Microsoft. Automation/automationAccounts/Schedules/Write | Criar ou atualizar um ativo de agendamento de automação do Azure |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Automation/automationAccounts/Jobs/saída/leitura | Obtém a saída de um trabalho |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-runbook-operator"></a>Operador de runbook de automação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler propriedades do runbook – para poder criar trabalhos do runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Automation/automationAccounts/runbooks/ler | Obter um runbook de automação do Azure |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
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
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Compute/*/Read |  |
> | Microsoft. Compute/availabilitySets/* |  |
> | Microsoft. Compute/virtualMachines/* |  |
> | Microsoft. Compute/disks/* |  |
> | Microsoft. Network/*/Read |  |
> | Microsoft. Network/networkInterfaces/* |  |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Network/virtualNetworks/sub-redes/leitura | Obter uma definição de sub-rede de rede virtual |
> | Microsoft. Network/virtualNetworks/sub-redes/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/Action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft. Network/networkSecurityGroups/junção/ação | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/*/Read |  |
> | Microsoft. Storage/storageAccounts/* |  |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Resources/subscriptions/resourceGroups/Resources/Read | Obtém os recursos para o grupo de recursos. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/excluir | Retorna o resultado da exclusão de um blob |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/leitura | Retorna um BLOB ou uma lista de BLOBs |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/gravação | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="avere-operator"></a>Operador avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Usado pelo cluster avere vFXT para gerenciar o cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Ações** |  |
> | Microsoft. Compute/virtualMachines/Read | Obter as propriedades de uma máquina virtual |
> | Microsoft. Network/networkInterfaces/Read | Obtém uma definição de interface de rede.  |
> | Microsoft. Network/networkInterfaces/Write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Network/virtualNetworks/sub-redes/leitura | Obter uma definição de sub-rede de rede virtual |
> | Microsoft. Network/virtualNetworks/sub-redes/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft. Network/networkSecurityGroups/junção/ação | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/blobservices/containers/Delete | Retorna o resultado da exclusão de um contêiner |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/leitura | Retorna a lista de contêineres |
> | Microsoft. Storage/storageAccounts/blobservices/containers/Write | Retorna o resultado do contêiner de blob put |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/excluir | Retorna o resultado da exclusão de um blob |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/leitura | Retorna um BLOB ou uma lista de BLOBs |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/gravação | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-event-hubs-data-owner"></a>Proprietário de dados dos hubs de eventos do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso completo aos recursos dos hubs de eventos do Azure. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Ações** |  |
> | Microsoft. EventHub/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-event-hubs-data-receiver"></a>Receptor de dados dos hubs de eventos do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de recebimento aos recursos dos hubs de eventos do Azure. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Ações** |  |
> | Microsoft. EventHub/*/Eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. EventHub/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-event-hubs-data-sender"></a>Remetente de dados dos hubs de eventos do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de envio aos recursos dos hubs de eventos do Azure. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Ações** |  |
> | Microsoft. EventHub/*/Eventhubs/Read |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Função de administrador de cluster do serviço kubernetes do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Listar ação de credencial de administrador do cluster. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Ações** |  |
> | Microsoft. ContainerService/managedClusters/listClusterAdminCredential/Action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Função de usuário de cluster do serviço kubernetes do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Listar ação de credencial de usuário de cluster. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Ações** |  |
> | Microsoft. ContainerService/managedClusters/listClusterUserCredential/Action | Listar a credencial clusterUser de um cluster gerenciado |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-maps-data-reader-preview"></a>Leitor de dados do Azure Maps (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Concede acesso para ler dados relacionados ao mapa de uma conta do Azure Maps. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Maps/contas/dados/ler | Concede acesso de leitura de dados a uma conta do Maps. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-sentinel-contributor"></a>Colaborador do Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Colaborador do Azure Sentinel |
> | **Id** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Ações** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft. OperationalInsights/Workspaces/Analytics/consulta/ação | Pesquisar usando o novo mecanismo. |
> | Microsoft. OperationalInsights/Workspaces/Read | Obter um espaço de trabalho existente |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/* |  |
> | Microsoft. OperationsManagement/soluções/leitura | Obter solução OMS de saída |
> | Microsoft. OperationalInsights/Workspaces/consulta/leitura | Executar consultas sobre os dados no espaço de trabalho |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de origem em um espaço de trabalho. |
> | Microsoft. insights/pastas de trabalho/* |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-sentinel-reader"></a>Leitor do Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leitor do Azure Sentinel |
> | **Id** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Ações** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. OperationalInsights/Workspaces/Analytics/consulta/ação | Pesquisar usando o novo mecanismo. |
> | Microsoft. OperationalInsights/Workspaces/Read | Obter um espaço de trabalho existente |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/Read | Obtém uma consulta de pesquisa salva |
> | Microsoft. OperationsManagement/soluções/leitura | Obter solução OMS de saída |
> | Microsoft. OperationalInsights/Workspaces/consulta/leitura | Executar consultas sobre os dados no espaço de trabalho |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de origem em um espaço de trabalho. |
> | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-sentinel-responder"></a>Respondente do Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Respondente do Azure Sentinel |
> | **Id** | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | **Ações** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/cases/* |  |
> | Microsoft. OperationalInsights/Workspaces/Analytics/consulta/ação | Pesquisar usando o novo mecanismo. |
> | Microsoft. OperationalInsights/Workspaces/Read | Obter um espaço de trabalho existente |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de origem em um espaço de trabalho. |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/Read | Obtém uma consulta de pesquisa salva |
> | Microsoft. OperationsManagement/soluções/leitura | Obter solução OMS de saída |
> | Microsoft. OperationalInsights/Workspaces/consulta/leitura | Executar consultas sobre os dados no espaço de trabalho |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de origem em um espaço de trabalho. |
> | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-service-bus-data-owner"></a>Proprietário de dados do barramento de serviço do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite acesso completo aos recursos do barramento de serviço do Azure. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Ações** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-service-bus-data-receiver"></a>Receptor de dados do barramento de serviço do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de recebimento aos recursos do barramento de serviço do Azure. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Ações** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. ServiceBus/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-service-bus-data-sender"></a>Remetente de dados do barramento de serviço do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de envio aos recursos do barramento de serviço do Azure. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Ações** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-stack-registration-owner"></a>Proprietário do registro de Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie registros de Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Ações** |  |
> | Microsoft. AzureStack/registrations/Products/*/Action |  |
> | Microsoft. AzureStack/registros/produtos/leitura | Obter as propriedades de um produto Azure Stack Marketplace |
> | Microsoft. AzureStack/registros/leitura | Obtém as propriedades de um registro de Azure Stack |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-contributor"></a>Colaborador de backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie o serviço de backup, mas não pode criar cofres e conceder acesso a outros |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Recoveryservices/Locations/* |  |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/operationResults/* | Gerenciar resultados da operação no gerenciamento de backup |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/* | Criar e gerenciar contêineres de backup dentro de malhas de backup do cofre dos serviços de recuperação |
> | Microsoft. Recoveryservices/cofres/backupFabrics/refreshContainers/ação | Atualiza a lista de contêineres |
> | Microsoft. Recoveryservices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft. Recoveryservices/Vaultes/backupJobsExport/Action | Exportar trabalhos |
> | Microsoft. Recoveryservices/Vaults/backupManagementMetaData/* | Criar e gerenciar metadados relacionados ao gerenciamento de backup |
> | Microsoft. Recoveryservices/Vaults/backupOperationResults/* | Criar e gerenciar resultados de operações de gerenciamento de backup |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/* | Criar e gerenciar políticas de backup |
> | Microsoft. Recoveryservices/Vaults/backupProtectableItems/* | Criar e gerenciar itens cujo backup pode ser feito |
> | Microsoft. Recoveryservices/Vaults/backupProtectedItems/* | Criar e gerenciar itens de backup |
> | Microsoft. Recoveryservices/Vaults/backupProtectionContainers/* | Criar e gerenciar contêineres que mantêm itens de backup |
> | Microsoft. Recoveryservices/Vaults/backupSecurityPIN/* |  |
> | Microsoft. Recoveryservices/Vaults/backupUsageSummaries/Read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Microsoft. Recoveryservices/cofres/certificados/* | Criar e gerenciar certificados relacionados ao backup no cofre dos serviços de recuperação |
> | Microsoft. Recoveryservices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/* |  |
> | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft. Recoveryservices/cofres/usos/* | Criar e gerenciar o uso do cofre dos serviços de recuperação |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft. Recoveryservices/Vaults/backupstorageconfig/* |  |
> | Microsoft. Recoveryservices/Vaults/BackupConfig/* |  |
> | Microsoft. Recoveryservices/Vaultes/backupValidateOperation/Action | Validar operação no item protegido |
> | Microsoft. Recoveryservices/cofres/gravação | A operação criar cofre cria um recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/Vaults/backupOperations/Read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/backupEngines/Read | Retorna todos os servidores de gerenciamento de backup registrados com o cofre. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectableContainers/Read | Obter todos os contêineres protegíveis |
> | Microsoft. Recoveryservices/Locations/backupStatus/Action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Microsoft. Recoveryservices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. Recoveryservices/Locations/backupValidateFeatures/Action | Validar recursos |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Write | Resolve o alerta. |
> | Microsoft. Recoveryservices/Operations/Read | A operação retorna a lista de operações para um provedor de recursos |
> | Microsoft. Recoveryservices/Locations/operationStatus/Read | Obtém o status da operação para uma determinada operação |
> | Microsoft. Recoveryservices/Vaults/backupProtectionIntents/Read | Listar todas as tentativas de proteção de backup |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-operator"></a>Operador de backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie serviços de backup, exceto remoção de backup, criação de cofre e concessão de acesso a outros |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/operationResults/Read | Retorna o status da operação |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/Read | Obtém o resultado da operação executada no contêiner de proteção. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/ação | Executa o backup para o item protegido. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Obtém o resultado da operação executada em itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Retorna o status da operação executada nos itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Retorna os detalhes do objeto do item protegido |
> | Microsoft. Recoveryservices/Vaultes/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Provisionar a recuperação instantânea de item para o item protegido |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Obter pontos de recuperação para itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Restaurar pontos de recuperação para itens protegidos. |
> | Microsoft. Recoveryservices/Vaultes/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Revogar a recuperação instantânea de item para o item protegido |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Criar um item protegido de backup |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/Read | Retorna todos os contêineres registrados |
> | Microsoft. Recoveryservices/cofres/backupFabrics/refreshContainers/ação | Atualiza a lista de contêineres |
> | Microsoft. Recoveryservices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft. Recoveryservices/Vaultes/backupJobsExport/Action | Exportar trabalhos |
> | Microsoft. Recoveryservices/Vaults/backupManagementMetaData/Read |  |
> | Microsoft. Recoveryservices/Vaults/backupOperationResults/* | Criar e gerenciar resultados de operações de gerenciamento de backup |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/operationResults/Read | Obter resultados da operação de política. |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/Read | Retorna todas as políticas de proteção |
> | Microsoft. Recoveryservices/Vaults/backupProtectableItems/* | Criar e gerenciar itens cujo backup pode ser feito |
> | Microsoft. Recoveryservices/Vaults/backupProtectedItems/Read | Retorna a lista de todos os itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupProtectionContainers/Read | Retorna todos os contêineres que pertencem à assinatura |
> | Microsoft. Recoveryservices/Vaults/backupUsageSummaries/Read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Microsoft. Recoveryservices/cofres/certificados/gravação | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Microsoft. Recoveryservices/Vaults/extendedInformation/Read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Microsoft. Recoveryservices/Vaults/extendedInformation/Write | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/* |  |
> | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/operationResults/Read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/Read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/Write | A operação registrar contêiner de serviço pode ser usada para registrar um contêiner com o serviço de recuperação. |
> | Microsoft. Recoveryservices/cofres/usos/leitura | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft. Recoveryservices/Vaults/backupstorageconfig/* |  |
> | Microsoft. Recoveryservices/Vaultes/backupValidateOperation/Action | Validar operação no item protegido |
> | Microsoft. Recoveryservices/Vaults/backupOperations/Read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/Operations/Read | Obter o status da operação de política. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/Write | Cria um contêiner registrado |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/Action | Fazer consulta para cargas de trabalho dentro de um contêiner |
> | Microsoft. Recoveryservices/Vaults/backupEngines/Read | Retorna todos os servidores de gerenciamento de backup registrados com o cofre. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/backupProtectionIntent/Write | Criar uma intenção de proteção de backup |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/backupProtectionIntent/Read | Obter uma intenção de proteção de backup |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectableContainers/Read | Obter todos os contêineres protegíveis |
> | Microsoft. Recoveryservices/cofres/backupFabrics/protectionContainers/itens/ler | Obter todos os itens em um contêiner |
> | Microsoft. Recoveryservices/Locations/backupStatus/Action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Microsoft. Recoveryservices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. Recoveryservices/Locations/backupValidateFeatures/Action | Validar recursos |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Write | Resolve o alerta. |
> | Microsoft. Recoveryservices/Operations/Read | A operação retorna a lista de operações para um provedor de recursos |
> | Microsoft. Recoveryservices/Locations/operationStatus/Read | Obtém o status da operação para uma determinada operação |
> | Microsoft. Recoveryservices/Vaults/backupProtectionIntents/Read | Listar todas as tentativas de proteção de backup |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-reader"></a>Leitor de backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode exibir serviços de backup, mas não pode fazer alterações |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Recoveryservices/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/operationResults/Read | Retorna o status da operação |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/Read | Obtém o resultado da operação executada no contêiner de proteção. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Obtém o resultado da operação executada em itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Retorna o status da operação executada nos itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Retorna os detalhes do objeto do item protegido |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Obter pontos de recuperação para itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/Read | Retorna todos os contêineres registrados |
> | Microsoft. Recoveryservices/Vaults/backupJobs/operationResults/Read | Retorna o resultado da operação de trabalho. |
> | Microsoft. Recoveryservices/Vaults/backupJobs/Read | Retorna todos os objetos de trabalho |
> | Microsoft. Recoveryservices/Vaultes/backupJobsExport/Action | Exportar trabalhos |
> | Microsoft. Recoveryservices/Vaults/backupManagementMetaData/Read |  |
> | Microsoft. Recoveryservices/Vaults/backupOperationResults/Read | Retorna o resultado da operação de backup para o cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/operationResults/Read | Obter resultados da operação de política. |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/Read | Retorna todas as políticas de proteção |
> | Microsoft. Recoveryservices/Vaults/backupProtectedItems/Read | Retorna a lista de todos os itens protegidos. |
> | Microsoft. Recoveryservices/Vaults/backupProtectionContainers/Read | Retorna todos os contêineres que pertencem à assinatura |
> | Microsoft. Recoveryservices/Vaults/backupUsageSummaries/Read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/extendedInformation/Read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/operationResults/Read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/Read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft. Recoveryservices/Vaults/backupstorageconfig/Read | Retorna a configuração de armazenamento para o cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/BackupConfig/Read | Retorna a configuração do cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/backupOperations/Read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/Operations/Read | Obter o status da operação de política. |
> | Microsoft. Recoveryservices/Vaults/backupEngines/Read | Retorna todos os servidores de gerenciamento de backup registrados com o cofre. |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/backupProtectionIntent/Read | Obter uma intenção de proteção de backup |
> | Microsoft. Recoveryservices/cofres/backupFabrics/protectionContainers/itens/ler | Obter todos os itens em um contêiner |
> | Microsoft. Recoveryservices/Locations/backupStatus/Action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/* |  |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Write | Resolve o alerta. |
> | Microsoft. Recoveryservices/Operations/Read | A operação retorna a lista de operações para um provedor de recursos |
> | Microsoft. Recoveryservices/Locations/operationStatus/Read | Obtém o status da operação para uma determinada operação |
> | Microsoft. Recoveryservices/Vaults/backupProtectionIntents/Read | Listar todas as tentativas de proteção de backup |
> | Microsoft. Recoveryservices/cofres/usos/leitura | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="billing-reader"></a>Leitor de Faturação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso de leitura aos dados de cobrança |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. billing/*/Read | Ler informações de cobrança |
> | Microsoft. Commerce/*/Read |  |
> | Microsoft. consumo/*/Read |  |
> | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="biztalk-contributor"></a>Colaborador do BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie serviços BizTalk, mas não tem acesso a eles. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. BizTalkservices/BizTalk/* | Criar e gerenciar serviços BizTalk |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="blockchain-member-node-access-preview"></a>Acesso ao nó de membro Blockchain (visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso a nós membro Blockchain |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Ações** |  |
> | Microsoft. Blockchain/blockchainMembers/transactionNodes/Read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Blockchain/blockchainMembers/transactionNodes/Connect/Action | Conecta-se a um nó de transação de membro Blockchain. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="blueprint-contributor"></a>Colaborador do Blueprint
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar definições de plantas, mas não atribuí-las. |
> | **Id** | 41077137-E803-4205-871c-5a86e6a753b4 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Blueprint/plantas/* | Crie e gerencie definições de plantas ou artefatos de Blueprint. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="blueprint-operator"></a>Operador Blueprint
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. Observação: isso só funcionará se a atribuição for feita com uma identidade gerenciada atribuída pelo usuário. |
> | **Id** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Blueprint/blueprintAssignments/* | Criar e gerenciar atribuições de plano gráfico. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-endpoint-contributor"></a>Colaborador do ponto de extremidade da CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar pontos de extremidade CDN, mas não pode conceder acesso a outros usuários. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/perfis/pontos de extremidade/* |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-endpoint-reader"></a>Leitor de ponto de extremidade CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode exibir pontos de extremidade CDN, mas não pode fazer alterações. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/perfis/pontos de extremidade/*/Read |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-profile-contributor"></a>Colaborador do perfil CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar perfis CDN e seus pontos de extremidade, mas não pode conceder acesso a outros usuários. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/* |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-profile-reader"></a>Leitor de perfil da CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode exibir perfis CDN e seus pontos de extremidade, mas não pode fazer alterações. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/*/Read |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-network-contributor"></a>Colaborador de rede clássico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie redes clássicas, mas não tem acesso a elas. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. ClassicNetwork/* | Criar e gerenciar redes clássicas |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-storage-account-contributor"></a>Colaborador da conta de armazenamento clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie contas de armazenamento clássicas, mas não tem acesso a elas. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. ClassicStorage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Função de serviço do operador chave da conta de armazenamento clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Os operadores de chave de conta de armazenamento clássico têm permissão para listar e regenerar chaves em contas de armazenamento clássicas |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Ações** |  |
> | Microsoft. ClassicStorage/storageAccounts/listkeys/Action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft. ClassicStorage/storageAccounts/regeneratekey/Action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-virtual-machine-contributor"></a>Colaborador da máquina virtual clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie máquinas virtuais clássicas, mas não tem acesso a elas, e não à rede virtual ou à conta de armazenamento à qual elas estão conectadas. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. ClassicCompute/nome_do_domínio/* | Criar e gerenciar nomes de domínio de computação clássicos |
> | Microsoft. ClassicCompute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft. ClassicNetwork/networkSecurityGroups/junção/ação |  |
> | Microsoft. ClassicNetwork/reservedIps/link/ação | Vincular um IP reservado |
> | Microsoft. ClassicNetwork/reservedIps/Read | Obter os IPs reservados |
> | Microsoft. ClassicNetwork/virtualNetworks/junção/ação | Une a rede virtual. |
> | Microsoft. ClassicNetwork/virtualNetworks/Read | Obter a rede virtual. |
> | Microsoft. ClassicStorage/storageAccounts/discos/ler | Retorna o disco da conta de armazenamento. |
> | Microsoft. ClassicStorage/storageAccounts/images/Read | Retorna a imagem da conta de armazenamento. Preterido. Use ' Microsoft. ClassicStorage/storageAccounts/vmImages ') |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft. ClassicStorage/storageAccounts/Read | Retorne a conta de armazenamento com a conta especificada. |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-contributor"></a>Colaborador de serviços cognitivas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite criar, ler, atualizar, excluir e gerenciar chaves de serviços cognitivas. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Cognitivaservices/* |  |
> | Microsoft. recursos/recursos/ler | Obtém os recursos de uma assinatura. |
> | Microsoft. Features/Providers/Features/Read | Obtém o recurso de uma assinatura em um determinado provedor de recursos. |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. insights/diagnosticSettings/* | Cria, atualiza ou lê a configuração de diagnóstico para Analysis Server |
> | Microsoft. insights/logDefinitions/Read | Ler definições de log |
> | Microsoft. insights/metricdefinitions/Read | Ler definições de métrica |
> | Microsoft. insights/métricas/leitura | Ler métricas |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/implantações/operações/leitura | Obtém ou lista operações de implantação. |
> | Microsoft. Resources/subscriptions/operationresults/Read | Obter os resultados da operação de assinatura. |
> | Microsoft. Resources/subscriptions/Read | Obtém a lista de assinaturas. |
> | Microsoft. Resources/subscriptions/resourcegroups/Implantations/* |  |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-data-reader-preview"></a>Leitor de dados de serviços cognitivas (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você leia dados de serviços cognitivas. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Cognitivaservices/*/Read |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-user"></a>Usuário de serviços cognitivas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você leia e liste chaves de serviços cognitivas. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Ações** |  |
> | Microsoft. Cognitivaservices/*/Read |  |
> | Microsoft. Cognitivaservices/accounts/listkeys/Action | Listar chaves |
> | Microsoft. insights/alertRules/Read | Ler um alerta de métrica clássico |
> | Microsoft. insights/diagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Microsoft. insights/logDefinitions/Read | Ler definições de log |
> | Microsoft. insights/metricdefinitions/Read | Ler definições de métrica |
> | Microsoft. insights/métricas/leitura | Ler métricas |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/implantações/operações/leitura | Obtém ou lista operações de implantação. |
> | Microsoft. Resources/subscriptions/operationresults/Read | Obter os resultados da operação de assinatura. |
> | Microsoft. Resources/subscriptions/Read | Obtém a lista de assinaturas. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Cognitivaservices/* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cosmos-db-account-reader-role"></a>Função de leitor de conta Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler Azure Cosmos DB dados da conta. Consulte [colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas de Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função, pode ler permissões dadas a cada usuário |
> | Microsoft. DocumentDB/*/Read | Ler qualquer coleção |
> | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Action | Lê as chaves somente leitura da conta do banco de dados. |
> | Microsoft. insights/MetricDefinitions/Read | Ler definições de métrica |
> | Microsoft. insights/métricas/leitura | Ler métricas |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
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
> | Microsoft. DocumentDb/databaseAccounts/* |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode enviar solicitação de restauração para um banco de dados Cosmos DB ou um contêiner para uma conta |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Ações** |  |
> | Microsoft. DocumentDB/databaseAccounts/backup/ação | Enviar uma solicitação para configurar o backup |
> | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Enviar uma solicitação de restauração |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cost-management-contributor"></a>Colaborador de gerenciamento de custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode exibir custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Ações** |  |
> | Microsoft. consumo/* |  |
> | Microsoft. CostManagement/* |  |
> | Microsoft. billing/billingPeriods/Read |  |
> | Microsoft. Resources/subscriptions/Read | Obtém a lista de assinaturas. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Advisor/Configurations/ler | Obter configurações |
> | Microsoft. Advisor/Recommendations/Read | Leituras de recomendações |
> | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cost-management-reader"></a>Leitor de gerenciamento de custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode exibir dados de custo e configuração (por exemplo, orçamentos, exportações) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Ações** |  |
> | Microsoft. consumo/*/Read |  |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. billing/billingPeriods/Read |  |
> | Microsoft. Resources/subscriptions/Read | Obtém a lista de assinaturas. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Advisor/Configurations/ler | Obter configurações |
> | Microsoft. Advisor/Recommendations/Read | Leituras de recomendações |
> | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-box-contributor"></a>Colaborador de Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie tudo em Data Box serviço, exceto conceder acesso a outras pessoas. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Data Box/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-box-reader"></a>Leitor de Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie Data Box serviço, exceto criar detalhes da ordem ou edição, e conceder acesso a outras pessoas. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Data Box/*/Read |  |
> | Microsoft. Data Box/Jobs/listsecrets/ação |  |
> | Microsoft. Data Box/Jobs/listcredentials/ação | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Microsoft. Data Box/Locations/availableSkus/Action | Esse método retorna a lista de SKUs disponíveis. |
> | Microsoft. Data Box/Locations/validateAddress/Action | Valida o endereço de envio e fornece endereços alternativos, se houver. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-factory-contributor"></a>Colaborador de Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Crie e gerencie fábricas de dados, bem como recursos filho dentro deles. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. datafactory/datafactorings/* | Crie e gerencie fábricas de dados e recursos filho dentro deles. |
> | Microsoft. datafactory/fábricas/* | Crie e gerencie fábricas de dados e recursos filho dentro deles. |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você envie, monitore e gerencie seus próprios trabalhos, mas não crie nem exclua contas de Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. BigAnalytics/accounts/* |  |
> | Microsoft. DataLakeAnalytics/accounts/* |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/contas/excluir |  |
> | Microsoft. BigAnalytics/accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/contas/gravação |  |
> | Microsoft. DataLakeAnalytics/contas/excluir | Excluir uma conta do DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/TakeOwnership/Action | Conceda permissões para cancelar trabalhos enviados por outros usuários. |
> | Microsoft. DataLakeAnalytics/contas/gravação | Criar ou atualizar uma conta do DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta do DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desvincular uma conta do DataLakeStore de uma conta do DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Delete | Desvincular uma conta de armazenamento de uma conta do DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Microsoft. DataLakeAnalytics/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
> | Microsoft. DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | Microsoft. DataLakeAnalytics/accounts/computePolicies/Delete | Excluir uma política de computação. |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-purger"></a>Limpador de dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode limpar dados de análise |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Ações** |  |
> | Microsoft. insights/Components/*/Read |  |
> | Microsoft. insights/componentes/limpeza/ação | Limpando dados de Application Insights |
> | Microsoft. OperationalInsights/Workspaces/*/Read |  |
> | Microsoft. OperationalInsights/espaços de trabalho/limpeza/ação | Excluir dados especificados do espaço de trabalho |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="devtest-labs-user"></a>Usuário do DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você conecte, inicie, reinicie e desligue suas máquinas virtuais no seu Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Compute/availabilitySets/Read | Obter as propriedades de um conjunto de disponibilidade |
> | Microsoft. Compute/virtualMachines/*/Read | Ler as propriedades de uma máquina virtual (tamanhos de VM, status de tempo de execução, extensões de VM, etc.) |
> | Microsoft. Compute/virtualMachines/desallocate/ação | Desliga a máquina virtual e libera os recursos de computação |
> | Microsoft. Compute/virtualMachines/Read | Obter as propriedades de uma máquina virtual |
> | Microsoft. Compute/virtualMachines/Restart/Action | Reinicia a máquina virtual |
> | Microsoft. Compute/virtualMachines/iniciar/ação | Inicia a máquina virtual |
> | Microsoft. DevTestLab/*/Read | Ler as propriedades de um laboratório |
> | Microsoft. DevTestLab/Labs/claimAnyVm/Action | Declare uma máquina virtual de declaração aleatória no laboratório. |
> | Microsoft. DevTestLab/Labs/createambiente/ação | Crie máquinas virtuais em um laboratório. |
> | Microsoft. DevTestLab/Labs/ensureCurrentUserProfile/Action | Verifique se o usuário atual tem um perfil válido no laboratório. |
> | Microsoft. DevTestLab/Labs/fórmulas/excluir | Excluir fórmulas. |
> | Microsoft. DevTestLab/Labs/fórmulas/ler | Ler fórmulas. |
> | Microsoft. DevTestLab/Labs/fórmulas/gravar | Adicionar ou modificar fórmulas. |
> | Microsoft. DevTestLab/Labs/policySets/evaluatePolicies/Action | Avalia a política de laboratório. |
> | Microsoft. DevTestLab/Labs/virtualMachines/declaração/ação | Apropriar-se de uma máquina virtual existente |
> | Microsoft. DevTestLab/Labs/VirtualMachines/listApplicableSchedules/Action | Lista as agendas de início/parada aplicáveis, se houver. |
> | Microsoft. DevTestLab/Labs/virtualMachines/getRdpFileContents/Action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Microsoft. Network/balancers/backendAddressPools/junção/ação | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft. Network/balancers/inboundNatRules/junção/ação | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft. Network/networkInterfaces/*/Read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga dos quais a interface de rede faz parte) |
> | Microsoft. Network/networkInterfaces/junção/ação | Une uma máquina virtual a uma interface de rede. Não é possível alertá-lo. |
> | Microsoft. Network/networkInterfaces/Read | Obtém uma definição de interface de rede.  |
> | Microsoft. Network/networkInterfaces/Write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft. Network/publicIPAddresses/*/Read | Ler as propriedades de um endereço IP público |
> | Microsoft. Network/publicIPAddresses/junção/ação | Une um endereço IP público. Não é possível alertá-lo. |
> | Microsoft. Network/publicIPAddresses/Read | Obtém uma definição de endereço IP público. |
> | Microsoft. Network/virtualNetworks/sub-redes/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft. Resources/implantações/operações/leitura | Obtém ou lista operações de implantação. |
> | Microsoft. Resources/implantações/leitura | Obtém ou lista implantações. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | **NotActions** |  |
> | Microsoft. Compute/virtualMachines/vmSizes/Read | Lista os tamanhos disponíveis para os quais a máquina virtual pode ser atualizada |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="dns-zone-contributor"></a>Colaborador de zona DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. Network/dnsZones/* | Criar e gerenciar registros e zonas DNS |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="documentdb-account-contributor"></a>Colaborador de conta do DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerenciar contas de Azure Cosmos DB. O Azure Cosmos DB é conhecido anteriormente como DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. DocumentDb/databaseAccounts/* | Criar e gerenciar contas de Azure Cosmos DB |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Colaborador EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie operações de assinatura de evento EventGrid. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. EventGrid/eventSubscriptions/* |  |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Listar assinaturas de evento global por tipo de tópico |
> | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Listar assinaturas de eventos regionais |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/Read | Listar assinaturas de evento regional por topictype |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Leitor EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você leia as assinaturas de evento do EventGrid. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. EventGrid/eventSubscriptions/Read | Ler um eventSubscription |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Listar assinaturas de evento global por tipo de tópico |
> | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Listar assinaturas de eventos regionais |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/Read | Listar assinaturas de evento regional por topictype |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
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
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft. HDInsight/clusters/getGatewaySettings/ação | Obter configurações de gateway para o cluster HDInsight |
> | Microsoft. HDInsight/clusters/updateGatewaySettings/ação | Atualizar as configurações do gateway para o cluster HDInsight |
> | Microsoft. HDInsight/clusters/Configurations/* |  |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Resources/implantações/operações/leitura | Obtém ou lista operações de implantação. |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="hdinsight-domain-services-contributor"></a>Colaborador dos serviços de domínio do HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler, criar, modificar e excluir as operações relacionadas ao serviço de domínio necessárias para o HDInsight Enterprise Security Package |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Ações** |  |
> | Microsoft. AAD/*/Read |  |
> | Microsoft. AAD/DomainServices/*/Read |  |
> | Microsoft. AAD/DomainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="intelligent-systems-account-contributor"></a>Colaborador de conta de sistemas inteligentes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie contas de sistemas inteligentes, mas não tem acesso a eles. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. IntelligentSystems/accounts/* | Criar e gerenciar contas de sistemas inteligentes |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="key-vault-contributor"></a>Colaborador de Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie cofres de chaves, mas não o acesso a eles. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. keyvault/* |  |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft. keyvault/Locations/deletedVaults/limpeza/ação | Limpar um cofre de chaves com exclusão reversível |
> | Microsoft. keyvault/hsmPools/* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="lab-creator"></a>Criador de laboratório
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite criar, gerenciar e excluir seus laboratórios gerenciados em suas contas de laboratório do Azure. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. LabServices/labAccounts/*/Read |  |
> | Microsoft. LabServices/labAccounts/createLab/Action | Crie um laboratório em uma conta de laboratório. |
> | Microsoft. LabServices/labAccounts/Reports/getRegionalAvailability/Action |  |
> | Microsoft. LabServices/labAccounts/getRegionalAvailability/Action | Obter informações de disponibilidade regional para cada categoria de tamanho configurada em uma conta de laboratório |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
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
> | Microsoft. Automation/automationAccounts/* |  |
> | Microsoft. ClassicCompute/virtualMachines/Extensions/* |  |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft. Compute/virtualMachines/Extensions/* |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. insights/diagnosticSettings/* | Cria, atualiza ou lê a configuração de diagnóstico para Analysis Server |
> | Microsoft. OperationalInsights/* |  |
> | Microsoft. OperationsManagement/* |  |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourcegroups/Implantations/* |  |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="log-analytics-reader"></a>Leitor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Log Analytics leitor pode exibir e Pesquisar todos os dados de monitoramento, bem como exibir configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. OperationalInsights/Workspaces/Analytics/consulta/ação | Pesquisar usando o novo mecanismo. |
> | Microsoft. OperationalInsights/Workspaces/pesquisa/ação | Executa uma consulta de pesquisa |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft. OperationalInsights/Workspaces/sharedKeys/Read | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="logic-app-contributor"></a>Colaborador do aplicativo lógico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie aplicativos lógicos, mas não altere o acesso a eles. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Lista as chaves de acesso para as contas de armazenamento. |
> | Microsoft. ClassicStorage/storageAccounts/Read | Retorne a conta de armazenamento com a conta especificada. |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. insights/metricAlerts/* |  |
> | Microsoft. insights/diagnosticSettings/* | Cria, atualiza ou lê a configuração de diagnóstico para Analysis Server |
> | Microsoft. insights/logdefinitions/* | Essa permissão é necessária para os usuários que precisam acessar os logs de atividade por meio do Portal. Listar categorias de log no log de atividades. |
> | Microsoft. insights/metricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft. Logic/* | Gerencia recursos de aplicativos lógicos. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/operationresults/Read | Obter os resultados da operação de assinatura. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/listkeys/Action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Web/connectionGateways/* | Criar e gerenciar um gateway de conexão. |
> | Microsoft. Web/Connections/* | Criar e gerenciar uma conexão. |
> | Microsoft. Web/customApis/* | Cria e gerencia uma API personalizada. |
> | Microsoft. Web/serverFarms/junção/ação |  |
> | Microsoft. Web/serverFarms/leitura | Obter as propriedades em um plano do serviço de aplicativo |
> | Microsoft. Web/sites/funções/listSecrets/ação | Listar segredos de função. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="logic-app-operator"></a>Operador de aplicativo lógico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler, habilitar e desabilitar aplicativos lógicos, mas não editá-los ou atualizá-los. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/*/Read | Ler regras de alerta do insights |
> | Microsoft. insights/metricAlerts/*/Read |  |
> | Microsoft. insights/diagnosticSettings/*/Read | Obter configurações de diagnóstico para aplicativos lógicos |
> | Microsoft. insights/metricDefinitions/*/Read | Obtém as métricas disponíveis para aplicativos lógicos. |
> | Microsoft. Logic/*/Read | Lê os recursos dos aplicativos lógicos. |
> | Microsoft. Logic/fluxos de trabalho/desabilitar/ação | Desabilita o fluxo de trabalho. |
> | Microsoft. Logic/fluxos de trabalho/habilitar/ação | Habilita o fluxo de trabalho. |
> | Microsoft. Logic/workflows/valida/Action | Valida o fluxo de trabalho. |
> | Microsoft. Resources/implantações/operações/leitura | Obtém ou lista operações de implantação. |
> | Microsoft. Resources/subscriptions/operationresults/Read | Obter os resultados da operação de assinatura. |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Web/connectionGateways/*/Read | Ler gateways de conexão. |
> | Microsoft. Web/Connections/*/Read | Ler conexões. |
> | Microsoft. Web/customApis/*/Read | Ler API personalizada. |
> | Microsoft. Web/serverFarms/leitura | Obter as propriedades em um plano do serviço de aplicativo |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-application-operator-role"></a>Função de operador de aplicativo gerenciado
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você leia e execute ações em recursos de aplicativos gerenciados |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. Solutions/Applications/Read | Recupera uma lista de aplicativos. |
> | Microsoft. Solutions/*/Action |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-applications-reader"></a>Leitor de aplicativos gerenciados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você leia recursos em um aplicativo gerenciado e solicite acesso JIT. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-identity-contributor"></a>Colaborador de identidade gerenciada
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar, ler, atualizar e excluir a identidade atribuída ao usuário |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Ações** |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/Read | Obtém uma identidade atribuída ao usuário existente |
> | Microsoft. ManagedIdentity/userAssignedIdentities/Write | Cria uma nova identidade atribuída ao usuário ou atualiza as marcas associadas a uma identidade atribuída ao usuário existente |
> | Microsoft. ManagedIdentity/userAssignedIdentities/Delete | Exclui uma identidade atribuída pelo usuário existente |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-identity-operator"></a>Operador de identidade gerenciada
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler e atribuir identidade atribuída ao usuário |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Ações** |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/*/Read |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/*/Assign/Action |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="management-group-contributor"></a>Colaborador do grupo de gerenciamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Função colaborador do grupo de gerenciamento |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Ações** |  |
> | Microsoft. Management/managementGroups/Delete | Excluir grupo de gerenciamento. |
> | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft. Management/managementGroups/assinaturas/excluir | Desassocia a assinatura do grupo de gerenciamento. |
> | Microsoft. Management/managementGroups/assinaturas/gravação | Associa a assinatura existente ao grupo de gerenciamento. |
> | Microsoft. Management/managementGroups/Write | Criar ou atualizar um grupo de gerenciamento. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="management-group-reader"></a>Leitor do grupo de gerenciamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Função de leitor do grupo de gerenciamento |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Ações** |  |
> | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-contributor"></a>Colaborador de monitoramento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. AlertsManagement/Alerts/* |  |
> | Microsoft. AlertsManagement/alertsSummary/* |  |
> | Microsoft. insights/actiongroups/* |  |
> | Microsoft. insights/activityLogAlerts/* |  |
> | Microsoft. insights/AlertRules/* | Regras de alerta de leitura/gravação/exclusão. |
> | Microsoft. insights/Components/* | Ler/gravar/excluir Application Insights componentes. |
> | Microsoft. insights/DiagnosticSettings/* | Configurações de diagnóstico de leitura/gravação/exclusão. |
> | Microsoft. insights/EventTypes/* | Listar eventos do log de atividades (eventos de gerenciamento) em uma assinatura. Essa permissão é aplicável a acesso de portal e programático ao log de atividades. |
> | Microsoft. insights/LogDefinitions/* | Essa permissão é necessária para os usuários que precisam acessar os logs de atividade por meio do Portal. Listar categorias de log no log de atividades. |
> | Microsoft. insights/metricalerts/* |  |
> | Microsoft. insights/MetricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft. insights/métricas/* | Ler métricas para um recurso. |
> | Microsoft. insights/registro/ação | Registrar o provedor do Microsoft insights |
> | Microsoft. insights/scheduledqueryrules/* |  |
> | Microsoft. insights/webtests/* | Ler/gravar/excluir Application Insights testes da Web. |
> | Microsoft. insights/pastas de trabalho/* |  |
> | Microsoft. OperationalInsights/Workspaces/intelligencepacks/* | Ler/gravar/excluir pacotes de solução do log Analytics. |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/* | Ler/gravar/excluir pesquisas salvas do log Analytics. |
> | Microsoft. OperationalInsights/Workspaces/pesquisa/ação | Executa uma consulta de pesquisa |
> | Microsoft. OperationalInsights/Workspaces/sharedKeys/Action | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/* | Ler/gravar/excluir configurações de insights de armazenamento do log Analytics. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. WorkloadMonitor/monitores/* |  |
> | Microsoft. WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-metrics-publisher"></a>Editor de métricas de monitoramento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a publicação de métricas em relação aos recursos do Azure |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Ações** |  |
> | Microsoft. insights/registro/ação | Registrar o provedor do Microsoft insights |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. insights/métricas/gravação | Métricas de gravação |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-reader"></a>Leitor de monitoramento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitoramento (métricas, logs, etc.). Consulte também [introdução às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. OperationalInsights/Workspaces/pesquisa/ação | Executa uma consulta de pesquisa |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="network-contributor"></a>Colaborador de rede
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie redes, mas não tem acesso a elas. |
> | **Id** | 4d97b98b-1d4f-4787-A291-c67834d212e7 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. Network/* | Criar e gerenciar redes |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="new-relic-apm-account-contributor"></a>Novo colaborador de conta do Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie contas e aplicativos Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | NewRelic. APM/accounts/* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="reader-and-data-access"></a>Leitor e acesso a dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite exibir tudo, mas não permitirá que você exclua ou crie uma conta de armazenamento ou recurso contido. Ele também permitirá acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio do acesso às chaves da conta de armazenamento. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Microsoft. Storage/storageAccounts/ListAccountSas/Action | Retorna o token SAS da conta para a conta de armazenamento especificada. |
> | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="redis-cache-contributor"></a>Colaborador do cache Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie caches Redis, mas não tem acesso a eles. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. cache/Redis/* | Criar e gerenciar caches Redis |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="resource-policy-contributor-preview"></a>Colaborador de política de recurso (versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Apresentação Usuários com preenchimento de EA, com direitos para criar/modificar a política de recursos, criar tíquete de suporte e ler recursos/hierarquia. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. Authorization/policyassignments/* | Criar e gerenciar atribuições de política |
> | Microsoft. Authorization/PolicyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft. Authorization/policysetdefinitions/* | Criar e gerenciar conjuntos de políticas |
> | Microsoft. PolicyInsights/* |  |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="scheduler-job-collections-contributor"></a>Colaborador de coleções de trabalhos do Agendador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie coleções de trabalhos do Agendador, mas não tem acesso a elas. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Scheduler/gratuitas/* | Criar e gerenciar coleções de trabalhos |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="search-service-contributor"></a>Colaborador de Serviço de Pesquisa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie serviços de pesquisa, mas não tem acesso a eles. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Search/SearchServices/* | Criar e gerenciar serviços de pesquisa |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-admin"></a>Administrador de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Somente na central de segurança: pode exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Authorization/policyAssignments/* | Criar e gerenciar atribuições de política |
> | Microsoft. Authorization/policyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft. Authorization/policySetDefinitions/* | Criar e gerenciar conjuntos de políticas |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft. operationalInsights/Workspaces/*/Read | Exibir dados do log Analytics |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Security/* |  |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-manager-legacy"></a>Gerenciador de segurança (Herdado)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Esta é uma função herdada. Em vez disso, use o administrador de segurança |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. ClassicCompute/*/Read | Ler informações de configuração das máquinas virtuais clássicas |
> | Microsoft. ClassicCompute/virtualMachines/*/Write | Gravar configuração para máquinas virtuais clássicas |
> | Microsoft. ClassicNetwork/*/Read | Ler informações de configuração sobre a rede clássica |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Security/* | Criar e gerenciar componentes e políticas de segurança |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-reader"></a>Leitor de segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Somente na central de segurança: pode exibir recomendações e alertas, exibir políticas de segurança, exibir Estados de segurança, mas não pode fazer alterações |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. operationalInsights/Workspaces/*/Read | Exibir dados do log Analytics |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Security/*/Read | Ler componentes e políticas de segurança |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-contributor"></a>Colaborador de Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie Site Recovery serviço, exceto criação de cofre e atribuição de função |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Recoveryservices/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft. Recoveryservices/Locations/allocateStamp/Action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft. Recoveryservices/cofres/certificados/gravação | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Microsoft. Recoveryservices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/Vaults/refreshContainers/Read |  |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft. Recoveryservices/Vaults/replicationAlertSettings/* | Criar ou atualizar configurações de alerta de replicação |
> | Microsoft. Recoveryservices/Vaults/replicationEvents/Read | Ler todos os eventos |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/* | Criar e gerenciar malhas de replicação |
> | Microsoft. Recoveryservices/Vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft. Recoveryservices/Vaults/replicationPolicies/* | Criar e gerenciar políticas de replicação |
> | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/* | Criar e gerenciar planos de recuperação |
> | Microsoft. Recoveryservices/Vaults/storageConfig/* | Criar e gerenciar a configuração de armazenamento do cofre dos serviços de recuperação |
> | Microsoft. Recoveryservices/Vaults/tokenInfo/Read |  |
> | Microsoft. Recoveryservices/cofres/usos/leitura | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/vaultTokens/Read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/* | Ler alertas para o cofre dos serviços de recuperação |
> | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-operator"></a>Operador de Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite failover e failback, mas não executa outras operações de gerenciamento de Site Recovery |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Recoveryservices/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft. Recoveryservices/Locations/allocateStamp/Action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft. Recoveryservices/Vaults/extendedInformation/Read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/Vaults/refreshContainers/Read |  |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/operationResults/Read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/Read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft. Recoveryservices/Vaults/replicationAlertSettings/Read | Ler as configurações de alertas |
> | Microsoft. Recoveryservices/Vaults/replicationEvents/Read | Ler todos os eventos |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/checkConsistency/ação | Verifica a consistência da malha |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/Read | Ler malhas |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/reassociateGateway/ação | Reassociar gateway |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/renewcertificate/ação | Renovar certificado para malha |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationNetworks/Read | Ler redes |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Ler mapeamentos de rede |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/Read | Ler contêineres de proteção |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Ler itens protegíveis |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/ação | Aplicar ponto de recuperação |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/ação | Confirmação de failover |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/ação | Failover planejado |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Ler itens protegidos |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Ler pontos de recuperação de replicação |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/ação | Reparar replicação |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/nova proteção/ação | Proteger novamente o item protegido |
> | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Alternar contêiner de proteção |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/ação | Ativação Pós-falha de Teste |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/ação | Limpeza do failover de teste |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/ação | Ativação Pós-Falha |
> | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/ação | Atualizar serviço de mobilidade |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Ler mapeamentos de contêiner de proteção |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Ler provedores de serviços de recuperação |
> | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationRecoveryServicesProviders/reualizarprovider/Action | Atualizar provedor |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/Read | Ler classificações de armazenamento |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Ler mapeamentos de classificação de armazenamento |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationvCenters/Read | Ler qualquer vCenters |
> | Microsoft. Recoveryservices/Vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft. Recoveryservices/Vaults/replicationPolicies/Read | Ler políticas |
> | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/failoverCommit/ação | Plano de recuperação de confirmação de failover |
> | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/plannedFailover/ação | Plano de recuperação de failover planejado |
> | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/Read | Ler planos de recuperação |
> | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/nova proteção/ação | Reproteger plano de recuperação |
> | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/testFailover/ação | Plano de recuperação de failover de teste |
> | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/testFailoverCleanup/ação | Plano de recuperação de limpeza do failover de teste |
> | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/unplannedFailover/ação | Plano de recuperação de failover |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/* | Ler alertas para o cofre dos serviços de recuperação |
> | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. Recoveryservices/Vaults/storageConfig/Read |  |
> | Microsoft. Recoveryservices/Vaults/tokenInfo/Read |  |
> | Microsoft. Recoveryservices/cofres/usos/leitura | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/vaultTokens/Read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-reader"></a>Leitor de Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite exibir Site Recovery status, mas não executar outras operações de gerenciamento |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. Recoveryservices/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft. Recoveryservices/Vaults/extendedInformation/Read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/Vaults/refreshContainers/Read |  |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/operationResults/Read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft. Recoveryservices/Vaults/registeredIdentities/Read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft. Recoveryservices/Vaults/replicationAlertSettings/Read | Ler as configurações de alertas |
> | Microsoft. Recoveryservices/Vaults/replicationEvents/Read | Ler todos os eventos |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/Read | Ler malhas |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationNetworks/Read | Ler redes |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Ler mapeamentos de rede |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/Read | Ler contêineres de proteção |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Ler itens protegíveis |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Ler itens protegidos |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Ler pontos de recuperação de replicação |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Ler mapeamentos de contêiner de proteção |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Ler provedores de serviços de recuperação |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/Read | Ler classificações de armazenamento |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Ler mapeamentos de classificação de armazenamento |
> | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationvCenters/Read | Ler qualquer vCenters |
> | Microsoft. Recoveryservices/Vaults/replicationJobs/Read | Ler trabalhos |
> | Microsoft. Recoveryservices/Vaults/replicationPolicies/Read | Ler políticas |
> | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/Read | Ler planos de recuperação |
> | Microsoft. Recoveryservices/Vaults/storageConfig/Read |  |
> | Microsoft. Recoveryservices/Vaults/tokenInfo/Read |  |
> | Microsoft. Recoveryservices/cofres/usos/leitura | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/Vaults/vaultTokens/Read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-contributor"></a>Colaborador da conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite gerenciar âncoras espaciais em sua conta, mas não excluí-las |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/criar/ação | Criar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/descoberta/leitura | Descobrir âncoras espaciais próximas |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/consulta/leitura | Localizar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-owner"></a>Proprietário da conta das âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie âncoras espaciais em sua conta, incluindo excluí-las |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/criar/ação | Criar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Excluir âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/descoberta/leitura | Descobrir âncoras espaciais próximas |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/consulta/leitura | Localizar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-reader"></a>Leitor de conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite localizar e ler propriedades de âncoras espaciais em sua conta |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/descoberta/leitura | Descobrir âncoras espaciais próximas |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/consulta/leitura | Localizar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-db-contributor"></a>Colaborador do BD SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie bancos de dados SQL, mas não tem acesso a eles. Além disso, você não pode gerenciar suas políticas relacionadas à segurança ou seus servidores SQL pai. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/servidores/bancos de dados/* | Criar e gerenciar bancos de dados SQL |
> | Microsoft. SQL/Servers/Read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. insights/métricas/leitura | Ler métricas |
> | Microsoft. insights/metricDefinitions/Read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servidores/bancos de dados/auditingPolicies/* | Editar políticas de auditoria |
> | Microsoft. SQL/servidores/bancos de dados/auditingSettings/* | Editar configurações de auditoria |
> | Microsoft. SQL/servidores/bancos de dados/auditRecords/leitura | Recuperar os registros de auditoria de blob de banco de dados |
> | Microsoft. SQL/servidores/bancos de dados/connectionPolicies/* | Editar políticas de conexão |
> | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/* | Editar políticas de mascaramento de dados |
> | Microsoft. SQL/servidores/bancos de dados/extendedAuditingSettings/* |  |
> | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/securityAlertPolicies/* | Editar políticas de alerta de segurança |
> | Microsoft. SQL/servidores/bancos de dados/securityMetrics/* | Editar métricas de segurança |
> | Microsoft. SQL/servidores/bancos de dados/sensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/Servers/vulnerabilityAssessments/* |  |
> | **Dataactions** |  |
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
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/managedInstances/* |  |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Network/virtualNetworks/sub-redes/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. insights/métricas/leitura | Ler métricas |
> | Microsoft. insights/metricDefinitions/Read | Ler definições de métrica |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-security-manager"></a>Gerenciador de segurança do SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acesso a eles. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler a autorização da Microsoft |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/Action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. SQL/managedInstances/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/transparentDataEncryption/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/Servers/auditingPolicies/* | Criar e gerenciar políticas de auditoria do SQL Server |
> | Microsoft. SQL/Servers/auditingSettings/* | Criar e gerenciar a configuração de auditoria do SQL Server |
> | Microsoft. SQL/Servers/extendedAuditingSettings/Read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Microsoft. SQL/servidores/bancos de dados/auditingPolicies/* | Criar e gerenciar políticas de auditoria de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/auditRecords/leitura | Ler registros de auditoria |
> | Microsoft. SQL/servidores/bancos de dados/connectionPolicies/* | Criar e gerenciar políticas de conexão de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/* | Criar e gerenciar as políticas de mascaramento de dados do SQL Server Database |
> | Microsoft. SQL/servidores/bancos de dados/extendedAuditingSettings/leitura | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Microsoft. SQL/servidores/bancos de dados/leitura | Retornar a lista de bancos de dados ou obter as propriedades do banco de dados especificado. |
> | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/esquemas/leitura | Obtenha um esquema de banco de dados. |
> | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/leitura | Obter uma coluna de banco de dados. |
> | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/leitura | Obter uma tabela de banco de dados. |
> | Microsoft. SQL/servidores/bancos de dados/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/securityMetrics/* | Criar e gerenciar métricas de segurança de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/sensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/transparentDataEncryption/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/Servers/firewallRules/* |  |
> | Microsoft. SQL/Servers/Read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft. SQL/Servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do SQL Server |
> | Microsoft. SQL/Servers/vulnerabilityAssessments/* |  |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-server-contributor"></a>Colaborador de SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite gerenciar servidores e bancos de dados SQL, mas não acessá-los, nem suas políticas relacionadas à segurança. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/Servers/* | Criar e gerenciar servidores SQL |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. insights/métricas/leitura | Ler métricas |
> | Microsoft. insights/metricDefinitions/Read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/Servers/auditingPolicies/* | Editar políticas de auditoria do SQL Server |
> | Microsoft. SQL/Servers/auditingSettings/* | Editar configurações de auditoria do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/auditingPolicies/* | Editar políticas de auditoria de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/auditingSettings/* | Editar configurações de auditoria do banco de dados SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/auditRecords/leitura | Ler registros de auditoria |
> | Microsoft. SQL/servidores/bancos de dados/connectionPolicies/* | Editar políticas de conexão de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/* | Editar as políticas de mascaramento de dados do SQL Server Database |
> | Microsoft. SQL/servidores/bancos de dados/extendedAuditingSettings/* |  |
> | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/securityAlertPolicies/* | Editar políticas de alerta de segurança do banco de dados SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/securityMetrics/* | Editar métricas de segurança de banco de dados do SQL Server |
> | Microsoft. SQL/servidores/bancos de dados/sensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/Servers/extendedAuditingSettings/* |  |
> | Microsoft. SQL/Servers/securityAlertPolicies/* | Editar políticas de alerta de segurança do SQL Server |
> | Microsoft. SQL/Servers/vulnerabilityAssessments/* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-account-contributor"></a>Colaborador da conta de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler todas as autorizações |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. insights/diagnosticSettings/* | Gerenciar configurações de diagnóstico |
> | Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/Action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. Storage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-account-key-operator-service-role"></a>Função de serviço do operador chave da conta de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite listar e regenerar chaves de acesso da conta de armazenamento. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/listkeys/Action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Microsoft. Storage/storageAccounts/regeneratekey/Action | Regenera as chaves de acesso para a conta de armazenamento especificada. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-contributor"></a>Colaborador de dados do blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler, gravar e excluir contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/blobservices/containers/Delete | Excluir um contêiner. |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/leitura | Retornar um contêiner ou uma lista de contêineres. |
> | Microsoft. Storage/storageAccounts/blobservices/containers/Write | Modifique os metadados ou as propriedades de um contêiner. |
> | Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey/Action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/excluir | Excluir um blob. |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/leitura | Retornar um BLOB ou uma lista de BLOBs. |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/gravação | Gravar em um blob. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-owner"></a>Proprietário de dados do blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Fornece acesso completo aos dados e contêineres de blob do armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/blobservices/containers/* | Permissões totais em contêineres. |
> | Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey/Action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/* | Permissões totais em BLOBs. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-reader"></a>Leitor de dados de blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia e liste contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/leitura | Retornar um contêiner ou uma lista de contêineres. |
> | Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey/Action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/leitura | Retornar um BLOB ou uma lista de BLOBs. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-delegator"></a>Delegador de blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Obtenha uma chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou BLOB que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [criar uma SAS de delegação de usuário](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey/Action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
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
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/leitura | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/gravação | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta. |
> | Microsoft. Storage/storageAccounts/fileservices/arquivos/compartilhamento/exclusão | Retorna o resultado da exclusão de um arquivo/pasta. |
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
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/leitura | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/gravação | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta. |
> | Microsoft. Storage/storageAccounts/fileservices/arquivos/compartilhamento/exclusão | Retorna o resultado da exclusão de um arquivo/pasta. |
> | Microsoft. Storage/storageAccounts/fileservices/arquivo compartilhado/arquivos/modifypermissions/Action | Retorna o resultado da permissão de modificação em um arquivo/pasta. |
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
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/leitura | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-contributor"></a>Colaborador de dados da fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler, gravar e excluir filas do armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/queueservices/filas/excluir | Excluir uma fila. |
> | Microsoft. Storage/storageAccounts/queueservices/filas/leitura | Retornar uma fila ou uma lista de filas. |
> | Microsoft. Storage/storageAccounts/queueservices/filas/gravação | Modificar metadados ou propriedades da fila. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/excluir | Excluir uma ou mais mensagens de uma fila. |
> | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/leitura | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/gravação | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-message-processor"></a>Processador de mensagens de dados da fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Espiar, recuperar e excluir uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/leitura | Inspecionar uma mensagem. |
> | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/processo/ação | Recuperar e excluir uma mensagem. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-message-sender"></a>Remetente da mensagem de dados da fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Adicionar mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Ações** |  |
> | *None* |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/Adicionar/ação | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-reader"></a>Leitor de dados da fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia e liste as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Ações** |  |
> | Microsoft. Storage/storageAccounts/queueservices/filas/leitura | Retorna uma fila ou uma lista de filas. |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/leitura | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="support-request-contributor"></a>Colaborador de solicitação de suporte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite criar e gerenciar solicitações de suporte |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="traffic-manager-contributor"></a>Colaborador do Gerenciador de tráfego
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie perfis do Gerenciador de tráfego, mas não permite que você controle quem tem acesso a eles. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Ler funções e atribuições de função |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Network/trafficManagerProfiles/* |  |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="user-access-administrator"></a>Administrador de Acesso de Utilizador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie o acesso do usuário aos recursos do Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Ações** |  |
> | */read | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. Authorization/* | Gerenciar autorização |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-administrator-login"></a>Logon de administrador da máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Exibir máquinas virtuais no portal e fazer logon como administrador |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Ações** |  |
> | Microsoft. Network/publicIPAddresses/Read | Obtém uma definição de endereço IP público. |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Network/balancers/Read | Obter uma definição de balanceador de carga |
> | Microsoft. Network/networkInterfaces/Read | Obtém uma definição de interface de rede.  |
> | Microsoft. Compute/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Compute/virtualMachines/logon/ação | Fazer logon em uma máquina virtual como um usuário normal |
> | Microsoft. Compute/virtualMachines/loginAsAdmin/Action | Fazer logon em uma máquina virtual com privilégios de administrador do Windows ou de usuário raiz do Linux |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-contributor"></a>Colaborador da máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie máquinas virtuais, mas não tem acesso a elas, e não à rede virtual ou à conta de armazenamento à qual elas estão conectadas. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. Compute/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
> | Microsoft. Compute/Locations/* | Criar e gerenciar locais de computação |
> | Microsoft. Compute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft. Compute/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de dimensionamento de máquinas virtuais |
> | Microsoft. DevTestLab/Schedules/* |  |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. Network/applicationGateways/backendAddressPools/junção/ação | Une um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | Microsoft. Network/balancers/backendAddressPools/junção/ação | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft. Network/balancers/inboundNatPools/junção/ação | Une um pool de NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft. Network/balancers/inboundNatRules/junção/ação | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft. Network/balancers/investigações/junção/ação | Permite o uso de investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode fazer referência à investigação. Não é possível alertá-lo. |
> | Microsoft. Network/balancers/Read | Obter uma definição de balanceador de carga |
> | Microsoft. Network/Locations/* | Criar e gerenciar locais de rede |
> | Microsoft. Network/networkInterfaces/* | Criar e gerenciar interfaces de rede |
> | Microsoft. Network/networkSecurityGroups/junção/ação | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft. Network/networkSecurityGroups/Read | Obter uma definição de grupo de segurança de rede |
> | Microsoft. Network/publicIPAddresses/junção/ação | Une um endereço IP público. Não é possível alertá-lo. |
> | Microsoft. Network/publicIPAddresses/Read | Obtém uma definição de endereço IP público. |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Network/virtualNetworks/sub-redes/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft. Recoveryservices/Locations/* |  |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/backupProtectionIntent/Write | Criar uma intenção de proteção de backup |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Retorna os detalhes do objeto do item protegido |
> | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Criar um item protegido de backup |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/Read | Retorna todas as políticas de proteção |
> | Microsoft. Recoveryservices/Vaults/backupPolicies/Write | Criar política de proteção |
> | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Microsoft. Recoveryservices/cofres/usos/leitura | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | Microsoft. Recoveryservices/cofres/gravação | A operação criar cofre cria um recurso do Azure do tipo ' cofre ' |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. SqlVirtualMachine/* |  |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-user-login"></a>Logon de usuário da máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Exiba as máquinas virtuais no portal e faça logon como um usuário normal. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Ações** |  |
> | Microsoft. Network/publicIPAddresses/Read | Obtém uma definição de endereço IP público. |
> | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Microsoft. Network/balancers/Read | Obter uma definição de balanceador de carga |
> | Microsoft. Network/networkInterfaces/Read | Obtém uma definição de interface de rede.  |
> | Microsoft. Compute/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | Microsoft. Compute/virtualMachines/logon/ação | Fazer logon em uma máquina virtual como um usuário normal |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="web-plan-contributor"></a>Colaborador do plano da Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie os planos da Web para sites, mas não acesso a eles. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Web/serverFarms/* | Criar e gerenciar farms de servidores |
> | Microsoft. Web/hostingEnvironments/junção/ação | Une um Ambiente do Serviço de Aplicativo |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="website-contributor"></a>Colaborador do site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite que você gerencie sites (não planos da Web), mas não tem acesso a eles. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Ações** |  |
> | Microsoft. Authorization/*/Read | Leitura de autorização |
> | Microsoft. insights/alertRules/* | Criar e gerenciar regras de alerta do insights |
> | Microsoft. insights/Components/* | Criar e gerenciar componentes do insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Microsoft. Resources/Implantations/* | Criar e gerenciar implantações de grupo de recursos |
> | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Microsoft. support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft. Web/Certificates/* | Criar e gerenciar certificados de site |
> | Microsoft. Web/listSitesAssignedToHostName/leitura | Obter nomes de sites atribuídos ao nome do host. |
> | Microsoft. Web/serverFarms/junção/ação |  |
> | Microsoft. Web/serverFarms/leitura | Obter as propriedades em um plano do serviço de aplicativo |
> | Microsoft. Web/sites/* | Criar e gerenciar sites (a criação de site também requer permissões de gravação para o plano do serviço de aplicativo associado) |
> | **NotActions** |  |
> | *None* |  |
> | **Dataactions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="next-steps"></a>Passos seguintes

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [Permissions in Azure Security Center](../security-center/security-center-permissions.md) (Permissões no Centro de Segurança do Azure)
