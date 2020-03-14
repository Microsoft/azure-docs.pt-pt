---
title: Papéis azure embutidos para Azure RBAC
description: Este artigo descreve as funções azure incorporadas para o controlo de acesso baseado em funções azure (RBAC). Lista Ações, NãoAções, Ações de Dados e NotDataActions.
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
ms.date: 03/12/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 5b6da05f07636f6a6dde16cf6d8061629a72adfa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281109"
---
# <a name="azure-built-in-roles"></a>Papéis azure embutidos

O [controlo de acesso baseado em funções azure (RBAC)](overview.md) tem várias funções azure incorporadas que pode atribuir a utilizadores, grupos, diretores de serviçoe identidades geridas. As atribuições de funções são a forma como controlas o acesso aos recursos do Azure. Se as funções incorporadas não atenderem às necessidades específicas da sua organização, pode criar os seus próprios [papéis personalizados Azure.](custom-roles.md)

Este artigo enumera as funções incorporadas para os recursos Azure, que estão sempre a evoluir. Para obter as funções mais recentes, utilize a lista de definição de definição de funções [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [az](/cli/azure/role/definition#az-role-definition-list). Se procura funções de administrador para o Azure Ative Directory (Azure AD), consulte [permissões de funções de administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="descriptions-and-ids"></a>Descrições e IDs

A tabela seguinte fornece uma breve descrição e a identificação única de cada papel incorporado. Selecione o nome do papel para ver a lista de `Actions`, `NotActions`, `DataActions`e `NotDataActions` para cada papel. Para obter informações sobre o que estas ações significam e como se aplicam aos planos de gestão e dados, consulte Compreender as definições de [papéis para os recursos Do Azure.](role-definitions.md)


> [!div class="mx-tableFixed"]
> | Papel incorporado | Descrição | ID |
> | --- | --- | --- |
> | [AcrDelete](#acrdelete) | acr excluir | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | sinal de imagem acr | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr puxar | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr empurrar | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarentenaReader](#acrquarantinereader) | acr leitor de dados de quarentena | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarentenaEscritor](#acrquarantinewriter) | acr escritor de dados de quarentena | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Colaborador do Serviço de Gestão da API](#api-management-service-contributor) | Pode gerir o serviço e as APIs | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Papel do operador de serviço de gestão da API](#api-management-service-operator-role) | Pode gerir o serviço, mas não as APIs | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Papel de leitor de serviço de gestão da API](#api-management-service-reader-role) | Acesso apenas a leitura ao serviço e APIs | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Proprietário de dados de configuração de aplicativos](#app-configuration-data-owner) | Permite o acesso total aos dados de Configuração de Aplicações. | 5ae67d6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Leitor de dados de configuração de aplicativos](#app-configuration-data-reader) | Permite ler o acesso aos dados de Configuração de Aplicações. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Contribuidor de componentes de insights de aplicação](#application-insights-component-contributor) | Pode gerir componentes de Insights de Aplicação | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Insights de aplicação Snapshot Debugger](#application-insights-snapshot-debugger) | Dá permissão ao utilizador para visualizar e descarregar imagens de depuração recolhidas com o Debugger Snapshot Debugger da Aplicação Insights. Note que estas permissões não estão incluídas nas funções [de Proprietário](#owner) ou [Colaborador.](#contributor) | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Operador de trabalho de automação](#automation-job-operator) | Criar e Gerir Empregos utilizando os Runbooks automation. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operador de Automação](#automation-operator) | Operadores de Automação são capazes de iniciar, parar, suspender e retomar postos de trabalho | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operador de livro de automação](#automation-runbook-operator) | Leia as propriedades do Runbook - para ser capaz de criar Jobs do livro de corridas. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Colaborador avere](#avere-contributor) | Pode criar e gerir um cluster Avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operador de Avere](#avere-operator) | Usado pelo cluster Avere vFXT para gerir o cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Onboard de máquina conectada Azure](#azure-connected-machine-onboarding) | Pode a bordo de Máquinas Conectadas Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrador de recursos de máquina conectada Azure](#azure-connected-machine-resource-administrator) | Pode ler, escrever, excluir e reembarcar máquinas ligadas azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Proprietário de dados do Azure Event Hubs](#azure-event-hubs-data-owner) | Permite o acesso total aos recursos do Azure Event Hubs. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Recetor de dados do Azure Event Hubs](#azure-event-hubs-data-receiver) | Permite ter acesso aos recursos do Azure Event Hubs. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Remetente de dados do Azure Event Hubs](#azure-event-hubs-data-sender) | Permite o envio de acesso aos recursos do Azure Event Hubs. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Papel de administrador de cluster de serviço Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Lista de ação credencial de administração de cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Função de utilizador do cluster de serviço Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Enumerar a ação credencial do utilizador do cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Leitor de dados do Azure Maps (Pré-visualização)](#azure-maps-data-reader-preview) | Concede acesso a dados relacionados com mapas de leitura a partir de uma conta de mapas Azure. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Colaborador Azure Sentinel](#azure-sentinel-contributor) | Colaborador Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Leitor de Sentinela Azure](#azure-sentinel-reader) | Leitor de Sentinela Azure | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Resposta Sentinela Azure](#azure-sentinel-responder) | Resposta Sentinela Azure | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Proprietário de dados de ônibus de serviço Azure](#azure-service-bus-data-owner) | Permite o acesso total aos recursos do Azure Service Bus. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Recetor de dados de ônibus de serviço Azure](#azure-service-bus-data-receiver) | Permite ter acesso aos recursos do Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Remetente de dados de ônibus de serviço Azure](#azure-service-bus-data-sender) | Permite o envio de acesso aos recursos do Azure Service Bus. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Proprietário de registo de pilha azure](#azure-stack-registration-owner) | Permite-lhe gerir as inscrições do Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Colaborador de backup](#backup-contributor) | Permite-lhe gerir o serviço de backup, mas não pode criar cofres e dar acesso a outros | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operador de backup](#backup-operator) | Permite-lhe gerir serviços de backup, exceto remoção de backup, criação de cofre e acesso a outros | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Leitor de Backup](#backup-reader) | Pode ver serviços de backup, mas não pode fazer alterações | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Leitor de Faturação](#billing-reader) | Permite ler o acesso aos dados de faturação | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Colaborador biztalk](#biztalk-contributor) | Permite-lhe gerir os serviços bizTalk, mas não ter acesso a eles. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Acesso ao nó do membro blockchain (pré-visualização)](#blockchain-member-node-access-preview) | Permite o acesso aos nódosos do Membro blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | [Contribuinte de Plantas](#blueprint-contributor) | Pode gerir definições de plantas, mas não atribuí-las. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operador de plantas](#blueprint-operator) | Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. NOTA: isto só funciona se a atribuição for feita com uma identidade gerida atribuída pelo utilizador. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Colaborador de ponto final da CDN](#cdn-endpoint-contributor) | Pode gerir os pontos finais da CDN, mas não pode conceder acesso a outros utilizadores. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Leitor de ponto final CDN](#cdn-endpoint-reader) | Pode ver pontos finais de CDN, mas não pode fazer alterações. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Colaborador de perfil cdn](#cdn-profile-contributor) | Pode gerir perfis de CDN e seus pontos finais, mas não pode conceder acesso a outros utilizadores. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Leitor de perfil CDN](#cdn-profile-reader) | Pode ver perfis de CDN e seus pontos finais, mas não pode fazer alterações. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Contribuidor de Rede Clássica](#classic-network-contributor) | Permite-lhe gerir redes clássicas, mas não ter acesso a elas. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Contribuinte de Conta de Armazenamento Clássico](#classic-storage-account-contributor) | Permite-lhe gerir contas de armazenamento clássicas, mas não ter acesso a elas. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Função de serviço de serviço de chave de conta de armazenamento clássico](#classic-storage-account-key-operator-service-role) | Os operadores chave da conta de armazenamento clássico são autorizados a listar e regenerar chaves em Contas de Armazenamento Clássicos | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Colaborador de máquina virtual clássica](#classic-virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais clássicas, mas não ter acesso a elas, e não a rede virtual ou conta de armazenamento a que estão ligadas. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Colaborador de Serviços Cognitivos](#cognitive-services-contributor) | Permite-lhe criar, ler, atualizar, apagar e gerir chaves dos Serviços Cognitivos. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Leitor de Dados de Serviços Cognitivos (Pré-visualização)](#cognitive-services-data-reader-preview) | Permite-lhe ler os dados dos Serviços Cognitivos. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Utilizador de Serviços Cognitivos](#cognitive-services-user) | Permite-lhe ler e listar chaves dos Serviços Cognitivos. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | [Contribuidor](#contributor) | Permite-lhe gerir tudo, menos dar acesso aos recursos. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Cosmos DB Papel de leitor de conta](#cosmos-db-account-reader-role) | Pode ler os dados da conta Azure Cosmos DB. Consulte o Contribuinte de [Conta DocumentDB](#documentdb-account-contributor) para gerir as contas da Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operador DB cosmos](#cosmos-db-operator) | Permite-lhe gerir as contas da Azure Cosmos DB, mas não aceder aos dados. Impede o acesso às chaves de conta e às cordas de ligação. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Pode submeter pedido de restauro de uma base de dados Cosmos DB ou um recipiente para uma conta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Colaborador de Gestão de Custos](#cost-management-contributor) | Pode ver os custos e gerir a configuração de custos (por exemplo, orçamentos, exportações) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Leitor de Gestão de Custos](#cost-management-reader) | Pode ver os dados de custos e a configuração (por exemplo, orçamentos, exportações) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Contribuinte da Caixa de Dados](#data-box-contributor) | Permite-lhe gerir tudo ao abrigo do Serviço data box, exceto dar acesso a outros. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Leitor de caixas de dados](#data-box-reader) | Permite-lhe gerir o Serviço de Caixas de Dados, exceto criar detalhes de encomenda ou edição e dar acesso a outros. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Contribuinte da fábrica de dados](#data-factory-contributor) | Criar e gerir fábricas de dados, bem como recursos infantis dentro delas. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Desenvolvedor de análise de data lake](#data-lake-analytics-developer) | Permite-lhe submeter, monitorizar e gerir os seus próprios empregos, mas não criar ou apagar contas data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c888 |
> | [Purgador de dados](#data-purger) | Pode expurgar dados de análise | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Utilizador de Laboratórios DevTest](#devtest-labs-user) | Permite ligar, iniciar, reiniciar e desligar as suas máquinas virtuais nos seus Laboratórios Azure DevTest. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Contribuinte da Zona DNS](#dns-zone-contributor) | Permite-lhe gerir zonas dNS e recordes em DNS Azure, mas não permite controlar quem tem acesso a elas. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Contribuinte de Conta DocumentDB](#documentdb-account-contributor) | Pode gerir as contas da Azure Cosmos DB. Azure Cosmos DB é anteriormente conhecido como DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Colaborador de Subscrição de EventosGrid](#eventgrid-eventsubscription-contributor) | Permite-lhe gerir as operações de subscrição de eventos EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Leitor de Subscrição de EventosGrid](#eventgrid-eventsubscription-reader) | Permite-lhe ler as assinaturas do eventoGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Operador de cluster HDInsight](#hdinsight-cluster-operator) | Permite-lhe ler e modificar as configurações do cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Colaborador de Serviços de Domínio HDInsight](#hdinsight-domain-services-contributor) | Pode ler, criar, modificar e eliminar as operações relacionadas com os serviços de domínio necessários para o Pacote de Segurança Empresarial HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Contribuinte de Conta de Sistemas Inteligentes](#intelligent-systems-account-contributor) | Permite-lhe gerir as contas da Intelligent Systems, mas não ter acesso a elas. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Contribuinte chave do cofre](#key-vault-contributor) | Permite-lhe gerir cofres chave, mas não acesso a eles. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Criador de Laboratório](#lab-creator) | Permite-lhe criar, gerir, apagar os seus laboratórios geridos sob as suas Contas Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | [Log Analytics Contributor](#log-analytics-contributor) | O Log Analytics Contributor pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VMs; chaves da conta de armazenamento de leitura para poder configurar a recolha de registos do Armazenamento Azure; criação e configuração de contas de Automação; adicionando soluções; e configurar diagnósticos Azure em todos os recursos do Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Leitor de Análise de Log](#log-analytics-reader) | O Log Analytics Reader pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração dos diagnósticos do Azure em todos os recursos do Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Colaborador de aplicações lógicas](#logic-app-contributor) | Permite-lhe gerir aplicações lógicas, mas não alterar o acesso às mesmas. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operador de aplicações lógicas](#logic-app-operator) | Permite-lhe ler, ativar e desativar aplicações lógicas, mas não editá-las ou atualizá-las. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | [Função de colaborador de aplicação gerida](#managed-application-contributor-role) | Permite a criação de recursos de aplicação geridos. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Função do operador de aplicação gerida](#managed-application-operator-role) | Permite-lhe ler e realizar ações sobre recursos de Aplicação Geridas | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Leitor de Aplicações Geridas](#managed-applications-reader) | Permite-lhe ler recursos numa aplicação gerida e solicitar acesso ao JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Colaborador de Identidade Gerido](#managed-identity-contributor) | Criar, Ler, Atualizar e Eliminar identidade atribuída ao utilizador | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operador de Identidade Gerido](#managed-identity-operator) | Ler e Atribuir identidade atribuída ao utilizador | f1a07417-d97a-45cb-824c-7a7467783830 |
> | [Atribuição de registo de serviços geridos eliminar função](#managed-services-registration-assignment-delete-role) | Serviços geridos Atribuição de Registo De exclusão De função permite aos utilizadores de inquilinos gerentes apagar a atribuição de registo atribuída ao seu inquilino. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Colaborador do Grupo de Gestão](#management-group-contributor) | Papel de Colaborador do Grupo de Gestão | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Leitor de Grupo de Gestão](#management-group-reader) | Papel de leitor de grupo de gestão | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Colaborador de Monitorização](#monitoring-contributor) | Pode ler todos os dados de monitorização e editar definições de monitorização. Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles) | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Editor de Métricas de Monitorização](#monitoring-metrics-publisher) | Permite a publicação de métricas contra recursos Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Leitor de Monitorização](#monitoring-reader) | Pode ler todos os dados de monitorização (métricas, registos, etc.). Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles) | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Contribuidor de Rede](#network-contributor) | Permite-lhe gerir redes, mas não ter acesso a elas. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Novo contribuinte de conta APM da Relíquia](#new-relic-apm-account-contributor) | Permite-lhe gerir as contas e aplicações da New Relic Application Performance Management, mas não o acesso às mesmas. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Proprietário](#owner) | Permite-lhe gerir tudo, incluindo o acesso aos recursos. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Policy Insights Data Writer (Pré-visualização)](#policy-insights-data-writer-preview) | Permite ler o acesso às políticas de recursos e escrever acesso a eventos de política de componentes de recursos. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Leitor](#reader) | Permite-lhe ver tudo, mas não fazer alterações. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Acesso a Leitores e Dados](#reader-and-data-access) | Permite-lhe ver tudo, mas não permitirá que apague ou crie uma conta de armazenamento ou recursos contidos. Também permitirá o acesso de leitura/escrita a todos os dados contidos numa conta de armazenamento através do acesso às chaves da conta de armazenamento. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Redis Cache Contributor](#redis-cache-contributor) | Permite-lhe gerir caches Redis, mas não ter acesso a eles. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Contribuinte de Política de Recursos](#resource-policy-contributor) | Utilizadores com direitos de criar/modificar a política de recursos, criar bilhetes de apoio e ler recursos/hierarquia. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Colaborador de Coleções de Emprego do Scheduler](#scheduler-job-collections-contributor) | Permite-lhe gerir as coleções de emprego do Scheduler, mas não ter acesso a elas. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Colaborador de Serviço de Pesquisa](#search-service-contributor) | Permite-lhe gerir os serviços de pesquisa, mas não aceder aos mesmos. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Administrador de Segurança](#security-admin) | Pode ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, descartar alertas e recomendações. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Contribuinte de Avaliação de Segurança](#security-assessment-contributor) | Permite-lhe levar avaliações para o Centro de Segurança | 612c2a1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gestor de Segurança (Legado)](#security-manager-legacy) | Este é um papel legado. Por favor, use o Administrador de Segurança em vez disso. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Leitor de Segurança](#security-reader) | Pode ver recomendações e alertas, ver políticas de segurança, ver estados de segurança, mas não pode fazer alterações. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | [Colaborador de Recuperação do Site](#site-recovery-contributor) | Permite-lhe gerir o serviço de recuperação do site, exceto criação de cofre e atribuição de papéis | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operador de recuperação do site](#site-recovery-operator) | Permite-lhe falhar e falhar, mas não realizar outras operações de gestão de recuperação do site | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Leitor de Recuperação do Site](#site-recovery-reader) | Permite-lhe visualizar o estado de Recuperação do Site, mas não realizar outras operações de gestão | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Contribuinte da conta de âncoras espaciais](#spatial-anchors-account-contributor) | Permite-lhe gerir âncoras espaciais na sua conta, mas não eliminá-las | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Proprietário da conta de âncoras espaciais](#spatial-anchors-account-owner) | Permite-lhe gerir âncoras espaciais na sua conta, incluindo apagando-as | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Leitor de conta de âncoras espaciais](#spatial-anchors-account-reader) | Permite localizar e ler propriedades de âncoras espaciais na sua conta | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | [Contribuinte SQL DB](#sql-db-contributor) | Permite-lhe gerir as bases de dados SQL, mas não ter acesso a elas. Além disso, não é possível gerir as suas políticas relacionadas com a segurança ou os servidores SQL dos seus pais. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Contribuinte de instância gerida SQL](#sql-managed-instance-contributor) | Permite-lhe gerir instâncias geridas pelo SQL e configurar a rede necessária, mas não pode dar acesso a outros. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gestor de Segurança SQL](#sql-security-manager) | Permite-lhe gerir as políticas relacionadas com a segurança dos servidores e bases de dados SQL, mas não o acesso aos mesmos. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Contribuinte do Servidor SQL](#sql-server-contributor) | Permite-lhe gerir servidores e bases de dados SQL, mas não aceder aos mesmos, e não às suas políticas relacionadas com a segurança. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | [Contribuidor de Conta de Armazenamento](#storage-account-contributor) | Permite a gestão de contas de armazenamento. Fornece acesso à chave da conta, que pode ser usada para aceder a dados através de autorização de chave partilhada. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Função de serviço de serviço de operador chave de conta de armazenamento](#storage-account-key-operator-service-role) | Permite a listagem e regeneração das chaves de acesso à conta de armazenamento. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Contribuinte de dados blob de armazenamento](#storage-blob-data-contributor) | Leia, escreva e elimine os recipientes de armazenamento e bolhas do Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Proprietário de dados blob de armazenamento](#storage-blob-data-owner) | Fornece acesso total aos recipientes e dados de blob de armazenamento Azure, incluindo a atribuição de controlo de acesso POSIX. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Leitor de dados blob de armazenamento](#storage-blob-data-reader) | Leia e enumere os recipientes de armazenamento e bolhas azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Depósito Blob Delegator](#storage-blob-delegator) | Obtenha uma chave de delegação de utilizadores, que pode ser usada para criar uma assinatura de acesso partilhado para um recipiente ou bolha que é assinada com credenciais Azure AD. Para mais informações, consulte [Criar uma delegação de utilizadores SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Colaborador de partilha de dados de ficheiros de armazenamento SMB Share](#storage-file-data-smb-share-contributor) | Permite ler, escrever e excluir o acesso a ficheiros/diretórios em ações de ficheiros Azure. Esta função não tem equivalente incorporado nos servidores de ficheiros do Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Relatório de armazenamento Dados SMB Share Colaborador Elevado](#storage-file-data-smb-share-elevated-contributor) | Permite ler, escrever, excluir e modificar ACLs em ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de alteração nos servidores de ficheiros do Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Armazenamento Arquivo Data SMB Share Reader](#storage-file-data-smb-share-reader) | Permite o acesso a ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de leitura nos servidores de ficheiros do Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Contribuinte de dados da fila de armazenamento](#storage-queue-data-contributor) | Leia, escreva e elimine as filas de armazenamento do Azure e as mensagens de fila. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processador de dados de fila de armazenamento](#storage-queue-data-message-processor) | Espreite, recupere e apague uma mensagem de uma fila de Armazenamento Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Remetente de mensagem de dados de fila de armazenamento](#storage-queue-data-message-sender) | Adicione mensagens a uma fila de Armazenamento Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Leitor de dados da fila de armazenamento](#storage-queue-data-reader) | Leia e enumere as filas de armazenamento azure e as mensagens de fila. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . | 19e7f393-937e-4f77-808e-94535e297925 |
> | [Colaborador de Pedido de Apoio](#support-request-contributor) | Permite-lhe criar e gerir pedidos de suporte | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Colaborador do Gestor de Tráfego](#traffic-manager-contributor) | Permite-lhe gerir os perfis do Traffic Manager, mas não permite controlar quem tem acesso aos mesmos. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | [Administrador de Acesso de Utilizador](#user-access-administrator) | Permite-lhe gerir o acesso dos utilizadores aos recursos do Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | [Login do administrador de máquina virtual](#virtual-machine-administrator-login) | Ver Máquinas Virtuais no portal e iniciar sessão como administrador | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Contribuidor de Máquina Virtual](#virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais, mas não aceder a elas, e não a rede virtual ou a conta de armazenamento a que estão ligadas. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Login de utilizador de máquina virtual](#virtual-machine-user-login) | Ver Máquinas Virtuais no portal e iniciar sessão como um utilizador regular. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | [Colaborador do Plano Web](#web-plan-contributor) | Permite-lhe gerir os planos web para websites, mas não ter acesso aos mesmos. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Colaborador do Site](#website-contributor) | Permite-lhe gerir websites (não planos web), mas não ter acesso aos mesmos. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | [Contribuinte do livro](#workbook-contributor) | Pode salvar livros partilhados. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Leitor de livro](#workbook-reader) | Pode ler livros. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |


## <a name="general"></a>Geral


### <a name="contributor"></a>Contribuinte

Permite-lhe gerir tudo, menos dar acesso aos recursos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | * | Criar e gerir recursos de todos os tipos |
> | **NotAções** |  |
> | Microsoft.Authorization/*/Delete | Eliminar funções, atribuições políticas, definições de políticas e definições de definições de definições de definições de políticas |
> | Microsoft.Authorization/*/Write | Criar funções, atribuições de papéis, atribuições políticas, definições de políticas e definições de definições de definições de definições de definições de políticas |
> | Microsoft.Authorization/elevateAccess/Action | Concede ao utilizador o acesso ao administrador de acesso ao utilizador no âmbito do arrendatário |
> | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar quaisquer atribuições de plantas |
> | Microsoft.Blueprint/blueprintAssignments/delete | Eliminar quaisquer atribuições de plantas |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Proprietário

Permite-lhe gerir tudo, incluindo o acesso aos recursos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | * | Criar e gerir recursos de todos os tipos |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Leitor

Permite-lhe ver tudo, mas não fazer alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Administrador de Acesso de Utilizador

Permite-lhe gerir o acesso dos utilizadores aos recursos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Authorization/* | Gerir a autorização |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + machine learning


### <a name="cognitive-services-contributor"></a>Colaborador de Serviços Cognitivos

Permite-lhe criar, ler, atualizar, apagar e gerir chaves dos Serviços Cognitivos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Obtém as características de uma subscrição. |
> | Microsoft.Features/providers/features/read | Obtém a funcionalidade de uma subscrição num determinado fornecedor de recursos. |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | Microsoft.Insights/logDefinitions/read | Ler definições de registo |
> | Microsoft.Insights/metricdefinitions/read | Ler definições métricas |
> | Microsoft.Insights/metrics/read | Leia as métricas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenha os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Leitor de Dados de Serviços Cognitivos (Pré-visualização)

Permite-lhe ler os dados dos Serviços Cognitivos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Utilizador de Serviços Cognitivos

Permite-lhe ler e listar chaves dos Serviços Cognitivos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Lista de Chaves |
> | Microsoft.Insights/alertRules/read | Leia um alerta métrico clássico |
> | Microsoft.Insights/diagnosticSettings/read | Leia uma definição de diagnóstico de recursos |
> | Microsoft.Insights/logDefinitions/read | Ler definições de registo |
> | Microsoft.Insights/metricdefinitions/read | Ler definições métricas |
> | Microsoft.Insights/metrics/read | Leia as métricas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenha os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Análise


### <a name="azure-event-hubs-data-owner"></a>Proprietário de dados do Azure Event Hubs

Permite o acesso total aos recursos do Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.EventHub/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Recetor de dados do Azure Event Hubs

Permite ter acesso aos recursos do Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Remetente de dados do Azure Event Hubs

Permite o envio de acesso aos recursos do Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Contribuinte da fábrica de dados

Criar e gerir fábricas de dados, bem como recursos infantis dentro delas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.DataFactory/dataFactories/* | Criar e gerir fábricas de dados e recursos infantis dentro delas. |
> | Microsoft.DataFactory/factories/* | Criar e gerir fábricas de dados e recursos infantis dentro delas. |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventoSubscrição |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Purgador de dados

Pode expurgar dados de análise

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Dados purgados de Insights de Aplicação |
> | Microsoft.OperationalInsights/workspaces/*/read | Ver dados de análise de registo |
> | Microsoft.OperationalInsights/workspaces/purge/action | Eliminar dados especificados do espaço de trabalho |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Operador de cluster HDInsight

Permite-lhe ler e modificar as configurações do cluster HDInsight.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obtenha definições de gateway para Cluster HDInsight |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Atualizar definições de gateway para Cluster HDInsight |
> | Microsoft.HDInsight/clusters/configurações/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Colaborador de Serviços de Domínio HDInsight

Pode ler, criar, modificar e eliminar as operações relacionadas com os serviços de domínio necessários para o Pacote de Segurança Empresarial HDInsight

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics

O Log Analytics Contributor pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VMs; chaves da conta de armazenamento de leitura para poder configurar a recolha de registos do Armazenamento Azure; criação e configuração de contas de Automação; adicionando soluções; e configurar diagnósticos Azure em todos os recursos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso das contas de armazenamento. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

O Log Analytics Reader pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração dos diagnósticos do Azure em todos os recursos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Procure usando um motor novo. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves partilhadas para o espaço de trabalho. Estas teclas são usadas para ligar os agentes da Microsoft Operational Insights ao espaço de trabalho. |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Acesso ao nó do membro blockchain (pré-visualização)

Permite o acesso aos nódosos do Membro blockchain

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista o nó de transação do membro blockchain existente. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Liga-se a um nó de transações do Membro blockchain. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Computação


### <a name="classic-virtual-machine-contributor"></a>Colaborador de máquina virtual clássica

Permite-lhe gerir máquinas virtuais clássicas, mas não ter acesso a elas, e não a rede virtual ou conta de armazenamento a que estão ligadas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ClassicCompute/domainNames/* | Criar e gerir nomes clássicos de domínio computacional |
> | Microsoft.ClassicCompute/virtualMachines/* | Criar e gerir máquinas virtuais |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Link um Ip reservado |
> | Microsoft.ClassicNetwork/reservedIps/read | Recebe os Ips reservados |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Junta-se à rede virtual. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Pegue a rede virtual. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Devolve o disco da conta de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Devolve a imagem da conta de armazenamento. (Depreciado. Utilize 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devolva a conta de armazenamento com a conta dada. |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Login do administrador de máquina virtual

Ver Máquinas Virtuais no portal e iniciar sessão como administrador

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obtém uma definição de equilibrador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Compute/virtualMachines/login/action | Inicie sessão numa máquina virtual como utilizador regular |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Inicie sessão numa máquina virtual com administrador do Windows ou privilégios de utilizador raiz linux |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Colaborador de Máquina Virtual

Permite-lhe gerir máquinas virtuais, mas não aceder a elas, e não a rede virtual ou a conta de armazenamento a que estão ligadas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Compute/availabilitySets/* | Criar e gerir conjuntos de disponibilidade de cálculo |
> | Microsoft.Compute/locations/* | Criar e gerir localizações computadas |
> | Microsoft.Compute/virtualMachines/* | Criar e gerir máquinas virtuais |
> | Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerir conjuntos de escala de máquinas virtuais |
> | Microsoft.Compute/disks/write | Cria um novo Disco ou atualiza um existente |
> | Microsoft.Compute/disks/read | Obtenha as propriedades de um Disco |
> | Microsoft.Compute/disks/delete | Elimina o Disco |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Junta-se a uma piscina de endereço sinuoso de entrada de aplicação. Não alertável. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Junta-se a uma piscina de endereço sapateado de equilíbrio de carga. Não alertável. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Junta-se a uma piscina NAT de entrada de equilibrista de carga. Não é alertável. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Junta-se a uma regra de entrada de nat do equilibrador de carga. Não alertável. |
> | Microsoft.Network/loadBalancers/probes/join/action | Permite a utilização de sondas de um equilibrador de carga. Por exemplo, com esta permissão healthHealthA propriedade de conjunto de escala VM pode fazer referência à sonda. Não é alertável. |
> | Microsoft.Network/loadBalancers/read | Obtém uma definição de equilibrador de carga |
> | Microsoft.Network/locations/* | Criar e gerir localizações de rede |
> | Microsoft.Network/networkInterfaces/* | Criar e gerir interfaces de rede |
> | Microsoft.Network/networkSecurityGroups/join/action | Junta-se a um grupo de segurança da rede. Não alertável. |
> | Microsoft.Network/networkSecurityGroups/read | Obtém uma definição de grupo de segurança de rede |
> | Microsoft.Network/publicIPAddresses/join/action | Junta-se a um endereço IP público. Não alertável. |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não alertável. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de reserva |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de reserva |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as Políticas de Proteção |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Cria política de proteção |
> | Microsoft.RecoveryServices/Vaults/read | A operação Get Vault recebe um objeto que representa o recurso Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização para um cofre de serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/write | Create Vault operation cria um recurso Azure de tipo 'cofre' |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Login de utilizador de máquina virtual

Ver Máquinas Virtuais no portal e iniciar sessão como um utilizador regular.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obtém uma definição de equilibrador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Compute/virtualMachines/login/action | Inicie sessão numa máquina virtual como utilizador regular |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Contentores


### <a name="acrdelete"></a>AcrDelete

acr excluir

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Apagar artefactos num registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

sinal de imagem acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Empurre/Puxe os metadados fidedignos de conteúdo para um registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr puxar

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Puxe ou obtenha imagens de um registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr empurrar

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Puxe ou obtenha imagens de um registo de contentores. |
> | Microsoft.ContainerRegistry/registries/push/write | Empurre ou escreva imagens para um registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

acr leitor de dados de quarentena

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registros/quarentena/read | Puxe ou obtenha imagens de quarentena do registo de contentores |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarentenaEscritor

acr escritor de dados de quarentena

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registros/quarentena/read | Puxe ou obtenha imagens de quarentena do registo de contentores |
> | Microsoft.ContainerRegistry/registros/quarentena/write | Escreva/Modifique o estado de quarentena das imagens em quarentena |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Papel de administrador de cluster de serviço Azure Kubernetes

Lista de ação credencial de administração de cluster.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Enumerar a credencial clusterAdmin de um cluster gerido |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtenha um perfil de acesso de cluster gerido por nome de papel usando credencial de lista |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Função de utilizador do cluster de serviço Azure Kubernetes

Enumerar a ação credencial do utilizador do cluster.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Enumerar a credencial do clusterUser de um cluster gerido |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Bases de Dados


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB Papel de leitor de conta

Pode ler os dados da conta Azure Cosmos DB. Consulte o Contribuinte de [Conta DocumentDB](#documentdb-account-contributor) para gerir as contas da Azure Cosmos DB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.DocumentDB/*/read | Leia qualquer coleção |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lê a conta da base de dados apenas com as chaves. |
> | Microsoft.Insights/MetricDefinitions/read | Ler definições métricas |
> | Microsoft.Insights/Metrics/read | Leia as métricas |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Operador DB cosmos

Permite-lhe gerir as contas da Azure Cosmos DB, mas não aceder aos dados. Impede o acesso às chaves de conta e às cordas de ligação.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma subnet. Não é alertável. |
> | **NotAções** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Pode submeter pedido de restauro de uma base de dados Cosmos DB ou um recipiente para uma conta

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Enviar um pedido para configurar backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Submeter um pedido de restauro |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Contribuinte de Conta DocumentDB

Pode gerir as contas da Azure Cosmos DB. Azure Cosmos DB é anteriormente conhecido como DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.DocumentDb/databaseAccounts/* | Criar e gerir contas da Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma subnet. Não é alertável. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache Contributor

Permite-lhe gerir caches Redis, mas não ter acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Cache/redis/* | Criar e gerir caches Redis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Contribuinte SQL DB

Permite-lhe gerir as bases de dados SQL, mas não ter acesso a elas. Além disso, não é possível gerir as suas políticas relacionadas com a segurança ou os servidores SQL dos seus pais.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Criar e gerir bases de dados SQL |
> | Microsoft.Sql/servers/read | Devolva a lista de servidores ou obtenha as propriedades para o servidor especificado. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Insights/metrics/read | Leia as métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições métricas |
> | **NotAções** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar definições de auditoria |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registos de auditoria blob da base de dados |
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
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Contribuinte de instância gerida SQL

Permite-lhe gerir instâncias geridas pelo SQL e configurar a rede necessária, mas não pode dar acesso a outros.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/metrics/read | Leia as métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições métricas |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Gestor de Segurança SQL

Permite-lhe gerir as políticas relacionadas com a segurança dos servidores e bases de dados SQL, mas não o acesso aos mesmos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma subnet. Não é alertável. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/bases de dados/transparenteDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Criar e gerir políticas de auditoria de servidores SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Criar e gerir a definição de auditoria do servidor SQL |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor alargado configurada num determinado servidor |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerir políticas de auditoria da base de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerir as definições de auditoria da base de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registos de auditoria blob da base de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerir políticas de ligação à base de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerir políticas de máscara de dados de dados de servidores SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob alargada configurada numa determinada base de dados |
> | Microsoft.Sql/servers/databases/read | Devolva a lista de bases de dados ou obtenha as propriedades para a base de dados especificada. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Arranja um esquema de base de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Arranja uma coluna de base de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Arranja uma mesa de base de dados. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerir políticas de alerta de segurança de base de dados de servidores SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerir métricas de segurança da base de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servidores/bases de dados/TransparenteDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Devolva a lista de servidores ou obtenha as propriedades para o servidor especificado. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerir as políticas de alerta de segurança do servidor SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Contribuinte do Servidor SQL

Permite-lhe gerir servidores e bases de dados SQL, mas não aceder aos mesmos, e não às suas políticas relacionadas com a segurança.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Criar e gerir servidores SQL |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Insights/metrics/read | Leia as métricas |
> | Microsoft.Insights/metricDefinitions/read | Ler definições métricas |
> | **NotAções** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Editar políticas de auditoria de servidores SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Editar as definições de auditoria do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria da base de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar definições de auditoria de bases de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registos de auditoria blob da base de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar políticas de ligação à base de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento de dados de dados de servidores SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança de base de dados de servidores SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métricas de segurança da base de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Editar políticas de alerta de segurança do servidor SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="developer-tools"></a>Ferramentas de programação


### <a name="app-configuration-data-owner"></a>Proprietário de dados de configuração de aplicativos

Permite o acesso total aos dados de Configuração de Aplicações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.AppConfiguration/configuraçãoStores/*/read |  |
> | Microsoft.AppConfiguration/configuraçãoStores/*/write |  |
> | Microsoft.AppConfiguration/ConfiguraçãoStores/*/delete |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Leitor de dados de configuração de aplicativos

Permite ler o acesso aos dados de Configuração de Aplicações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.AppConfiguration/configuraçãoStores/*/read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Criador de Laboratório

Permite-lhe criar, gerir, apagar os seus laboratórios geridos sob as suas Contas Azure Lab.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crie um laboratório numa conta de laboratório. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Obtenha informações de disponibilidade regional para cada categoria de tamanho configurado sob uma conta de laboratório |
> | Microsoft.LabServices/labAccounts/getPricingAndAvailability/action | Obtenha os preços e disponibilidade de combinações de tamanhos, geografias e sistemas operativos para a conta de laboratório. |
> | Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action | Obtenha restrições de núcleo e utilização para esta subscrição |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Utilizador de Laboratórios DevTest

Permite ligar, iniciar, reiniciar e desligar as suas máquinas virtuais nos seus Laboratórios Azure DevTest.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Compute/availabilitySets/read | Obtenha as propriedades de um conjunto de disponibilidade |
> | Microsoft.Compute/virtualMachines/*/read | Leia as propriedades de uma máquina virtual (tamanhos VM, estado de execução, extensões VM, etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Powers off the virtual machine and releases the compute resources |
> | Microsoft.Compute/virtualMachines/read | Obtenha as propriedades de uma máquina virtual |
> | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Microsoft.Compute/virtualMachines/start/action | Começa a máquina virtual |
> | Microsoft.DevTestLab/*/read | Leia as propriedades de um laboratório |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Reclame uma máquina virtual aleatória no laboratório. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Criar máquinas virtuais num laboratório. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Certifique-se de que o utilizador atual tem um perfil válido no laboratório. |
> | Microsoft.DevTestLab/labs/formulas/delete | Apagar fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/write | Adicione ou modifique fórmulas. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avalia a política do laboratório. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Apropriar-se de uma máquina virtual existente |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Lista os horários de início/paragem aplicáveis, caso seja. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Obtém uma cadeia que representa o conteúdo do ficheiro RDP para a máquina virtual |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Junta-se a uma piscina de endereço sapateado de equilíbrio de carga. Não alertável. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Junta-se a uma regra de entrada de nat do equilibrador de carga. Não alertável. |
> | Microsoft.Network/networkInterfaces/*/read | Leia as propriedades de uma interface de rede (por exemplo, todos os equilibradores de carga de que a interface de rede faz parte) |
> | Microsoft.Network/networkInterfaces/join/action | Junta uma Máquina Virtual a uma interface de rede. Não alertável. |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Leia as propriedades de um endereço IP público |
> | Microsoft.Network/publicIPAddresses/join/action | Junta-se a um endereço IP público. Não alertável. |
> | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não alertável. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Microsoft.Resources/deployments/read | Obtém ou lista destacamentos. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | **NotAções** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Listas de tamanhos disponíveis a máquina virtual pode ser atualizada para |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="hybrid"></a>Híbrido


### <a name="azure-connected-machine-onboarding"></a>Onboard de máquina conectada Azure

Pode a bordo de Máquinas Conectadas Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.HybridCompute/machines/read | Leia quaisquer máquinas Azure Arc |
> | Microsoft.HybridCompute/machines/write | Escreve uma máquina Azure Arc |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obtenha a atribuição de configuração de hóspedes. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Administrador de recursos de máquina conectada Azure

Pode ler, escrever, excluir e reembarcar máquinas ligadas azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.HybridCompute/machines/read | Leia quaisquer máquinas Azure Arc |
> | Microsoft.HybridCompute/machines/write | Escreve uma máquina Azure Arc |
> | Microsoft.HybridCompute/machines/delete | Elimina uma máquina Azure Arc |
> | Microsoft.HybridCompute/machines/reconnect/action | Reconecta uma máquina Azure Arc |
> | Microsoft.HybridCompute/*/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Proprietário de registo de pilha azure

Permite-lhe gerir as inscrições do Azure Stack.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.AzureStack/registrations/products/*/action |  |
> | Microsoft.AzureStack/registrations/products/read | Obtém as propriedades de um produto Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Obtém as propriedades de um registo azure stack |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identidade


### <a name="managed-identity-contributor"></a>Colaborador de Identidade Gerido

Criar, Ler, Atualizar e Eliminar identidade atribuída ao utilizador

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtém um utilizador existente a identidade atribuída |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Cria uma nova identidade atribuída ao utilizador ou atualiza as etiquetas associadas a uma identidade atribuída ao utilizador existente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Elimina uma identidade atribuída a um utilizador existente |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Operador de Identidade Gerido

Ler e Atribuir identidade atribuída ao utilizador

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integração


### <a name="api-management-service-contributor"></a>Colaborador do Serviço de Gestão da API

Pode gerir o serviço e as APIs

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/* | Criar e gerir o serviço de Gestão API |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Papel do operador de serviço de gestão da API

Pode gerir o serviço, mas não as APIs

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do Serviço de Gestão da API |
> | Microsoft.ApiManagement/service/backup/action | Serviço de Gestão aPI de backup para o recipiente especificado em uma conta de armazenamento fornecida por utilizador |
> | Microsoft.ApiManagement/service/delete | Eliminar a instância do Serviço de Gestão da API |
> | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gestão aPI |
> | Microsoft.ApiManagement/service/read | Ler metadados para uma instância do Serviço de Gestão da API |
> | Microsoft.ApiManagement/service/restore/action | Restaurar o Serviço de Gestão da API do recipiente especificado numa conta de armazenamento fornecida pelo utilizador |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carregue o certificado SSL para um Serviço de Gestão API |
> | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizados para um Serviço de Gestão API |
> | Microsoft.ApiManagement/service/write | Criar ou atualizar a instância do Serviço de Gestão aPI |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtenha chaves associadas ao utilizador |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Papel de leitor de serviço de gestão da API

Acesso apenas a leitura ao serviço e APIs

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do Serviço de Gestão da API |
> | Microsoft.ApiManagement/service/read | Ler metadados para uma instância do Serviço de Gestão da API |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtenha chaves associadas ao utilizador |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Proprietário de dados de ônibus de serviço Azure

Permite o acesso total aos recursos do Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Recetor de dados de ônibus de serviço Azure

Permite ter acesso aos recursos do Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Remetente de dados de ônibus de serviço Azure

Permite o envio de acesso aos recursos do Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Contribuinte de Conta de Sistemas Inteligentes

Permite-lhe gerir as contas da Intelligent Systems, mas não ter acesso a elas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.IntelligentSystems/accounts/* | Criar e gerir contas de sistemas inteligentes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Colaborador de aplicações lógicas

Permite-lhe gerir aplicações lógicas, mas não alterar o acesso às mesmas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devolva a conta de armazenamento com a conta dada. |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | Microsoft.Insights/logdefinitions/* | Esta permissão é necessária para os utilizadores que necessitem de acesso a Registos de Atividade através do portal. Lista as categorias de registo no Registo de Atividades. |
> | Microsoft.Insights/metricDefinitions/* | Ler definições métricas (lista dos tipos métricos disponíveis para um recurso). |
> | Microsoft.Logic/* | Gere os recursos das Aplicações Lógicas. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenha os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Web/connectionGateways/* | Criar e gerir um Gateway de Ligação. |
> | Microsoft.Web/connections/* | Criar e gerir uma Ligação. |
> | Microsoft.Web/customApis/* | Cria e gere uma API personalizada. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obtenha as propriedades em um Plano de Serviço de Aplicações |
> | Microsoft.Web/sites/functions/listSecrets/action | Segredos da Função da Lista. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Operador de aplicações lógicas

Permite-lhe ler, ativar e desativar aplicações lógicas, mas não editá-las ou atualizá-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/*/read | Ler regras de alerta insights |
> | Microsoft.Insights/metricAlerts/*/read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Obtém definições de diagnóstico para Aplicações Lógicas |
> | Microsoft.Insights/metricDefinitions/*/read | Obtém as métricas disponíveis para Aplicações Lógicas. |
> | Microsoft.Logic/*/read | Lê recursos de Apps Lógicas. |
> | Microsoft.Logic/workflows/disable/action | Desativa o fluxo de trabalho. |
> | Microsoft.Logic/workflows/enable/action | Ativa o fluxo de trabalho. |
> | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Microsoft.Resources/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenha os resultados da operação de subscrição. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Web/connectionGateways/*/read | Leia gateways de ligação. |
> | Microsoft.Web/connections/*/read | Leia ligações. |
> | Microsoft.Web/customApis/*/read | Leia a API personalizada. |
> | Microsoft.Web/serverFarms/read | Obtenha as propriedades em um Plano de Serviço de Aplicações |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Internet das coisas


### <a name="eventgrid-eventsubscription-contributor"></a>Colaborador de Subscrição de EventosGrid

Permite-lhe gerir as operações de subscrição de eventos EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar subscrições globais de eventos por tipo tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Lista de assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Lista de assinaturas de eventos regionais por tópico |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Leitor de Subscrição de EventosGrid

Permite-lhe ler as assinaturas do eventoGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.EventGrid/eventSubscriptions/read | Leia um eventoSubscrição |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar subscrições globais de eventos por tipo tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Lista de assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Lista de assinaturas de eventos regionais por tópico |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Gestão + governação


### <a name="application-insights-component-contributor"></a>Contribuidor de componentes de insights de aplicação

Pode gerir componentes de Insights de Aplicação

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras clássicas de alerta |
> | Microsoft.Insights/metricAlerts/* | Criar e gerir novas regras de alerta |
> | Microsoft.Insights/components/* | Criar e gerir componentes insights |
> | Microsoft.Insights/webtests/* | Criar e gerir testes web insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Insights de aplicação Snapshot Debugger

Dá permissão ao utilizador para visualizar e descarregar imagens de depuração recolhidas com o Debugger Snapshot Debugger da Aplicação Insights. Note que estas permissões não estão incluídas nas funções [de Proprietário](#owner) ou [Colaborador.](#contributor)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-job-operator"></a>Operador de trabalho de automação

Criar e Gerir Empregos utilizando os Runbooks automation.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê recursos híbridos do trabalhador do livro de corridas |
> | Microsoft.Automation/automationAccounts/jobs/read | Consegue um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de trabalho da Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Cria um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém a saída de um emprego |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operador de Automatização

Operadores de Automação são capazes de iniciar, parar, suspender e retomar postos de trabalho

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê recursos híbridos do trabalhador do livro de corridas |
> | Microsoft.Automation/automationAccounts/jobs/read | Consegue um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de trabalho da Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Cria um trabalho de Automação Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtém um horário de trabalho da Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Cria um horário de trabalho da Automação Azure |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o espaço de trabalho ligado à conta de automação |
> | Microsoft.Automation/automationAccounts/read | Obtém uma conta de Automação Azure |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtém um livro de execução da Automação Azure |
> | Microsoft.Automation/automationAccounts/schedules/read | Obtém um ativo de agenda de Automação Azure |
> | Microsoft.Automation/automationAccounts/schedules/write | Cria ou atualiza um ativo de programação da Automação Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém a saída de um emprego |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Operador de livro de automação

Leia as propriedades do Runbook - para ser capaz de criar Jobs do livro de corridas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtém um livro de execução da Automação Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Leitor de Faturação

Permite ler o acesso aos dados de faturação

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Billing/*/read | Ler informações sobre faturação |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Contribuinte de Plantas

Pode gerir definições de plantas, mas não atribuí-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Blueprint/blueprints/* | Crie e gerencie definições de plantas ou artefactos de plantas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Operador de plantas

Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. NOTA: isto só funciona se a atribuição for feita com uma identidade gerida atribuída pelo utilizador.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Blueprint/blueprintAssignments/* | Criar e gerir tarefas de plantas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Colaborador de Gestão de Custos

Pode ver os custos e gerir a configuração de custos (por exemplo, orçamentos, exportações)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Consumo/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Lê recomendações |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Leitor de Gestão de Custos

Pode ver os dados de custos e a configuração (por exemplo, orçamentos, exportações)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Obtém a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Lê recomendações |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Função de colaborador de aplicação gerida

Permite a criação de recursos de aplicação geridos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Soluções/aplicações/* |  |
> | Microsoft.Solutions/register/action | Registe-se em Soluções. |
> | Microsoft.Recursos/subscrições/recursosGroups/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Função do operador de aplicação gerida

Permite-lhe ler e realizar ações sobre recursos de Aplicação Geridas

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Solutions/applications/read | Recupera uma lista de aplicações. |
> | Microsoft.Solutions/*/action |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Leitor de Aplicações Geridas

Permite-lhe ler recursos numa aplicação gerida e solicitar acesso ao JIT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Atribuição de registo de serviços geridos eliminar função

Serviços geridos Atribuição de Registo De exclusão De função permite aos utilizadores de inquilinos gerentes apagar a atribuição de registo atribuída ao seu inquilino.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ManagedServices/registrationAssignments/read | Recupera uma lista de atribuições de registo de Serviços Geridos. |
> | Microsoft.ManagedServices/registrationAssignments/delete | Remove a atribuição de registo de Serviços Geridos. |
> | Microsoft.ManagedServices/operationStatuses/read | Lê o estado de funcionamento do recurso. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Colaborador do Grupo de Gestão

Papel de Colaborador do Grupo de Gestão

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/delete | Eliminar o grupo de gestão. |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Desvincula a subscrição do grupo de gestão. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associa a subscrição existente com o grupo de gestão. |
> | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gestão. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Leitor de Grupo de Gestão

Papel de leitor de grupo de gestão

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Colaborador de Monitorização

Pode ler todos os dados de monitorização e editar definições de monitorização. Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/components/* | Criar e gerir componentes insights |
> | Microsoft.Insights/DiagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | Microsoft.Insights/eventtypes/* | Lista de eventos de Registo de Atividades (eventos de gestão) numa subscrição. Esta permissão é aplicável tanto ao acesso programático como ao portal ao Registo de Atividades. |
> | Microsoft.Insights/LogDefinitions/* | Esta permissão é necessária para os utilizadores que necessitem de acesso a Registos de Atividade através do portal. Lista as categorias de registo no Registo de Atividades. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Ler definições métricas (lista dos tipos métricos disponíveis para um recurso). |
> | Microsoft.Insights/Metrics/* | Leia as métricas para um recurso. |
> | Microsoft.Insights/Register/Action | Registe o fornecedor Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Criar e gerir testes web insights |
> | Microsoft.Insights/livros/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Leia/escreva/apague pacotes de soluções de análise de registo. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Leia/escreva/apague as pesquisas guardadas por registo. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves partilhadas para o espaço de trabalho. Estas teclas são usadas para ligar os agentes da Microsoft Operational Insights ao espaço de trabalho. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Leia/escreva/elimine as configurações de informação de armazenamento de análise de registo. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.WorkloadMonitor/monitores/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft.AlertsGe/actionRules/* |  |
> | Microsoft.AlertsManagement/smartGroups/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Editor de Métricas de Monitorização

Permite a publicação de métricas contra recursos Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Insights/Register/Action | Registe o fornecedor Microsoft Insights |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Insights/Metrics/Write | Escrever métricas |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Leitor de Monitorização

Pode ler todos os dados de monitorização (métricas, registos, etc.). Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Novo contribuinte de conta APM da Relíquia

Permite-lhe gerir as contas e aplicações da New Relic Application Performance Management, mas não o acesso às mesmas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | NewRelic.APM/accounts/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights Data Writer (Pré-visualização)

Permite ler o acesso às políticas de recursos e escrever acesso a eventos de política de componentes de recursos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Autorizações/tarefas/leitura | Obtenha informações sobre uma missão política. |
> | Microsoft.Autorizações/definições de políticas/leitura | Obtenha informações sobre uma definição de política. |
> | Microsoft.Authorization/policysetdefinis/read | Obtenha informações sobre uma definição de definição de definição de definição de definição de política. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | Verifique o estado de conformidade de um determinado componente contra as políticas de dados. |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Inicie o registo dos eventos de política de componentes de recursos. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Contribuidor de Política de Recursos

Utilizadores com direitos de criar/modificar a política de recursos, criar bilhetes de apoio e ler recursos/hierarquia.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.Autorizações/tarefas políticas/* | Criar e gerir atribuições políticas |
> | Microsoft.Autorizações/definições de políticas/* | Criar e gerir definições políticas |
> | Microsoft.Authorization/policysetdefinitions/* | Criar e gerir conjuntos de políticas |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Colaborador de Coleções de Emprego do Scheduler

Permite-lhe gerir as coleções de emprego do Scheduler, mas não ter acesso a elas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Scheduler/jobcollections/* | Criar e gerir coleções de emprego |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Colaborador de Recuperação do Site

Permite-lhe gerir o serviço de recuperação do site, exceto criação de cofre e atribuição de papéis

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Update Resource Certificate atualiza o certificado de credencial de recurso/cofre. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerir informações estendidas relacionadas com o cofre |
> | Microsoft.RecoveryServices/Vaults/read | A operação Get Vault recebe um objeto que representa o recurso Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Criar ou atualizar definições de alerta de replicação |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Leia quaisquer Eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Criar e gerir tecidos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerir trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Criar e gerir políticas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Criar e gerir planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Criar e gerir a configuração de armazenamento do cofre dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização para um cofre de serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação Vault Token pode ser usada para conseguir o Vault Token para operações de backend do nível do cofre. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Leia os alertas para o cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | Leia qualquer estado de operação de replicação do cofre |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operador de recuperação do site

Permite-lhe falhar e falhar, mas não realizar outras operações de gestão de recuperação do site

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Get Extended Info obtém uma Info Estendida de um objeto que representa o recurso Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | A operação Get Vault recebe um objeto que representa o recurso Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser utilizada obter o estado de funcionamento e resultado para a operação assíncronamente submetida |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada para registar os contentores para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Leia quaisquer Definições de Alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Leia quaisquer Eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica a consistência do tecido |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Leia quaisquer tecidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar certificado para tecido |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer Redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Leia quaisquer Mapeamentos de Rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Leia quaisquer recipientes de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Leia quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover planeado |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Leia quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Leia quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replicação de reparação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Reproteger o item protegido |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Recipiente de proteção para interruptores |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Ativação Pós-falha de Teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Teste de limpeza de failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Ativação pós-falha |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Serviço de Mobilidade Atualizada |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Leia quaisquer mapeamentos de contentores de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Leia quaisquer Prestadores de Serviços de Recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Fornecedor de Atualização |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Leia quaisquer Classificações de Armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Leia quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Leia quaisquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerir trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Leia quaisquer Políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de Recuperação de Compromissos failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de Recuperação de Failover Planeado |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Leia quaisquer Planos de Recuperação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Plano de Recuperação de Reproteção |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plano de Recuperação de Falhas de Teste |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plano de recuperação de limpeza de falhas de teste |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de Recuperação de Failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Leia os alertas para o cofre dos serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização para um cofre de serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação Vault Token pode ser usada para conseguir o Vault Token para operações de backend do nível do cofre. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Leitor de Recuperação do Site

Permite-lhe visualizar o estado de Recuperação do Site, mas não realizar outras operações de gestão

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Get Extended Info obtém uma Info Estendida de um objeto que representa o recurso Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Get Vault recebe um objeto que representa o recurso Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser utilizada obter o estado de funcionamento e resultado para a operação assíncronamente submetida |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada para registar os contentores para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Leia quaisquer Definições de Alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Leia quaisquer Eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Leia quaisquer tecidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer Redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Leia quaisquer Mapeamentos de Rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Leia quaisquer recipientes de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Leia quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Leia quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Leia quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Leia quaisquer mapeamentos de contentores de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Leia quaisquer Prestadores de Serviços de Recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Leia quaisquer Classificações de Armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Leia quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Leia quaisquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Leia quaisquer Empregos |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Leia quaisquer Políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Leia quaisquer Planos de Recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização para um cofre de serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação Vault Token pode ser usada para conseguir o Vault Token para operações de backend do nível do cofre. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Colaborador de Pedido de Apoio

Permite-lhe criar e gerir pedidos de suporte

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Contribuinte do livro

Pode salvar livros partilhados.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Insights/livros/escrita | Criar ou atualizar um livro |
> | Microsoft.Insights/livros/eliminação | Apagar um livro |
> | Microsoft.Insights/livros/leitura | Leia um livro |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Leitor de livro

Pode ler livros.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | microsoft.insights/books/read | Leia um livro |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Realidade mista


### <a name="spatial-anchors-account-contributor"></a>Contribuinte da conta de âncoras espaciais

Permite-lhe gerir âncoras espaciais na sua conta, mas não eliminá-las

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descubra âncoras espaciais próximas |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obtenha propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Envie dados de diagnóstico para ajudar a melhorar a qualidade do serviço Deâncoras Espaciais Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Proprietário da conta de âncoras espaciais

Permite-lhe gerir âncoras espaciais na sua conta, incluindo apagando-as

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Eliminar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descubra âncoras espaciais próximas |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obtenha propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Envie dados de diagnóstico para ajudar a melhorar a qualidade do serviço Deâncoras Espaciais Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Leitor de conta de âncoras espaciais

Permite localizar e ler propriedades de âncoras espaciais na sua conta

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descubra âncoras espaciais próximas |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obtenha propriedades de âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Envie dados de diagnóstico para ajudar a melhorar a qualidade do serviço Deâncoras Espaciais Azure |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Redes


### <a name="cdn-endpoint-contributor"></a>Colaborador de ponto final da CDN

Pode gerir os pontos finais da CDN, mas não pode conceder acesso a outros utilizadores.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Leitor de ponto final CDN

Pode ver pontos finais de CDN, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Colaborador de perfil cdn

Pode gerir perfis de CDN e seus pontos finais, mas não pode conceder acesso a outros utilizadores.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Leitor de perfil CDN

Pode ver perfis de CDN e seus pontos finais, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Colaborador clássico da rede

Permite-lhe gerir redes clássicas, mas não ter acesso a elas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ClassicNetwork/* | Criar e gerir redes clássicas |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Contribuinte da Zona DNS

Permite-lhe gerir zonas dNS e recordes em DNS Azure, mas não permite controlar quem tem acesso a elas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Network/dnsZones/* | Criar e gerir zonas e registos dNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Colaborador de Rede

Permite-lhe gerir redes, mas não ter acesso a elas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Network/* | Criar e gerir redes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Colaborador do Gestor de Tráfego

Permite-lhe gerir os perfis do Traffic Manager, mas não permite controlar quem tem acesso aos mesmos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Segurança


### <a name="azure-sentinel-contributor"></a>Colaborador Azure Sentinel

Colaborador Azure Sentinel

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Procure usando um motor novo. |
> | Microsoft.OperationalInsights/workspaces/*/read | Ver dados de análise de registo |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Obtenha saída da solução OMS |
> | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas sobre os dados no espaço de trabalho |
> | Microsoft.OperationalInsights/workspaces/consulta/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obter fontes de dados sob um espaço de trabalho. |
> | Microsoft.Insights/livros/* |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Leitor de Sentinela Azure

Leitor de Sentinela Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Procure usando um motor novo. |
> | Microsoft.OperationalInsights/workspaces/*/read | Ver dados de análise de registo |
> | Microsoft.OperationalInsights/workspaces/LinkedServices/read | Obtenha serviços ligados sob um determinado espaço de trabalho. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtém uma consulta de pesquisa guardada |
> | Microsoft.OperationsManagement/solutions/read | Obtenha saída da solução OMS |
> | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas sobre os dados no espaço de trabalho |
> | Microsoft.OperationalInsights/workspaces/consulta/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obter fontes de dados sob um espaço de trabalho. |
> | Microsoft.Insights/livros/leitura | Leia um livro |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Resposta Sentinela Azure

Resposta Sentinela Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.SecurityInsights/incidents/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Procure usando um motor novo. |
> | Microsoft.OperationalInsights/workspaces/*/read | Ver dados de análise de registo |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obter fontes de dados sob um espaço de trabalho. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtém uma consulta de pesquisa guardada |
> | Microsoft.OperationsManagement/solutions/read | Obtenha saída da solução OMS |
> | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas sobre os dados no espaço de trabalho |
> | Microsoft.OperationalInsights/workspaces/consulta/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obter fontes de dados sob um espaço de trabalho. |
> | Microsoft.Insights/livros/leitura | Leia um livro |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Contribuinte chave do cofre

Permite-lhe gerir cofres chave, mas não acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Purgue um cofre de chave suave apagado |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Administrador de Segurança

Pode ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, descartar alertas e recomendações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerir atribuições políticas |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerir definições políticas |
> | Microsoft.Authorization/policySetDefinitions/* | Criar e gerir conjuntos de políticas |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | Microsoft.operationalInsights/workspaces/*/read | Ver dados de análise de registo |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Security/* | Criar e gerir componentes e políticas de segurança |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Contribuinte de Avaliação de Segurança

Permite-lhe levar avaliações para o Centro de Segurança

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Segurança/avaliações/escrita | Crie ou atualize avaliações de segurança na sua subscrição |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Gestor de Segurança (Legado)

Este é um papel legado. Por favor, use o Administrador de Segurança em vez disso.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ClassicCompute/*/read | Ler informações de configuração máquinas virtuais clássicas |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Escreva configuração para máquinas virtuais clássicas |
> | Microsoft.ClassicNetwork/*/read | Leia informações de configuração sobre a rede clássica |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Security/* | Criar e gerir componentes e políticas de segurança |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Leitor de Segurança

Pode ver recomendações e alertas, ver políticas de segurança, ver estados de segurança, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.operationalInsights/workspaces/*/read | Ver dados de análise de registo |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Security/*/read | Ler componentes e políticas de segurança |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Armazenamento


### <a name="avere-contributor"></a>Colaborador avere

Pode criar e gerir um cluster Avere vFXT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obtém uma definição de subnet de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não alertável. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma subnet. Não é alertável. |
> | Microsoft.Network/networkSecurityGroups/join/action | Junta-se a um grupo de segurança da rede. Não alertável. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Obtém os recursos para o grupo de recursos. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da aleletação de uma bolha |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve uma bolha ou uma lista de bolhas |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever uma bolha |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Operador de Avere

Usado pelo cluster Avere vFXT para gerir o cluster

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Compute/virtualMachines/read | Obtenha as propriedades de uma máquina virtual |
> | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obtém uma definição de subnet de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não alertável. |
> | Microsoft.Network/networkSecurityGroups/join/action | Junta-se a um grupo de segurança da rede. Não alertável. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Devolve o resultado da aparação de um contentor |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Lista de devoluções de contentores |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Devolve o resultado do recipiente de colocação blob |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da aleletação de uma bolha |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve uma bolha ou uma lista de bolhas |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever uma bolha |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Colaborador de backup

Permite-lhe gerir o serviço de backup, mas não pode criar cofres e dar acesso a outros

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gerir resultados da operação na gestão de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Criar e gerir recipientes de backup dentro de tecidos de backup do cofre dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contentores |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerir empregos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Empregos de Exportação |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerir Resultados de operações de gestão de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Criar e gerir políticas de backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser apoiados |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Criar e gerir itens apoiados |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Criar e gerir contentores que retenham itens de reserva |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumidos resumos para itens protegidos e servidores protegidos para um serviço de recuperação . |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Criar e gerir certificados relacionados com backup no cofre dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerir informações estendidas relacionadas com o cofre |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Get Vault recebe um objeto que representa o recurso Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
> | Microsoft.RecoveryServices/Vaults/usages/* | Criar e gerir o uso do cofre dos Serviços de Recuperação |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação em item protegido |
> | Microsoft.RecoveryServices/Vaults/write | Create Vault operation cria um recurso Azure de tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve o estado de operação de backup para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de backup registados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obtenha todos os recipientes protegidos |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verifique o estado de backup para cofres de serviços de recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar funcionalidades |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de Operações para um Fornecedor de Recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Estado da Operação para uma determinada Operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Enumerar todas as intenções de proteção de reserva |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operador de backup

Permite-lhe gerir serviços de backup, exceto remoção de backup, criação de cofre e acesso a outros

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Network/virtualNetworks/read | Obtenha a definição de rede virtual |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devolução do estado da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém resultado da Operação realizada no Recipiente de Proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executa backup para objeto protegido. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém resultado da operação realizada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação realizada em Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Fornecimento de Recuperação instantânea de artigos para artigoprotegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenha pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação instantânea do item para o item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de reserva |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devoluções todos os contentores registados |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contentores |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerir empregos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Empregos de Exportação |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerir Resultados de operações de gestão de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtenha resultados da Operação Política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as Políticas de Proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser apoiados |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devolve todos os recipientes pertencentes à subscrição |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumidos resumos para itens protegidos e servidores protegidos para um serviço de recuperação . |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Update Resource Certificate atualiza o certificado de credencial de recurso/cofre. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Get Extended Info obtém uma Info Estendida de um objeto que representa o recurso Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Get Extended Info obtém uma Info Estendida de um objeto que representa o recurso Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Get Vault recebe um objeto que representa o recurso Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser utilizada obter o estado de funcionamento e resultado para a operação assíncronamente submetida |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada para registar os contentores para um recurso. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação do contentor de registo pode ser utilizada para registar um contentor com o Serviço de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização para um cofre de serviços de recuperação. |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades para a conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação em item protegido |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve o estado de operação de backup para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter Estado de Operação Política. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Cria um contentor registado |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Faça um inquérito sobre cargas de trabalho dentro de um contentor |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de backup registados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de reserva |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obtenha uma intenção de proteção de reserva |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obtenha todos os recipientes protegidos |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pegue todos os itens em um recipiente |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verifique o estado de backup para cofres de serviços de recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar funcionalidades |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de Operações para um Fornecedor de Recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Estado da Operação para uma determinada Operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Enumerar todas as intenções de proteção de reserva |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Leitor de Backup

Pode ver serviços de backup, mas não pode fazer alterações

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devolução do estado da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém resultado da Operação realizada no Recipiente de Proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém resultado da operação realizada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação realizada em Itens Protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenha pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devoluções todos os contentores registados |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devolve o resultado da operação de emprego. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Devolve todos os objetos de trabalho |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Empregos de Exportação |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Devolve o resultado da operação de backup para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtenha resultados da Operação Política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as Políticas de Proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devolve todos os recipientes pertencentes à subscrição |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumidos resumos para itens protegidos e servidores protegidos para um serviço de recuperação . |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Get Extended Info obtém uma Info Estendida de um objeto que representa o recurso Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/read | A operação Get Vault recebe um objeto que representa o recurso Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser utilizada obter o estado de funcionamento e resultado para a operação assíncronamente submetida |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada para registar os contentores para um recurso. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Configuração de armazenamento de devoluções para cofre de serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Configuração de devoluções para cofre de serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve o estado de operação de backup para o cofre dos serviços de recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter Estado de Operação Política. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de backup registados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obtenha uma intenção de proteção de reserva |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pegue todos os itens em um recipiente |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verifique o estado de backup para cofres de serviços de recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de Operações para um Fornecedor de Recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Estado da Operação para uma determinada Operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Enumerar todas as intenções de proteção de reserva |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização para um cofre de serviços de recuperação. |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar funcionalidades |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Contribuinte de Conta de Armazenamento Clássico

Permite-lhe gerir contas de armazenamento clássicas, mas não ter acesso a elas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ClassicStorage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Função de serviço de serviço de chave de conta de armazenamento clássico

Os operadores chave da conta de armazenamento clássico são autorizados a listar e regenerar chaves em Contas de Armazenamento Clássicos

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Lista as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Contribuinte da Caixa de Dados

Permite-lhe gerir tudo ao abrigo do Serviço data box, exceto dar acesso a outros.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Databox/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Leitor de caixas de dados

Permite-lhe gerir o Serviço de Caixas de Dados, exceto criar detalhes de encomenda ou edição e dar acesso a outros.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Lista as credenciais não encriptadas relacionadas com a encomenda. |
> | Microsoft.Databox/locations/availableSkus/action | Este método devolve a lista de skus disponíveis. |
> | Microsoft.Databox/locations/validaçõesInputs/action | Este método faz todo o tipo de validações. |
> | Microsoft.Databox/locations/regionConfiguration/action | Este método devolve as configurações para a região. |
> | Microsoft.Databox/locations/validateAddress/action | Valida o endereço de envio e fornece endereços alternativos, se houver. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Desenvolvedor de análise de data lake

Permite-lhe submeter, monitorizar e gerir os seus próprios empregos, mas não criar ou apagar contas data Lake Analytics.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Elimine uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceda permissões para cancelar os trabalhos apresentados por outros utilizadores. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta dataLakeStore ligada a uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desligue uma conta DataLakeStore a partir de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de Armazenamento ligada a uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Desvincular uma conta de Armazenamento a partir de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Apague uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de cálculo. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Apague uma política de cálculo. |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Acesso a Leitores e Dados

Permite-lhe ver tudo, mas não permitirá que apague ou crie uma conta de armazenamento ou recursos contidos. Também permitirá o acesso de leitura/escrita a todos os dados contidos numa conta de armazenamento através do acesso às chaves da conta de armazenamento.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Devolve o token da conta SAS para a conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades para a conta de armazenamento especificada. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Contribuinte de Conta de Armazenamento

Permite a gestão de contas de armazenamento. Fornece acesso à chave da conta, que pode ser usada para aceder a dados através de autorização de chave partilhada.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma subnet. Não é alertável. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Storage/storageAccounts/* | Criar e gerir contas de armazenamento |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Função de serviço de serviço de operador chave de conta de armazenamento

Permite a listagem e regeneração das chaves de acesso à conta de armazenamento.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso da conta de armazenamento especificada. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Contribuinte de dados blob de armazenamento

Leia, escreva e elimine os recipientes de armazenamento e bolhas do Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Apagar um recipiente. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolva um recipiente ou uma lista de recipientes. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modificar os metadados ou propriedades de um contentor. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço Blob. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Apague uma bolha. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolva uma bolha ou uma lista de bolhas. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Escreva para uma bolha. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Proprietário de dados blob de armazenamento

Fornece acesso total aos recipientes e dados de blob de armazenamento Azure, incluindo a atribuição de controlo de acesso POSIX. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Permissões completas em contentores. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço Blob. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Permissões completas em bolhas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Leitor de dados blob de armazenamento

Leia e enumere os recipientes de armazenamento e bolhas azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolva um recipiente ou uma lista de recipientes. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço Blob. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolva uma bolha ou uma lista de bolhas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Depósito Blob Delegator

Obtenha uma chave de delegação de utilizadores, que pode ser usada para criar uma assinatura de acesso partilhado para um recipiente ou bolha que é assinada com credenciais Azure AD. Para mais informações, consulte [Criar uma delegação de utilizadores SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço Blob. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Colaborador de partilha de dados de ficheiros de armazenamento SMB Share

Permite ler, escrever e excluir o acesso a ficheiros/diretórios em ações de ficheiros Azure. Esta função não tem equivalente incorporado nos servidores de ficheiros do Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Devolve um ficheiro/pasta ou uma lista de ficheiros/pastas. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Devolve o resultado de escrever um ficheiro ou criar uma pasta. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Devolve o resultado da adesão de um ficheiro/pasta. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Relatório de armazenamento Dados SMB Share Colaborador Elevado

Permite ler, escrever, excluir e modificar ACLs em ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de alteração nos servidores de ficheiros do Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Devolve um ficheiro/pasta ou uma lista de ficheiros/pastas. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Devolve o resultado de escrever um ficheiro ou criar uma pasta. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Devolve o resultado da adesão de um ficheiro/pasta. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Devolve o resultado da modificação da permissão num ficheiro/pasta. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Armazenamento Arquivo Data SMB Share Reader

Permite o acesso a ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de leitura nos servidores de ficheiros do Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Devolve um ficheiro/pasta ou uma lista de ficheiros/pastas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Contribuinte de dados da fila de armazenamento

Leia, escreva e elimine as filas de armazenamento do Azure e as mensagens de fila. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Apague uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolva uma fila ou uma lista de filas. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modificar metadados ou propriedades de fila. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Apague uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Espreite ou recupere uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Processador de dados de fila de armazenamento

Espreite, recupere e apague uma mensagem de uma fila de Armazenamento Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Olha uma mensagem. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Recuperar e apagar uma mensagem. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Remetente de mensagem de dados de fila de armazenamento

Adicione mensagens a uma fila de Armazenamento Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Leitor de dados da fila de armazenamento

Leia e enumere as filas de armazenamento azure e as mensagens de fila. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Espreite ou recupere uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader-preview"></a>Leitor de dados do Azure Maps (Pré-visualização)

Concede acesso a dados relacionados com mapas de leitura a partir de uma conta de mapas Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Maps/accounts/data/read | Os dados dos subsídios lêem o acesso a uma conta de mapas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Colaborador de Serviço de Pesquisa

Permite-lhe gerir os serviços de pesquisa, mas não aceder aos mesmos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Search/searchServices/* | Criar e gerir serviços de pesquisa |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Colaborador do Plano Web

Permite-lhe gerir os planos web para websites, mas não ter acesso aos mesmos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Web/serverFarms/* | Criar e gerir quintas de servidores |
> | Microsoft.Web/hostingEnvironments/Join/Action | Junta-se a um Ambiente de Serviço de Aplicações |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Colaborador do Site

Permite-lhe gerir websites (não planos web), mas não ter acesso aos mesmos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/components/* | Criar e gerir componentes insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | Microsoft.Web/certificates/* | Criar e gerir certificados de website |
> | Microsoft.Web/listSitesAssignedToHostName/read | Obtenha nomes de sites atribuídos ao nome de anfitrião. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obtenha as propriedades em um Plano de Serviço de Aplicações |
> | Microsoft.Web/sites/* | Criar e gerir websites (a criação de sites também requer permissões de escrita para o Plano de Serviço de Aplicações associado) |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Outros


### <a name="biztalk-contributor"></a>Colaborador biztalk

Permite-lhe gerir os serviços bizTalk, mas não ter acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerir serviços BizTalk |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Passos seguintes

- [Prestador de recursos de correspondência para o serviço](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Papéis personalizados do Azure](custom-roles.md)
- [Permissions in Azure Security Center](../security-center/security-center-permissions.md) (Permissões no Centro de Segurança do Azure)
