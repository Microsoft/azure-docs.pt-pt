---
title: Papéis integrados para os recursos do Azure / Microsoft Docs
description: Descreve as funções incorporadas para o controlo de acesso baseado em papéis (RBAC) e recursos Azure. Lista as Ações, NotActions, DataActions e NotDataActions.
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
ms.date: 02/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 63b1adc7b25b732cda147c5c1d11cc37e7b39248
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562025"
---
# <a name="built-in-roles-for-azure-resources"></a>Papéis integrados para os recursos do Azure

[O controlo de acesso baseado em funções (RBAC)](overview.md) tem várias funções incorporadas para recursos Azure que pode atribuir a utilizadores, grupos, diretores de serviços e identidades geridas. As atribuições de funções são a forma como controlas o acesso aos recursos do Azure. Se as funções incorporadas não atenderem às necessidades específicas da sua organização, pode criar as suas próprias [funções personalizadas para os recursos Azure.](custom-roles.md)

Este artigo enumera as funções incorporadas para os recursos Azure, que estão sempre a evoluir. Para obter as funções mais recentes, utilize a lista de definição de definição de funções [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [az](/cli/azure/role/definition#az-role-definition-list). Se procura funções de administrador para o Azure Ative Directory, consulte [as permissões de funções do Administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Descrições de papéis incorporados

A tabela seguinte fornece uma breve descrição de cada papel incorporado. Clique no nome do papel para ver a lista de `Actions`, `NotActions`, `DataActions`e `NotDataActions` para cada papel. Para obter informações sobre o que estas ações significam e como se aplicam aos planos de gestão e dados, consulte Compreender as definições de [papéis para os recursos Do Azure.](role-definitions.md)


> [!div class="mx-tableFixed"]
> | Papel incorporado | Descrição | Id |
> | --- | --- | --- |
> | [Proprietário](#owner) | Permite-lhe gerir tudo, incluindo o acesso aos recursos. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Contribuidor](#contributor) | Permite-lhe gerir tudo, menos dar acesso aos recursos. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Leitor](#reader) | Permite-lhe ver tudo, mas não fazer alterações. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
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
> | [Colaborador de Monitorização](#monitoring-contributor) | Pode ler todos os dados de monitorização e editar definições de monitorização. Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles) | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Editor de Métricas de Monitorização](#monitoring-metrics-publisher) | Permite a publicação de métricas contra recursos Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Leitor de Monitorização](#monitoring-reader) | Pode ler todos os dados de monitorização (métricas, registos, etc.). Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles) | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Contribuidor de Rede](#network-contributor) | Permite-lhe gerir redes, mas não ter acesso a elas. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Novo contribuinte de conta APM da Relíquia](#new-relic-apm-account-contributor) | Permite-lhe gerir as contas e aplicações da New Relic Application Performance Management, mas não o acesso às mesmas. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights Data Writer (Pré-visualização)](#policy-insights-data-writer-preview) | Permite ler o acesso às políticas de recursos e escrever acesso a eventos de política de componentes de recursos. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
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


## <a name="owner"></a>Proprietário
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo, incluindo o acesso aos recursos. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Ações** |  |
> | * | Criar e gerir recursos de todos os tipos |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="contributor"></a>Contribuinte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo, menos dar acesso aos recursos. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
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

## <a name="reader"></a>Leitor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ver tudo, mas não fazer alterações. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | acr excluir |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Apagar artefactos num registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | sinal de imagem acr |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Empurre/Puxe os metadados fidedignos de conteúdo para um registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | acr puxar |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Puxe ou obtenha imagens de um registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | acr empurrar |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Puxe ou obtenha imagens de um registo de contentores. |
> | Microsoft.ContainerRegistry/registries/push/write | Empurre ou escreva imagens para um registo de contentores. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | acr leitor de dados de quarentena |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registros/quarentena/read | Puxe ou obtenha imagens de quarentena do registo de contentores |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="acrquarantinewriter"></a>AcrQuarentenaEscritor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | acr escritor de dados de quarentena |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registros/quarentena/read | Puxe ou obtenha imagens de quarentena do registo de contentores |
> | Microsoft.ContainerRegistry/registros/quarentena/write | Escreva/Modifique o estado de quarentena das imagens em quarentena |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="api-management-service-contributor"></a>Colaborador do Serviço de Gestão da API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir o serviço e as APIs |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/* | Criar e gerir o serviço de Gestão API |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="api-management-service-operator-role"></a>Papel do operador de serviço de gestão da API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir o serviço, mas não as APIs |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
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
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="api-management-service-reader-role"></a>Papel de leitor de serviço de gestão da API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Acesso apenas a leitura ao serviço e APIs |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do Serviço de Gestão da API |
> | Microsoft.ApiManagement/service/read | Ler metadados para uma instância do Serviço de Gestão da API |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="app-configuration-data-owner"></a>Proprietário de dados de configuração de aplicativos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso total aos dados de Configuração de Aplicações. |
> | **Id** | 5ae67d6-50cb-40e7-96ff-dc2bfa4b606b |
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

## <a name="app-configuration-data-reader"></a>Leitor de dados de configuração de aplicativos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler o acesso aos dados de Configuração de Aplicações. |
> | **Id** | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.AppConfiguration/configuraçãoStores/*/read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="application-insights-component-contributor"></a>Contribuidor de componentes de insights de aplicação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir componentes de Insights de Aplicação |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras clássicas de alerta |
> | Microsoft.Insights/metricAlerts/* | Criar e gerir novas regras de alerta |
> | Microsoft.Insights/components/* | Criar e gerir componentes insights |
> | Microsoft.Insights/webtests/* | Criar e gerir testes web |
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

## <a name="application-insights-snapshot-debugger"></a>Insights de aplicação Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Dá permissão ao utilizador para visualizar e descarregar imagens de depuração recolhidas com o Debugger Snapshot Debugger da Aplicação Insights. Note que estas permissões não estão incluídas nas funções [de Proprietário](#owner) ou [Colaborador.](#contributor) |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
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

## <a name="automation-job-operator"></a>Operador de trabalho de automação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar e Gerir Empregos utilizando os Runbooks automation. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
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

## <a name="automation-operator"></a>Operador de Automatização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Operadores de Automação são capazes de iniciar, parar, suspender e retomar postos de trabalho |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
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

## <a name="automation-runbook-operator"></a>Operador de livro de automação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia as propriedades do Runbook - para ser capaz de criar Jobs do livro de corridas. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
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

## <a name="avere-contributor"></a>Colaborador avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode criar e gerir um cluster Avere vFXT. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
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
> | Microsoft.Storage/storageAccounts/* |  |
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

## <a name="avere-operator"></a>Operador de Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Usado pelo cluster Avere vFXT para gerir o cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
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

## <a name="azure-connected-machine-onboarding"></a>Onboard de máquina conectada Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode a bordo de Máquinas Conectadas Azure. |
> | **Id** | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
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

## <a name="azure-connected-machine-resource-administrator"></a>Administrador de recursos de máquina conectada Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler, escrever, excluir e reembarcar máquinas ligadas azure. |
> | **Id** | cd570a14-e51a-42ad-bac8-bafd67325302 |
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

## <a name="azure-event-hubs-data-owner"></a>Proprietário de dados do Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso total aos recursos do Azure Event Hubs. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Ações** |  |
> | Microsoft.EventHub/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="azure-event-hubs-data-receiver"></a>Recetor de dados do Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ter acesso aos recursos do Azure Event Hubs. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="azure-event-hubs-data-sender"></a>Remetente de dados do Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o envio de acesso aos recursos do Azure Event Hubs. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Ações** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Papel de administrador de cluster de serviço Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Lista de ação credencial de administração de cluster. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Enumerar a credencial clusterAdmin de um cluster gerido |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtenha um perfil de acesso de cluster gerido por nome de papel usando credencial de lista |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Função de utilizador do cluster de serviço Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Enumerar a ação credencial do utilizador do cluster. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Enumerar a credencial do clusterUser de um cluster gerido |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="azure-maps-data-reader-preview"></a>Leitor de dados do Azure Maps (Pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Concede acesso a dados relacionados com mapas de leitura a partir de uma conta de mapas Azure. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Maps/accounts/data/read | Os dados dos subsídios lêem o acesso a uma conta de mapas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="azure-sentinel-contributor"></a>Colaborador Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Colaborador Azure Sentinel |
> | **Id** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Ações** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Procure usando um motor novo. |
> | Microsoft.OperationalInsights/workspaces/read | Obtém um espaço de trabalho existente |
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

## <a name="azure-sentinel-reader"></a>Leitor de Sentinela Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leitor de Sentinela Azure |
> | **Id** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Ações** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Procure usando um motor novo. |
> | Microsoft.OperationalInsights/workspaces/read | Obtém um espaço de trabalho existente |
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

## <a name="azure-sentinel-responder"></a>Resposta Sentinela Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Resposta Sentinela Azure |
> | **Id** | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | **Ações** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Procure usando um motor novo. |
> | Microsoft.OperationalInsights/workspaces/read | Obtém um espaço de trabalho existente |
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

## <a name="azure-service-bus-data-owner"></a>Proprietário de dados de ônibus de serviço Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso total aos recursos do Azure Service Bus. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Ações** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="azure-service-bus-data-receiver"></a>Recetor de dados de ônibus de serviço Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ter acesso aos recursos do Azure Service Bus. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
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

## <a name="azure-service-bus-data-sender"></a>Remetente de dados de ônibus de serviço Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o envio de acesso aos recursos do Azure Service Bus. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
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

## <a name="azure-stack-registration-owner"></a>Proprietário de registo de pilha azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as inscrições do Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
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

## <a name="backup-contributor"></a>Colaborador de backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o serviço de backup, mas não pode criar cofres e dar acesso a outros |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
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

## <a name="backup-operator"></a>Operador de backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir serviços de backup, exceto remoção de backup, criação de cofre e acesso a outros |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
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

## <a name="backup-reader"></a>Leitor de Backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver serviços de backup, mas não pode fazer alterações |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
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
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="billing-reader"></a>Leitor de Faturação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler o acesso aos dados de faturação |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
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

## <a name="biztalk-contributor"></a>Colaborador biztalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os serviços bizTalk, mas não ter acesso a eles. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerir serviços BizTalk |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="blockchain-member-node-access-preview"></a>Acesso ao nó do membro blockchain (pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso aos nódosos do Membro blockchain |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Ações** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista o nó de transação do membro blockchain existente. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Liga-se a um nó de transações do Membro blockchain. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="blueprint-contributor"></a>Contribuinte de Plantas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir definições de plantas, mas não atribuí-las. |
> | **Id** | 41077137-e803-4205-871c-5a86e6a753b4 |
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

## <a name="blueprint-operator"></a>Operador de plantas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. NOTA: isto só funciona se a atribuição for feita com uma identidade gerida atribuída pelo utilizador. |
> | **Id** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
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

## <a name="cdn-endpoint-contributor"></a>Colaborador de ponto final da CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir os pontos finais da CDN, mas não pode conceder acesso a outros utilizadores. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
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

## <a name="cdn-endpoint-reader"></a>Leitor de ponto final CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver pontos finais de CDN, mas não pode fazer alterações. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
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

## <a name="cdn-profile-contributor"></a>Colaborador de perfil cdn
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir perfis de CDN e seus pontos finais, mas não pode conceder acesso a outros utilizadores. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
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

## <a name="cdn-profile-reader"></a>Leitor de perfil CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver perfis de CDN e seus pontos finais, mas não pode fazer alterações. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
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

## <a name="classic-network-contributor"></a>Colaborador clássico da rede
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir redes clássicas, mas não ter acesso a elas. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
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

## <a name="classic-storage-account-contributor"></a>Contribuinte de Conta de Armazenamento Clássico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir contas de armazenamento clássicas, mas não ter acesso a elas. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
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

## <a name="classic-storage-account-key-operator-service-role"></a>Função de serviço de serviço de chave de conta de armazenamento clássico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Os operadores chave da conta de armazenamento clássico são autorizados a listar e regenerar chaves em Contas de Armazenamento Clássicos |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Ações** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Lista as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="classic-virtual-machine-contributor"></a>Colaborador de máquina virtual clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir máquinas virtuais clássicas, mas não ter acesso a elas, e não a rede virtual ou conta de armazenamento a que estão ligadas. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
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

## <a name="cognitive-services-contributor"></a>Colaborador de Serviços Cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar, ler, atualizar, apagar e gerir chaves dos Serviços Cognitivos. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
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

## <a name="cognitive-services-data-reader-preview"></a>Leitor de Dados de Serviços Cognitivos (Pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler os dados dos Serviços Cognitivos. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="cognitive-services-user"></a>Utilizador de Serviços Cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e listar chaves dos Serviços Cognitivos. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
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

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB Papel de leitor de conta
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler os dados da conta Azure Cosmos DB. Consulte o Contribuinte de [Conta DocumentDB](#documentdb-account-contributor) para gerir as contas da Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis, pode ler permissões dadas a cada utilizador |
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

## <a name="cosmos-db-operator"></a>Operador DB cosmos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as contas da Azure Cosmos DB, mas não aceder aos dados. Impede o acesso às chaves de conta e às cordas de ligação. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
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

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode submeter pedido de restauro de uma base de dados Cosmos DB ou um recipiente para uma conta |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Ações** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Enviar um pedido para configurar backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Submeter um pedido de restauro |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="cost-management-contributor"></a>Colaborador de Gestão de Custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os custos e gerir a configuração de custos (por exemplo, orçamentos, exportações) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
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

## <a name="cost-management-reader"></a>Leitor de Gestão de Custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver os dados de custos e a configuração (por exemplo, orçamentos, exportações) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
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

## <a name="data-box-contributor"></a>Contribuinte da Caixa de Dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir tudo ao abrigo do Serviço data box, exceto dar acesso a outros. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
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

## <a name="data-box-reader"></a>Leitor de caixas de dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o Serviço de Caixas de Dados, exceto criar detalhes de encomenda ou edição e dar acesso a outros. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
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

## <a name="data-factory-contributor"></a>Contribuinte da fábrica de dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar e gerir fábricas de dados, bem como recursos infantis dentro delas. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.DataFactory/dataFactories/* | Criar e gerir fábricas de dados e recursos infantis dentro delas. |
> | Microsoft.DataFactory/factories/* | Criar e gerir fábricas de dados e recursos infantis dentro delas. |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="data-lake-analytics-developer"></a>Desenvolvedor de análise de data lake
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe submeter, monitorizar e gerir os seus próprios empregos, mas não criar ou apagar contas data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c888 |
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

## <a name="data-purger"></a>Purgador de dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode expurgar dados de análise |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Ações** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Dados purgados de Insights de Aplicação |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Eliminar dados especificados do espaço de trabalho |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="devtest-labs-user"></a>Utilizador de Laboratórios DevTest
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ligar, iniciar, reiniciar e desligar as suas máquinas virtuais nos seus Laboratórios Azure DevTest. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
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

## <a name="dns-zone-contributor"></a>Contribuinte da Zona DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir zonas dNS e recordes em DNS Azure, mas não permite controlar quem tem acesso a elas. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Network/dnsZones/* | Criar e gerir zonas e registos dNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtém o estado de disponibilidade de todos os recursos no âmbito especificado |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de Apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="documentdb-account-contributor"></a>Contribuinte de Conta DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode gerir as contas da Azure Cosmos DB. Azure Cosmos DB é anteriormente conhecido como DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.DocumentDb/databaseAccounts/* | Criar e gerir contas da Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="eventgrid-eventsubscription-contributor"></a>Colaborador de Subscrição de EventosGrid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as operações de subscrição de eventos EventGrid. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
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

## <a name="eventgrid-eventsubscription-reader"></a>Leitor de Subscrição de EventosGrid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler as assinaturas do eventoGrid. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
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

## <a name="hdinsight-cluster-operator"></a>Operador de cluster HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e modificar as configurações do cluster HDInsight. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
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

## <a name="hdinsight-domain-services-contributor"></a>Colaborador de Serviços de Domínio HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler, criar, modificar e eliminar as operações relacionadas com os serviços de domínio necessários para o Pacote de Segurança Empresarial HDInsight |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
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

## <a name="intelligent-systems-account-contributor"></a>Contribuinte de Conta de Sistemas Inteligentes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as contas da Intelligent Systems, mas não ter acesso a elas. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="key-vault-contributor"></a>Contribuinte chave do cofre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir cofres chave, mas não acesso a eles. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
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

## <a name="lab-creator"></a>Criador de Laboratório
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar, gerir, apagar os seus laboratórios geridos sob as suas Contas Azure Lab. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
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

## <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | O Log Analytics Contributor pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VMs; chaves da conta de armazenamento de leitura para poder configurar a recolha de registos do Armazenamento Azure; criação e configuração de contas de Automação; adicionando soluções; e configurar diagnósticos Azure em todos os recursos do Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
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

## <a name="log-analytics-reader"></a>Leitor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | O Log Analytics Reader pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração dos diagnósticos do Azure em todos os recursos do Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
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

## <a name="logic-app-contributor"></a>Colaborador de aplicações lógicas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir aplicações lógicas, mas não alterar o acesso às mesmas. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
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

## <a name="logic-app-operator"></a>Operador de aplicações lógicas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler, ativar e desativar aplicações lógicas, mas não editá-las ou atualizá-las. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
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

## <a name="managed-application-contributor-role"></a>Função de colaborador de aplicação gerida
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a criação de recursos de aplicação geridos. |
> | **Id** | 641177b8-a67a-45b9-a033-47bc880bb21e |
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

## <a name="managed-application-operator-role"></a>Função do operador de aplicação gerida
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler e realizar ações sobre recursos de Aplicação Geridas |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
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

## <a name="managed-applications-reader"></a>Leitor de Aplicações Geridas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ler recursos numa aplicação gerida e solicitar acesso ao JIT. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
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

## <a name="managed-identity-contributor"></a>Colaborador de Identidade Gerido
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Criar, Ler, Atualizar e Eliminar identidade atribuída ao utilizador |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
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

## <a name="managed-identity-operator"></a>Operador de Identidade Gerido
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ler e Atribuir identidade atribuída ao utilizador |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
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

## <a name="managed-services-registration-assignment-delete-role"></a>Atribuição de registo de serviços geridos eliminar função
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Serviços geridos Atribuição de Registo De exclusão De função permite aos utilizadores de inquilinos gerentes apagar a atribuição de registo atribuída ao seu inquilino. |
> | **Id** | 91c1777a-f3dc-4fae-b103-61d183457e46 |
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

## <a name="management-group-contributor"></a>Colaborador do Grupo de Gestão
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Papel de Colaborador do Grupo de Gestão |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
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

## <a name="management-group-reader"></a>Leitor de Grupo de Gestão
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Papel de leitor de grupo de gestão |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="monitoring-contributor"></a>Colaborador de Monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitorização e editar definições de monitorização. Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles) |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Ações** |  |
> | */ler | Leia os recursos de todos os tipos, exceto segredos. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Leia/escreva/apague as regras de alerta. |
> | Microsoft.Insights/components/* | Ler/escrever/excluir componentes de Insights de Aplicação. |
> | Microsoft.Insights/DiagnosticSettings/* | Ler/escrever/eliminar as definições de diagnóstico. |
> | Microsoft.Insights/eventtypes/* | Lista de eventos de Registo de Atividades (eventos de gestão) numa subscrição. Esta permissão é aplicável tanto ao acesso programático como ao portal ao Registo de Atividades. |
> | Microsoft.Insights/LogDefinitions/* | Esta permissão é necessária para os utilizadores que necessitem de acesso a Registos de Atividade através do portal. Lista as categorias de registo no Registo de Atividades. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Ler definições métricas (lista dos tipos métricos disponíveis para um recurso). |
> | Microsoft.Insights/Metrics/* | Leia as métricas para um recurso. |
> | Microsoft.Insights/Register/Action | Registe o fornecedor Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Ler/escrever/excluir os testes web de Informação de Aplicação Insights. |
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
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="monitoring-metrics-publisher"></a>Editor de Métricas de Monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a publicação de métricas contra recursos Azure |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
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

## <a name="monitoring-reader"></a>Leitor de Monitorização
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler todos os dados de monitorização (métricas, registos, etc.). Ver também [Começar com funções, permissões e segurança com o Monitor Azure.](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles) |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
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

## <a name="network-contributor"></a>Colaborador de Rede
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir redes, mas não ter acesso a elas. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="new-relic-apm-account-contributor"></a>Novo contribuinte de conta APM da Relíquia
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as contas e aplicações da New Relic Application Performance Management, mas não o acesso às mesmas. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
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

## <a name="policy-insights-data-writer-preview"></a>Policy Insights Data Writer (Pré-visualização)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler o acesso às políticas de recursos e escrever acesso a eventos de política de componentes de recursos. |
> | **Id** | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
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

## <a name="reader-and-data-access"></a>Acesso a Leitores e Dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe ver tudo, mas não permitirá que apague ou crie uma conta de armazenamento ou recursos contidos. Também permitirá o acesso de leitura/escrita a todos os dados contidos numa conta de armazenamento através do acesso às chaves da conta de armazenamento. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
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

## <a name="redis-cache-contributor"></a>Redis Cache Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir caches Redis, mas não ter acesso a eles. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Cache/redis/* | Criar e gerir caches Redis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="resource-policy-contributor"></a>Contribuidor de Política de Recursos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Utilizadores com direitos de criar/modificar a política de recursos, criar bilhetes de apoio e ler recursos/hierarquia. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
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

## <a name="scheduler-job-collections-contributor"></a>Colaborador de Coleções de Emprego do Scheduler
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as coleções de emprego do Scheduler, mas não ter acesso a elas. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="search-service-contributor"></a>Colaborador de Serviço de Pesquisa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os serviços de pesquisa, mas não aceder aos mesmos. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="security-admin"></a>Administrador de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, descartar alertas e recomendações. |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerir atribuições políticas |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerir definições políticas |
> | Microsoft.Authorization/policySetDefinitions/* | Criar e gerir conjuntos de políticas |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
> | Microsoft.Management/managementGroups/read | Lista de grupos de gestão para o utilizador autenticado. |
> | Microsoft.operationalInsights/workspaces/*/read | Ver dados de análise de registo |
> | Microsoft.Resources/deployments/* | Criar e gerir implementações de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="security-assessment-contributor"></a>Contribuinte de Avaliação de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe levar avaliações para o Centro de Segurança |
> | **Id** | 612c2a1-cb24-443b-ac28-3ab7272de6f5 |
> | **Ações** |  |
> | Microsoft.Segurança/avaliações/escrita | Crie ou atualize avaliações de segurança na sua subscrição |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="security-manager-legacy"></a>Gestor de Segurança (Legado)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Este é um papel legado. Por favor, use o Administrador de Segurança em vez disso. |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.ClassicCompute/*/read | Ler informações de configuração máquinas virtuais clássicas |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Escreva configuração para máquinas virtuais clássicas |
> | Microsoft.ClassicNetwork/*/read | Leia informações de configuração sobre a rede clássica |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="security-reader"></a>Leitor de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ver recomendações e alertas, ver políticas de segurança, ver estados de segurança, mas não pode fazer alterações. |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="site-recovery-contributor"></a>Colaborador de Recuperação do Site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o serviço de recuperação do site, exceto criação de cofre e atribuição de papéis |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="site-recovery-operator"></a>Operador de recuperação do site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe falhar e falhar, mas não realizar outras operações de gestão de recuperação do site |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="site-recovery-reader"></a>Leitor de Recuperação do Site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe visualizar o estado de Recuperação do Site, mas não realizar outras operações de gestão |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
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

## <a name="spatial-anchors-account-contributor"></a>Contribuinte da conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir âncoras espaciais na sua conta, mas não eliminá-las |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
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

## <a name="spatial-anchors-account-owner"></a>Proprietário da conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir âncoras espaciais na sua conta, incluindo apagando-as |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
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

## <a name="spatial-anchors-account-reader"></a>Leitor de conta de âncoras espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite localizar e ler propriedades de âncoras espaciais na sua conta |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
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

## <a name="sql-db-contributor"></a>Contribuinte SQL DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as bases de dados SQL, mas não ter acesso a elas. Além disso, não é possível gerir as suas políticas relacionadas com a segurança ou os servidores SQL dos seus pais. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler papéis e atribuições de papéis |
> | Microsoft.Insights/alertRules/* | Criar e gerir regras de alerta |
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

## <a name="sql-managed-instance-contributor"></a>Contribuinte de instância gerida SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir instâncias geridas pelo SQL e configurar a rede necessária, mas não pode dar acesso a outros. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
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

## <a name="sql-security-manager"></a>Gestor de Segurança SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir as políticas relacionadas com a segurança dos servidores e bases de dados SQL, mas não o acesso aos mesmos. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leia a autorização da Microsoft |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | Ler registos de auditoria |
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

## <a name="sql-server-contributor"></a>Contribuinte do Servidor SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir servidores e bases de dados SQL, mas não aceder aos mesmos, e não às suas políticas relacionadas com a segurança. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | Ler registos de auditoria |
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

## <a name="storage-account-contributor"></a>Contribuinte de Conta de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a gestão de contas de armazenamento. Fornece acesso à chave da conta, que pode ser usada para aceder a dados através de autorização de chave partilhada. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Leia toda a autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerir as regras de alerta insights |
> | Microsoft.Insights/diagnosticSettings/* | Gerir as definições de diagnóstico |
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

## <a name="storage-account-key-operator-service-role"></a>Função de serviço de serviço de operador chave de conta de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite a listagem e regeneração das chaves de acesso à conta de armazenamento. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso da conta de armazenamento especificada. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="storage-blob-data-contributor"></a>Contribuinte de dados blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia, escreva e elimine os recipientes de armazenamento e bolhas do Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
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

## <a name="storage-blob-data-owner"></a>Proprietário de dados blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Fornece acesso total aos recipientes e dados de blob de armazenamento Azure, incluindo a atribuição de controlo de acesso POSIX. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Permissões completas em contentores. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço Blob. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Permissões completas em bolhas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="storage-blob-data-reader"></a>Leitor de dados blob de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia e enumere os recipientes de armazenamento e bolhas azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolva um recipiente ou uma lista de recipientes. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço Blob. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolva uma bolha ou uma lista de bolhas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="storage-blob-delegator"></a>Depósito Blob Delegator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Obtenha uma chave de delegação de utilizadores, que pode ser usada para criar uma assinatura de acesso partilhado para um recipiente ou bolha que é assinada com credenciais Azure AD. Para mais informações, consulte [Criar uma delegação de utilizadores SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço Blob. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Colaborador de partilha de dados de ficheiros de armazenamento SMB Share
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler, escrever e excluir o acesso a ficheiros/diretórios em ações de ficheiros Azure. Esta função não tem equivalente incorporado nos servidores de ficheiros do Windows. |
> | **Id** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
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

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Relatório de armazenamento Dados SMB Share Colaborador Elevado
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite ler, escrever, excluir e modificar ACLs em ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de alteração nos servidores de ficheiros do Windows. |
> | **Id** | a7264617-510b-434b-a828-9731dc254ea7 |
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

## <a name="storage-file-data-smb-share-reader"></a>Armazenamento Arquivo Data SMB Share Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite o acesso a ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de leitura nos servidores de ficheiros do Windows. |
> | **Id** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Devolve um ficheiro/pasta ou uma lista de ficheiros/pastas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="storage-queue-data-contributor"></a>Contribuinte de dados da fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia, escreva e elimine as filas de armazenamento do Azure e as mensagens de fila. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
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

## <a name="storage-queue-data-message-processor"></a>Processador de dados de fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Espreite, recupere e apague uma mensagem de uma fila de Armazenamento Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Olha uma mensagem. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Recuperar e apagar uma mensagem. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="storage-queue-data-message-sender"></a>Remetente de mensagem de dados de fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Adicione mensagens a uma fila de Armazenamento Azure. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="storage-queue-data-reader"></a>Leitor de dados da fila de armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Leia e enumere as filas de armazenamento azure e as mensagens de fila. Para saber que ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamadas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)de operações de dados blob e fila . |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Espreite ou recupere uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="support-request-contributor"></a>Colaborador de Pedido de Apoio
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe criar e gerir pedidos de suporte |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | Microsoft.Support/* | Criar e gerir bilhetes de apoio |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="traffic-manager-contributor"></a>Colaborador do Gestor de Tráfego
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os perfis do Traffic Manager, mas não permite controlar quem tem acesso aos mesmos. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
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

## <a name="user-access-administrator"></a>Administrador de Acesso de Utilizador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir o acesso dos utilizadores aos recursos do Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
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

## <a name="virtual-machine-administrator-login"></a>Login do administrador de máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ver Máquinas Virtuais no portal e iniciar sessão como administrador |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
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

## <a name="virtual-machine-contributor"></a>Colaborador de Máquina Virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir máquinas virtuais, mas não aceder a elas, e não a rede virtual ou a conta de armazenamento a que estão ligadas. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
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

## <a name="virtual-machine-user-login"></a>Login de utilizador de máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Ver Máquinas Virtuais no portal e iniciar sessão como um utilizador regular. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
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

## <a name="web-plan-contributor"></a>Colaborador do Plano Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir os planos web para websites, mas não ter acesso aos mesmos. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
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

## <a name="website-contributor"></a>Colaborador do Site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Permite-lhe gerir websites (não planos web), mas não ter acesso aos mesmos. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
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

## <a name="workbook-contributor"></a>Contribuinte do livro
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode salvar livros partilhados. |
> | **Id** | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
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

## <a name="workbook-reader"></a>Leitor de livro
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrição** | Pode ler livros. |
> | **Id** | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Ações** |  |
> | microsoft.insights/books/read | Leia um livro |
> | **NotAções** |  |
> | *nenhum* |  |
> | **Ações de Dados** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

## <a name="next-steps"></a>Passos seguintes

- [Prestador de recursos de correspondência para o serviço](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Permissions in Azure Security Center](../security-center/security-center-permissions.md) (Permissões no Centro de Segurança do Azure)
