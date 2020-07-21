---
title: Funções azure incorporadas - Azure RBAC
description: Este artigo descreve os papéis incorporados do Azure para o controlo de acesso baseado em funções Azure (Azure RBAC). Ele lista Ações, NotActions, DataActions e NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 07/16/2020
ms.openlocfilehash: c0a397ac71c7c466145357093b6c22f78e627d1c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511590"
---
# <a name="azure-built-in-roles"></a>Funções incorporadas do Azure

[O controlo de acesso baseado em funções (Azure RBAC)](overview.md) tem várias funções incorporadas Azure que pode atribuir aos utilizadores, grupos, diretores de serviços e identidades geridas. As atribuições de papéis são a forma como controlas o acesso aos recursos da Azure. Se as funções incorporadas não corresponderem às necessidades específicas da sua organização, pode criar os seus [próprios papéis personalizados Azure.](custom-roles.md)

Este artigo lista os papéis incorporados do Azure, que estão sempre a evoluir. Para obter as funções mais recentes, utilize a lista [de definição de funções Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [az](/cli/azure/role/definition#az-role-definition-list). Se estiver à procura de funções de administrador para o Azure Ative Directory (Azure AD), consulte [permissões de função de administrador no Diretório Ativo Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

A tabela seguinte fornece uma breve descrição e a identificação única de cada papel incorporado. Clique no nome da função para ver a lista de `Actions` , , e para cada `NotActions` `DataActions` `NotDataActions` papel. Para obter informações sobre o que estas ações significam e como se aplicam aos aviões de gestão e dados, consulte as definições de [funções do Understand Azure](role-definitions.md).

## <a name="all"></a>Todos

> [!div class="mx-tableFixed"]
> | Papel incorporado | Descrição | ID |
> | --- | --- | --- |
> | **Geral** |  |  |
> | [Contribuinte](#contributor) | Permite-lhe gerir tudo, exceto conceder acesso aos recursos. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Proprietário](#owner) | Permite-lhe gerir tudo, incluindo o acesso aos recursos. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Leitor](#reader) | Permite-lhe ver tudo, mas não fazer alterações. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrador de Acesso do Utilizador](#user-access-administrator) | Permite-lhe gerir o acesso do utilizador aos recursos Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Computação** |  |  |
> | [Colaborador clássico da máquina virtual](#classic-virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais clássicas, mas não acesso a elas, e não à rede virtual ou à conta de armazenamento a que estão ligadas. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Login de administrador de máquina virtual](#virtual-machine-administrator-login) | Ver Máquinas Virtuais no portal e iniciar sessão como administrador | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Contribuidor de Máquina Virtual](#virtual-machine-contributor) | Permite-lhe gerir máquinas virtuais, mas não acesso a elas, e não à rede virtual ou à conta de armazenamento a que estão ligadas. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Início de sessão do utilizador de máquina virtual](#virtual-machine-user-login) | Ver Máquinas Virtuais no portal e iniciar sessão como um utilizador regular. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Redes** |  |  |
> | [Colaborador de Ponto Final cdn](#cdn-endpoint-contributor) | Pode gerir os pontos finais da CDN, mas não pode dar acesso a outros utilizadores. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Leitor de ponto final CDN](#cdn-endpoint-reader) | Pode ver os pontos finais do CDN, mas não pode fazer alterações. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Colaborador de perfil CDN](#cdn-profile-contributor) | Pode gerir perfis de CDN e seus pontos finais, mas não pode dar acesso a outros utilizadores. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Leitor de perfil CDN](#cdn-profile-reader) | Pode ver perfis de CDN e seus pontos finais, mas não pode fazer alterações. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Contribuidor de Rede Clássica](#classic-network-contributor) | Permite-lhe gerir redes clássicas, mas não acesso a elas. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Colaborador da Zona DNS](#dns-zone-contributor) | Permite gerir zonas DNS e recordes em Azure DNS, mas não permite controlar quem tem acesso a elas. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Contribuidor de Rede](#network-contributor) | Permite-lhe gerir as redes, mas não ter acesso a elas. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Colaborador privado da zona do DNS](#private-dns-zone-contributor) | Permite-lhe gerir recursos privados de zona de DNS, mas não as redes virtuais a que estão ligadas. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Gestor de Tráfego Colaborador](#traffic-manager-contributor) | Permite-lhe gerir perfis de Gestor de Tráfego, mas não permite que controle quem tem acesso a eles. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Armazenamento** |  |  |
> | [Contribuidor Avere](#avere-contributor) | Pode criar e gerir um cluster Avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operador avere](#avere-operator) | Usado pelo cluster Avere vFXT para gerir o cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Colaborador de backup](#backup-contributor) | Permite-lhe gerir o serviço de backup, mas não pode criar cofres e dar acesso a outros | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operador de backup](#backup-operator) | Permite-lhe gerir serviços de backup, exceto remoção de backup, criação de cofre e dar acesso a outros | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Leitor de reserva](#backup-reader) | Pode ver serviços de backup, mas não pode fazer alterações | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Colaborador clássico da conta de armazenamento](#classic-storage-account-contributor) | Permite-lhe gerir contas de armazenamento clássicas, mas não acesso a elas. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Papel clássico do serviço do operador chave de armazenamento de armazenamento](#classic-storage-account-key-operator-service-role) | Os operadores clássicos da conta de armazenamento são autorizados a listar e regenerar chaves em contas de armazenamento clássicas | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Contribuinte da Caixa de Dados](#data-box-contributor) | Permite-lhe gerir tudo no âmbito do Serviço de Caixa de Dados, exceto dar acesso a outros. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Leitor de caixa de dados](#data-box-reader) | Permite-lhe gerir o Serviço de Caixa de Dados, exceto criar detalhes de encomenda ou edição e dar acesso a outros. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Desenvolvedor de Análise de Lago de Dados](#data-lake-analytics-developer) | Permite submeter, monitorizar e gerir os seus próprios trabalhos, mas não criar ou apagar contas data lake analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Leitor e Acesso a Dados](#reader-and-data-access) | Permite-lhe ver tudo, mas não permitirá que elimine ou crie uma conta de armazenamento ou recurso contido. Também permitirá o acesso de leitura/escrita a todos os dados contidos numa conta de armazenamento através do acesso às chaves da conta de armazenamento. | c12c16-33a1-487b-954d-41c89c60f349 |
> | [Contribuidor de Conta de Armazenamento](#storage-account-contributor) | Permite a gestão das contas de armazenamento. Fornece acesso à chave da conta, que pode ser usada para aceder a dados através da autorização da Chave Partilhada. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Papel de serviço chave de operador de conta de armazenamento](#storage-account-key-operator-service-role) | Permite a listagem e regeneração das chaves de acesso à conta de armazenamento. | 81aa9662b-bebf-436f-a333-f67b29880f12 |
> | [Colaborador de dados blob de armazenamento](#storage-blob-data-contributor) | Leia, escreva e elimine os recipientes e bolhas de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Proprietário de dados blob de armazenamento](#storage-blob-data-owner) | Fornece acesso total aos recipientes e dados blob de armazenamento Azure, incluindo a atribuição do controlo de acesso POSIX. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Leitor de dados blob de armazenamento](#storage-blob-data-reader) | Leia e enuncie os recipientes e bolhas de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegado blob de armazenamento](#storage-blob-delegator) | Obtenha uma chave de delegação de utilizador, que pode ser usada para criar uma assinatura de acesso partilhado para um recipiente ou bolha que é assinado com credenciais AZure AD. Para mais informações, consulte [Criar uma delegação de utilizador SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Contribuinte de Partilhas SMB de Dados de Ficheiros de Armazenamento](#storage-file-data-smb-share-contributor) | Permite ler, escrever e eliminar o acesso em ficheiros/diretórios em ações de ficheiros Azure. Esta função não tem equivalente incorporado nos servidores de ficheiros Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Contribuinte Elevado de Partilhas SMB de Dados de Ficheiros de Armazenamento](#storage-file-data-smb-share-elevated-contributor) | Permite ler, escrever, excluir e modificar ACLs em ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de alteração nos servidores de ficheiros do Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Leitor de Partilhas SMB de Dados de Ficheiros de Armazenamento](#storage-file-data-smb-share-reader) | Permite o acesso de leitura em ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de leitura em servidores de ficheiros Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Colaborador de dados de fila de armazenamento](#storage-queue-data-contributor) | Leia, escreva e elimine as filas de armazenamento do Azure e as mensagens de fila. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855d0fb88 |
> | [Processador de mensagens de dados de fila de armazenamento](#storage-queue-data-message-processor) | Espreite, recupere e elimine uma mensagem de uma fila de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Remetente de mensagem de dados de fila de armazenamento](#storage-queue-data-message-sender) | Adicione mensagens a uma fila de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Leitor de dados de fila de armazenamento](#storage-queue-data-reader) | Leia e enuma as filas de armazenamento do Azure e as mensagens de fila. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Leitor de dados Azure Maps](#azure-maps-data-reader) | Concede acesso a ler dados relacionados com mapas a partir de uma conta de mapas Azure. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Colaborador do Serviço de Pesquisa](#search-service-contributor) | Permite-lhe gerir os serviços de Pesquisa, mas não o acesso aos mesmos. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Colaborador do Plano Web](#web-plan-contributor) | Permite-lhe gerir os planos web para sites, mas não acesso aos mesmos. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0f0a4b |
> | [Colaborador do Site](#website-contributor) | Permite-lhe gerir websites (não planos web), mas não acesso aos mesmos. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Contentores** |  |  |
> | [AcrDelete](#acrdelete) | acr eliminar | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr sinaleiro de imagem | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [Rio AcrPull](#acrpull) | acr puxar | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | impulso acr | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | leitor de dados de quarentena acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [Escritor de AcrQuarantineWriter](#acrquarantinewriter) | acr escritor de dados de quarentena | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service Cluster Função de Administrador](#azure-kubernetes-service-cluster-admin-role) | Lista de ação de credencial de administrador de agrupamento. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Papel de utilizador do cluster de serviço Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Listar ação credencial do utilizador do cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Papel de Colaborador de Serviço Azure Kubernetes](#azure-kubernetes-service-contributor-role) | Concede acesso à leitura e escreva clusters de serviçoS Azure Kubernetes | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Serviço RBAC Administrador](#azure-kubernetes-service-rbac-admin) | Permite-lhe gerir todos os recursos em cluster/namespace, exceto atualizar ou eliminar quotas de recursos e espaços de nome. | 3498e952-d568-435e-9b2c-8d77e338d7f7f7 |
> | [Azure Kubernetes Serviço RBAC Cluster Admin](#azure-kubernetes-service-rbac-cluster-admin) | Permite-lhe gerir todos os recursos do cluster. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC Reader](#azure-kubernetes-service-rbac-reader) | Permite-lhe ver todos os recursos no cluster/namespace, exceto segredos. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC Writer](#azure-kubernetes-service-rbac-writer) | Permite-lhe atualizar tudo no cluster/namespace, exceto quotas de recursos, espaços de nome, políticas de segurança de pod, pedidos de assinatura de certificados, (cluster)funções e (cluster)role bindings. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Bases de dados** |  |  |
> | [Papel do leitor de conta de cosm de Cosmos DB](#cosmos-db-account-reader-role) | Pode ler os dados da conta DB da Azure Cosmos. Consulte [o Contribuinte de Conta DocumentDB](#documentdb-account-contributor) para gerir as contas DB da Azure Cosmos. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operador de DB cosmos](#cosmos-db-operator) | Permite-lhe gerir as contas DB da Azure Cosmos, mas não aceder aos dados nelas. Impede o acesso às chaves da conta e às cordas de ligação. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Pode submeter pedido de restauro para uma base de dados Cosmos DB ou um recipiente para uma conta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Colaborador de Conta DocumentDB](#documentdb-account-contributor) | Pode gerir as contas de DB da Azure Cosmos. Azure Cosmos DB é anteriormente conhecido como DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Contribuidor redis cache](#redis-cache-contributor) | Permite-te gerir caches Redis, mas não acesso a eles. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Colaborador do SQL DB](#sql-db-contributor) | Permite-lhe gerir bases de dados SQL, mas não acesso a elas. Além disso, não é possível gerir as suas políticas relacionadas com a segurança ou os seus servidores SQL. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Colaborador de instância gerida da SQL](#sql-managed-instance-contributor) | Permite-lhe gerir as Ocorrências Geridas SQL e a configuração de rede necessária, mas não pode dar acesso a outros. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gestor de Segurança SQL](#sql-security-manager) | Permite-lhe gerir as políticas relacionadas com a segurança dos servidores e bases de dados SQL, mas não o acesso aos mesmos. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Colaborador do Servidor SQL](#sql-server-contributor) | Permite-lhe gerir servidores e bases de dados SQL, mas não acesso aos mesmos, e não às suas políticas relacionadas com a segurança. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Análise** |  |  |
> | [Proprietário de dados Azure Event Hubs](#azure-event-hubs-data-owner) | Permite o acesso total aos recursos do Azure Event Hubs. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Recetor de dados Azure Event Hubs](#azure-event-hubs-data-receiver) | Permite ter acesso aos recursos do Azure Event Hubs. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Ender de dados do Azure Event Hubs](#azure-event-hubs-data-sender) | Permite enviar acesso aos recursos do Azure Event Hubs. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Contribuinte da Fábrica de Dados](#data-factory-contributor) | Criar e gerir fábricas de dados, bem como recursos infantis dentro delas. | 673868a-7521-48a0-acc6-0f60742d39f5 |
> | [Purgador de dados](#data-purger) | Pode purgar dados de análise | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operador de cluster HDInsight](#hdinsight-cluster-operator) | Permite-lhe ler e modificar configurações de cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HdInsight Domain Services Colaborador](#hdinsight-domain-services-contributor) | Pode ler, criar, modificar e eliminar as operações relacionadas com os serviços de domínio necessárias para o pacote de segurança da empresa HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Contribuidor do Log Analytics](#log-analytics-contributor) | Log Analytics Contributor pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VM; leitura das chaves da conta de armazenamento para poder configurar a recolha de registos do Azure Storage; criar e configurar contas de automação; adição de soluções; e configurar diagnósticos Azure em todos os recursos da Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Leitor do Log Analytics](#log-analytics-reader) | O Log Analytics Reader pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração dos diagnósticos Azure em todos os recursos do Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Blockchain** |  |  |
> | [Acesso ao nó do membro blockchain (visualização)](#blockchain-member-node-access-preview) | Permite o acesso aos nóns do Membro Blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **IA + aprendizagem automática** |  |  |
> | [Colaborador dos Serviços Cognitivos](#cognitive-services-contributor) | Permite criar, ler, atualizar, eliminar e gerir chaves dos Serviços Cognitivos. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Leitor de dados de serviços cognitivos (pré-visualização)](#cognitive-services-data-reader-preview) | Permite-lhe ler os dados dos Serviços Cognitivos. | b59867f0-fa02-499b-be73-45a86b3e1c |
> | [Utilizador de Serviços Cognitivos](#cognitive-services-user) | Permite-lhe ler e listar as chaves dos Serviços Cognitivos. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Realidade mista** |  |  |
> | [Administrador de renderização remota](#remote-rendering-administrator) | Fornece ao utilizador capacidades de conversão, gestão de sessão, renderização e diagnóstico para renderização remota do Azure | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Cliente de renderização remota](#remote-rendering-client) | Fornece ao utilizador capacidades de gestão, renderização e diagnóstico para renderização remota Azure. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Contribuinte da Conta De Âncoras Espaciais](#spatial-anchors-account-contributor) | Permite-lhe gerir âncoras espaciais na sua conta, mas não apagá-las | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Proprietário de conta de âncoras espaciais](#spatial-anchors-account-owner) | Permite-lhe gerir âncoras espaciais na sua conta, incluindo apagá-las | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Leitor de conta de âncoras espaciais](#spatial-anchors-account-reader) | Permite localizar e ler propriedades de âncoras espaciais na sua conta | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integração** |  |  |
> | [Colaborador do Serviço de Gestão da API](#api-management-service-contributor) | Pode gerir o serviço e as APIs | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Função do operador de serviço de gestão da API](#api-management-service-operator-role) | Pode gerir o serviço, mas não as APIs | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Função de leitor de serviço de gestão da API](#api-management-service-reader-role) | Acesso apenas de leitura ao serviço e APIs | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Proprietário de dados de configuração de aplicativos](#app-configuration-data-owner) | Permite o acesso total aos dados de Configuração de Aplicações. | 5ae67d6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Leitor de dados de configuração de aplicativos](#app-configuration-data-reader) | Permite ler o acesso aos dados de Configuração de Aplicações. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Proprietário de dados de ônibus de serviço Azure](#azure-service-bus-data-owner) | Permite o acesso total aos recursos do Azure Service Bus. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Recetor de dados de ônibus da Azure Service](#azure-service-bus-data-receiver) | Permite ter acesso aos recursos do Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a22247e0 |
> | [Remetente de dados de ônibus de serviço Azure](#azure-service-bus-data-sender) | Permite enviar acesso aos recursos do Azure Service Bus. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Proprietário de registo de pilha de Azure](#azure-stack-registration-owner) | Permite-lhe gerir as inscrições do Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Colaborador de EventosGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Permite-lhe gerir as operações de subscrição de eventos EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Leitor de subscrição de eventos Com EventosGrid](#eventgrid-eventsubscription-reader) | Permite-lhe ler subscrições de eventosGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Colaborador de dados da FHIR](#fhir-data-contributor) | A função permite ao utilizador ou principal acesso total aos dados do FHIR | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [Exportador de dados FHIR](#fhir-data-exporter) | Função permite ao utilizador ou principal ler e exportar dados fHIR | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Leitor de dados FHIR](#fhir-data-reader) | A função permite ao utilizador ou principal ler dados do FHIR | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [Escritor de dados da FHIR](#fhir-data-writer) | A função permite ao utilizador ou ao principal ler e escrever Dados FHIR | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Colaborador do Ambiente do Serviço de Integração](#integration-service-environment-contributor) | Permite-lhe gerir ambientes de serviços de integração, mas não acesso aos mesmos. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Desenvolvedor de Ambiente de Serviço de Integração](#integration-service-environment-developer) | Permite que os desenvolvedores criem e atualizem fluxos de trabalho, contas de integração e conexões API em ambientes de serviços de integração. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Contribuinte de Conta de Sistemas Inteligentes](#intelligent-systems-account-contributor) | Permite-lhe gerir contas de Sistemas Inteligentes, mas não acesso a elas. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Contribuidor de Aplicativos Lógicos](#logic-app-contributor) | Permite-lhe gerir aplicações lógicas, mas não alterar o acesso às suas. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operador de aplicativos lógica](#logic-app-operator) | Permite-lhe ler, ativar e desativar aplicações lógicas, mas não editá-las ou atualizá-las. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identidade** |  |  |
> | [Colaborador de Identidade Gerido](#managed-identity-contributor) | Criar, Ler, Atualizar e Eliminar Identidade Atribuída ao Utilizador | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operador de identidade gerido](#managed-identity-operator) | Ler e atribuir identidade atribuída ao utilizador | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Segurança** |  |  |
> | [Colaborador Azure Sentinel](#azure-sentinel-contributor) | Colaborador Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Reader](#azure-sentinel-reader) | Azure Sentinel Reader | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Contribuidor do Cofre Chave](#key-vault-contributor) | Permite-lhe gerir cofres chave, mas não acesso a eles. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Administrador de Segurança](#security-admin) | Ver e atualizar permissões para o Centro de Segurança. As mesmas permissões que a função de Leitor de Segurança também podem atualizar a política de segurança e rejeitar alertas e recomendações. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Contribuinte de Avaliação de Segurança](#security-assessment-contributor) | Permite-lhe fazer avaliações para o Centro de Segurança | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gestor de Segurança (Legado)](#security-manager-legacy) | Este é um papel antigo. Por favor, use o Administrador de Segurança. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Leitor de Segurança](#security-reader) | Ver permissões para o Centro de Segurança. Pode ver recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Utilizador de Laboratórios DevTest](#devtest-labs-user) | Permite ligar, iniciar, reiniciar e desligar as suas máquinas virtuais nos seus Laboratórios Azure DevTest. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Criador de Laboratório](#lab-creator) | Permite criar novos laboratórios sob as suas contas do Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitorização** |  |  |
> | [Contribuinte componente de insights de aplicação](#application-insights-component-contributor) | Pode gerir componentes de Insights de Aplicação | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Insights de aplicação Snapshot Debugger](#application-insights-snapshot-debugger) | Dá permissão ao utilizador para visualizar e descarregar instantâneos de depurg recolhidos com o Debugger Debugger Do Snapshot da Aplicação. Note que estas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Contribuinte.](#contributor) Ao atribuir aos utilizadores o papel de Debugger Snapshot Debugger do Application Insights, deve conceder a função diretamente ao utilizador. O papel não é reconhecido quando é adicionado a um papel personalizado. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Colaborador de monitorização](#monitoring-contributor) | Pode ler todos os dados de monitorização e editar as definições de monitorização. Ver também [Começar com papéis, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Editor de Métricas de Monitorização](#monitoring-metrics-publisher) | Permite publicar métricas contra recursos Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Leitor de Monitorização](#monitoring-reader) | Pode ler todos os dados de monitorização (métricas, registos, etc.). Ver também [Começar com papéis, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Contribuinte do livro](#workbook-contributor) | Pode salvar livros partilhados. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Leitor de livros de trabalho](#workbook-reader) | Pode ler livros. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Gestão + governação** |  |  |
> | [Operador de emprego de automação](#automation-job-operator) | Criar e Gerir Empregos utilizando livros de automação. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operador de Automação](#automation-operator) | Operadores de automação podem iniciar, parar, suspender e retomar postos de trabalho | d3881f73-407a-4167-8283-e981cbba04 |
> | [Operador de runbook de automação](#automation-runbook-operator) | Leia as propriedades do Runbook - para ser capaz de criar Jobs of the runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Máquina conectada Azure a bordo](#azure-connected-machine-onboarding) | Pode embarcar máquinas ligadas a Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrador de recursos de máquina conectada Azure](#azure-connected-machine-resource-administrator) | Pode ler, escrever, apagar e voltar a bordo máquinas ligadas a azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Leitor de Faturação](#billing-reader) | Permite ler acesso a dados de faturação | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Contribuidor de Projeto](#blueprint-contributor) | Pode gerir definições de planta, mas não atribuí-las. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operador de blueprint](#blueprint-operator) | Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. Note que isto só funciona se a atribuição for feita com uma identidade gerida atribuída pelo utilizador. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Contribuidor para a Gestão de Custos](#cost-management-contributor) | Pode ver os custos e gerir a configuração dos custos (por exemplo, orçamentos, exportações) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Leitor de Gestão de Custos](#cost-management-reader) | Pode ver dados de custos e configuração (por exemplo, orçamentos, exportações) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Administrador de Definições de Hierarquia](#hierarchy-settings-administrator) | Permite aos utilizadores editar e eliminar Definições de Hierarquia | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Papel de Contribuinte de Aplicação Gerido](#managed-application-contributor-role) | Permite criar recursos de aplicação geridos. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Papel do operador de aplicação gerido](#managed-application-operator-role) | Permite-lhe ler e executar ações sobre recursos de aplicação gerida | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Leitor de Aplicações Geridas](#managed-applications-reader) | Permite-lhe ler recursos numa aplicação gerida e solicitar acesso ao JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Atribuição de registo de serviços geridos Eliminar Função](#managed-services-registration-assignment-delete-role) | Atribuição de Registo de Serviços Geridos Eliminar Função permite que os utilizadores inquilinos gestores apaguem a atribuição de inscrição atribuída ao seu inquilino. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Colaborador do Grupo de Gestão](#management-group-contributor) | Função contributiva do Grupo de Gestão | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Leitor de Grupo de Gestão](#management-group-reader) | Função de leitor de grupo de gestão | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Novo contribuinte da conta APM da relíquia](#new-relic-apm-account-contributor) | Permite-lhe gerir as contas e aplicações de Gestão de Desempenho de Aplicações Novas Relíquias, mas não o acesso às contas. | 5d28c62d-5b37-4476-8438-e5877788df237 |
> | [Escritor de dados de insights de política (pré-visualização)](#policy-insights-data-writer-preview) | Permite ler o acesso às políticas de recursos e escrever acesso a eventos de política de componentes de recursos. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Contribuidor de Política de Recursos](#resource-policy-contributor) | Utilizadores com direitos de criar/modificar a política de recursos, criar bilhete de apoio e ler recursos/hierarquia. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Colaborador de Recuperação de Sítios](#site-recovery-contributor) | Permite-lhe gerir o serviço de Recuperação de Sítios, exceto a criação de abóbada e atribuição de funções | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operador de Recuperação de Sítio](#site-recovery-operator) | Permite-lhe falhar e falhar, mas não realizar outras operações de gestão de Recuperação de Sítios | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Leitor de Recuperação de Site](#site-recovery-reader) | Permite-lhe ver o estado de Recuperação do Site, mas não realizar outras operações de gestão | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Colaborador de Pedido de Apoio](#support-request-contributor) | Permite criar e gerir pedidos de Suporte | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Colaborador da Tag](#tag-contributor) | Permite-lhe gerir etiquetas em entidades, sem fornecer acesso às próprias entidades. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Outros** |  |  |
> | [Colaborador bizTalk](#biztalk-contributor) | Permite-lhe gerir os serviços BizTalk, mas não ter acesso aos mesmos. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Utilizador de Virtualização de Desktop](#desktop-virtualization-user) | Permite ao utilizador utilizar as aplicações num grupo de aplicações. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Colaborador de Coleções de Trabalho do Programador](#scheduler-job-collections-contributor) | Permite-lhe gerir as coleções de emprego do Scheduler, mas não ter acesso a elas. | 188a0f2f-5c9e-469b-ae67-2aaa5ce574b94 |


## <a name="general"></a>Geral


### <a name="contributor"></a>Contribuinte

Permite-lhe gerir tudo, exceto conceder acesso aos recursos. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | * | Criar e gerir recursos de todos os tipos |
> | **NotActions** |  |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/Eliminar | Eliminar funções, atribuições de políticas, definições de políticas e definições de definições de definições de definições de definições de definições |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/Write | Criar funções, atribuições de funções, atribuições de políticas, definições de políticas e definições de definições de definições de definições de definições de definições de definições de definições de definições de definições de definições de definições de definições de |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Concede ao chamador o acesso de Administrador de Acesso de Utilizador no âmbito do inquilino |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Criar ou atualizar quaisquer atribuições de plantas |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Excluir quaisquer atribuições de plantas |
> | **DataActions** |  |
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

Permite-lhe gerir tudo, incluindo o acesso aos recursos. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | * | Criar e gerir recursos de todos os tipos |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite-lhe ver tudo, mas não fazer alterações. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="user-access-administrator"></a>Administrador de Acesso dos Utilizadores

Permite-lhe gerir o acesso do utilizador aos recursos Azure. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/* | Gerir a autorização |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="compute"></a>Computação


### <a name="classic-virtual-machine-contributor"></a>Colaborador clássico da máquina virtual

Permite-lhe gerir máquinas virtuais clássicas, mas não acesso a elas, e não à rede virtual ou à conta de armazenamento a que estão ligadas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Criar e gerir nomes clássicos de domínio computacional |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Criar e gerir máquinas virtuais |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Link um Ip reservado |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Obtém as Ips reservadas |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Junta-se à rede virtual. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Pegue a rede virtual. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAcounts/discos/read | Devolve o disco da conta de armazenamento. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | Devolve a imagem da conta de armazenamento. (Precatado. Utilizar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Devolva a conta de armazenamento com a conta dada. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="virtual-machine-administrator-login"></a>Login de administrador de máquina virtual

Ver Máquinas Virtuais no portal e iniciar sessão como administrador [Saiba mais](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Obtém uma definição de equilibrador de carga |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Faça login numa máquina virtual como utilizador regular |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Inicie sessão numa máquina virtual com o administrador do Windows ou os privilégios do utilizador de raiz Linux |
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

### <a name="virtual-machine-contributor"></a>Contribuidor de Máquina Virtual

Permite-lhe gerir máquinas virtuais, mas não acesso a elas, e não à rede virtual ou à conta de armazenamento a que estão ligadas. [Saiba mais](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Criar e gerir conjuntos de disponibilidade de cálculo |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Criar e gerir localizações de computação |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Criar e gerir máquinas virtuais |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Criar e gerir conjuntos de dimensionamento de máquinas virtuais |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/discos/write | Cria um novo Disco ou atualiza um existente |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/discos/read | Obtenha as propriedades de um disco |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/discos/delete | Elimina o Disco |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/agendas/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Junta-se a um conjunto de endereços de backend gateway de aplicação. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Junta-se a um conjunto de endereços de backend de reequilibrador de carga. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Junta-se a uma piscina NAT de entrada de balançador de carga. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Junta-se a uma regra de entrada de natação de carregamento. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/sondas/join/action | Permite a utilização de sondas de um equilibrador de carga. Por exemplo, com esta permissão saúdeProbe propriedade de escala VM definida pode referenciar a sonda. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Obtém uma definição de equilibrador de carga |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Criar e gerir localizações de rede |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Criar e gerir interfaces de rede |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Junta-se a um grupo de segurança de rede. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Obtém uma definição de grupo de segurança de rede |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Junta-se a um endereço IP público. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não é alerta. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionInt/write | Criar uma intenção de proteção de reserva |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve detalhes do objeto do Item Protegido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de reserva |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Devolve todas as Políticas de Proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Cria Política de Proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A operação Get Vault obtém um objeto que representa o recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/utilizações/ler | Devolve detalhes de utilização para um Cofre de Serviços de Recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Criar operação Vault cria um recurso Azure do tipo 'cofre' |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/listKeys/ação | Devolve as chaves de acesso à conta de armazenamento especificada. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/ler | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="virtual-machine-user-login"></a>Início de sessão do utilizador de máquina virtual

Ver Máquinas Virtuais no portal e iniciar sessão como um utilizador regular. [Saiba mais](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Obtém uma definição de equilibrador de carga |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Faça login numa máquina virtual como utilizador regular |
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

## <a name="networking"></a>Rede


### <a name="cdn-endpoint-contributor"></a>Colaborador de Ponto Final cdn

Pode gerir os pontos finais da CDN, mas não pode dar acesso a outros utilizadores.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/perfis/pontos finais/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Pode ver os pontos finais do CDN, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/perfis/pontos finais/*/ler |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="cdn-profile-contributor"></a>Colaborador de perfil CDN

Pode gerir perfis de CDN e seus pontos finais, mas não pode dar acesso a outros utilizadores. [Saiba mais](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/perfis/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/perfis/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="classic-network-contributor"></a>Contribuidor de Rede Clássica

Permite-lhe gerir redes clássicas, mas não acesso a elas. [Saiba mais](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Criar e gerir redes clássicas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="dns-zone-contributor"></a>Colaborador da Zona DNS

Permite gerir zonas DNS e recordes em Azure DNS, mas não permite controlar quem tem acesso a elas. [Saiba mais](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Criar e gerir zonas e registos DNS |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="network-contributor"></a>Contribuidor de Rede

Permite-lhe gerir as redes, mas não ter acesso a elas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Criar e gerir redes |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="private-dns-zone-contributor"></a>Colaborador privado da zona do DNS

Permite-lhe gerir recursos privados de zona de DNS, mas não as redes virtuais a que estão ligadas. [Saiba mais](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Junta-se a uma rede virtual. Não é alerta. |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Gestor de Tráfego Colaborador

Permite-lhe gerir perfis de Gestor de Tráfego, mas não permite que controle quem tem acesso a eles.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="storage"></a>Armazenamento


### <a name="avere-contributor"></a>Contribuidor Avere

Pode criar e gerir um cluster Avere vFXT. [Saiba mais](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/closeyPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Obtém uma definição de sub-rede de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma sub-rede. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Junta-se a um grupo de segurança de rede. Não é alerta. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/* | Criar e gerir contas de armazenamento |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/recursosGroups/recursos/read | Obtém os recursos para o grupo de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de uma bolha |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/read | Devolve uma bolha ou uma lista de bolhas |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/write | Devolve o resultado de escrever uma bolha |
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
        "Microsoft.Compute/proximityPlacementGroups/*",
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

### <a name="avere-operator"></a>Operador avere

Usado pelo cluster Avere vFXT para gerir o cluster [Saiba mais](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Obtenha as propriedades de uma máquina virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Obtém uma definição de sub-rede de rede virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Junta-se a um grupo de segurança de rede. Não é alerta. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/delete | Devolve o resultado da eliminação de um contentor |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/read | Lista de devoluções de contentores |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/write | Devolve o resultado do recipiente de bolhas de colocação |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de uma bolha |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/read | Devolve uma bolha ou uma lista de bolhas |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/write | Devolve o resultado de escrever uma bolha |
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

Permite-lhe gerir o serviço de backup, mas não pode criar cofres e dar acesso a outros [Saiba mais](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Gerir os resultados da operação na gestão de backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Criar e gerir recipientes de backup dentro de tecidos de backup do cofre dos Serviços de Recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Refresca a lista de contentores |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Criar e gerir trabalhos de backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Empregos de Exportação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Criar e gerir Resultados de operações de gestão de backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Criar e gerir políticas de backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser apoiados |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Criar e gerir itens com apoio |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Criar e gerir contentores que contenham itens de backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Retorna resumos para itens protegidos e servidores protegidos para um Serviço de Recuperação . |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/certificados/* | Criar e gerir certificados relacionados com backup no cofre dos Serviços de Recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Criar e gerir informações estendidas relacionadas com o cofre |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A operação Get Vault obtém um objeto que representa o recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/utilizações/* | Criar e gerir o uso do cofre dos Serviços de Recuperação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/ler | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperação/ação | Validar operação em item protegido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Criar operação Vault cria um recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Devolve o estado da operação de backup para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Devolve todos os servidores de gestão de backup registados com cofre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Obtenha todos os recipientes protegidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Verifique o estado de backup para cofres de serviços de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Validar funcionalidades |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Operação devolve a lista de Operações para um Fornecedor de Recursos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Obtém o Estado da Operação para uma determinada Operação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Listar todas as intenções de proteção de reserva |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite-lhe gerir serviços de backup, exceto remoção de backup, criação de cofre e dar acesso a outros [Saiba mais](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Devolução do estado da operação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da operação realizada no Recipiente de Proteção. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executa cópia de segurança para artigo protegido. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém Resultado da operação realizada em itens protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da operação realizada em Itens Protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve detalhes do objeto do Item Protegido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisão de recuperação instantânea de artigos para artigos protegidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenha pontos de recuperação para itens protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação instantânea do item para artigo protegido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de reserva |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Devoluções de todos os contentores registados |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Refresca a lista de contentores |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Criar e gerir trabalhos de backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Empregos de Exportação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Criar e gerir Resultados de operações de gestão de backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Obtenha resultados da operação política. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Devolve todas as Políticas de Proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser apoiados |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Devolve todos os contentores pertencentes à subscrição |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Retorna resumos para itens protegidos e servidores protegidos para um Serviço de Recuperação . |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/certificados/write | A operação 'Certificado de Recurso de actualização' atualiza o certificado de credencial de recurso/cofre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A operação Get Extended Info obtém informações estendidas de um objeto que representa o recurso Azure do tipo ?cofre? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | A operação Get Extended Info obtém informações estendidas de um objeto que representa o recurso Azure do tipo ?cofre? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A operação Get Vault obtém um objeto que representa o recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser usada obter o estado de funcionamento e o resultado para a operação assíncronea submetida |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada obter os contentores registados para um recurso. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | A operação do Contentor do Serviço de Registo pode ser utilizada para registar um contentor com o Serviço de Recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/utilizações/ler | Devolve detalhes de utilização para um Cofre de Serviços de Recuperação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/ler | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperação/ação | Validar operação em item protegido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Devolve o estado da operação de backup para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Obter Estado de Operação Política. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Cria um recipiente registado |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | Faça um inquérito sobre cargas de trabalho dentro de um contentor |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Devolve todos os servidores de gestão de backup registados com cofre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionInt/write | Criar uma intenção de proteção de reserva |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionInt/read | Obtenha uma intenção de proteção de reserva |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Obtenha todos os recipientes protegidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Obtenha todos os itens em um recipiente |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Verifique o estado de backup para cofres de serviços de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Validar funcionalidades |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Operação devolve a lista de Operações para um Fornecedor de Recursos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Obtém o Estado da Operação para uma determinada Operação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Listar todas as intenções de proteção de reserva |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="backup-reader"></a>Leitor de reserva

Pode ver serviços de backup, mas não pode fazer alterações [Saiba mais](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/alocedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Devolução do estado da operação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da operação realizada no Recipiente de Proteção. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém Resultado da operação realizada em itens protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da operação realizada em Itens Protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve detalhes do objeto do Item Protegido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenha pontos de recuperação para itens protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Devoluções de todos os contentores registados |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | Devolve o Resultado da Operação De Trabalho. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Devolve todos os objetos de trabalho |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Empregos de Exportação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Retorna resultado da operação de backup para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Obtenha resultados da operação política. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Devolve todas as Políticas de Proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Devolve todos os contentores pertencentes à subscrição |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Retorna resumos para itens protegidos e servidores protegidos para um Serviço de Recuperação . |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A operação Get Extended Info obtém informações estendidas de um objeto que representa o recurso Azure do tipo ?cofre? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A operação Get Vault obtém um objeto que representa o recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser usada obter o estado de funcionamento e o resultado para a operação assíncronea submetida |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada obter os contentores registados para um recurso. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Retorna a configuração de armazenamento para cofre de serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Configuração de devoluções para cofre de serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Devolve o estado da operação de backup para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Obter Estado de Operação Política. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Devolve todos os servidores de gestão de backup registados com cofre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionInt/read | Obtenha uma intenção de proteção de reserva |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Obtenha todos os itens em um recipiente |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Verifique o estado de backup para cofres de serviços de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Operação devolve a lista de Operações para um Fornecedor de Recursos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Obtém o Estado da Operação para uma determinada Operação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Listar todas as intenções de proteção de reserva |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/utilizações/ler | Devolve detalhes de utilização para um Cofre de Serviços de Recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Validar funcionalidades |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="classic-storage-account-contributor"></a>Colaborador clássico da conta de armazenamento

Permite-lhe gerir contas de armazenamento clássicas, mas não acesso a elas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Criar e gerir contas de armazenamento |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="classic-storage-account-key-operator-service-role"></a>Papel clássico do serviço do operador chave de armazenamento de armazenamento

Os operadores clássicos da conta de armazenamento são autorizados a listar e regenerar chaves em contas de armazenamento clássicas [Saiba mais](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite-lhe gerir tudo no âmbito do Serviço de Caixa de Dados, exceto dar acesso a outros. [Saiba mais](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="data-box-reader"></a>Leitor de caixa de dados

Permite-lhe gerir o Serviço de Caixa de Dados, exceto criar detalhes de encomenda ou edição e dar acesso a outros. [Saiba mais](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Lista as credenciais não encriptadas relacionadas com a encomenda. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Este método devolve a lista de skus disponíveis. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Este método faz todo o tipo de validações. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Este método devolve as configurações para a região. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Valida o endereço de envio e fornece endereços alternativos, caso existam. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="data-lake-analytics-developer"></a>Desenvolvedor de Análise de Lago de Dados

Permite submeter, monitorizar e gerir os seus próprios trabalhos, mas não criar ou apagar contas data lake analytics. [Saiba mais](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/contas/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Elimine uma conta DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Conceder permissões para cancelar postos de trabalho submetidos por outros utilizadores. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore ligada de uma conta DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Deslota uma conta DataLakeStore a partir de uma conta DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Criar ou atualizar uma conta de Armazenamento ligada de uma conta DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Desvincular uma conta de Armazenamento de uma conta DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Apague uma regra de firewall. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Excluir uma política de computação. |
> | **DataActions** |  |
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

### <a name="reader-and-data-access"></a>Leitor e Acesso a Dados

Permite-lhe ver tudo, mas não permitirá que elimine ou crie uma conta de armazenamento ou recurso contido. Também permitirá o acesso de leitura/escrita a todos os dados contidos numa conta de armazenamento através do acesso às chaves da conta de armazenamento.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/listKeys/ação | Devolve as chaves de acesso à conta de armazenamento especificada. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/ListAccountSas/ação | Devolve o token da Conta SAS para a conta de armazenamento especificada. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/ler | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="storage-account-contributor"></a>Contribuidor de Conta de Armazenamento

Permite a gestão das contas de armazenamento. Fornece acesso à chave da conta, que pode ser usada para aceder a dados através da autorização da Chave Partilhada. [Saiba mais](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma sub-rede. Não é alerta. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/* | Criar e gerir contas de armazenamento |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="storage-account-key-operator-service-role"></a>Papel de serviço chave de operador de conta de armazenamento

Permite a listagem e regeneração das chaves de acesso à conta de armazenamento. [Saiba mais](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/listkeys/ação | Devolve as chaves de acesso à conta de armazenamento especificada. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/regeneratekey/ação | Regenera as chaves de acesso para a conta de armazenamento especificada. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="storage-blob-data-contributor"></a>Colaborador de dados blob de armazenamento

Leia, escreva e elimine os recipientes e bolhas de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/delete | Apague um recipiente. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/read | Devolva um recipiente ou uma lista de contentores. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/write | Modifique os metadados ou propriedades de um recipiente. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizador para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/delete | Apague uma bolha. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/read | Devolva uma bolha ou uma lista de bolhas. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/move/action | Move a bolha de um caminho para outro |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/write | Escreva para uma bolha. |
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
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
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

Fornece acesso total aos recipientes e dados blob de armazenamento Azure, incluindo a atribuição do controlo de acesso POSIX. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/* | Permissões completas em contentores. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizador para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/* | Permissões completas em bolhas. |
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

Leia e enuncie os recipientes e bolhas de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/read | Devolva um recipiente ou uma lista de contentores. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizador para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/containers/blobs/read | Devolva uma bolha ou uma lista de bolhas. |
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

### <a name="storage-blob-delegator"></a>Delegado blob de armazenamento

Obtenha uma chave de delegação de utilizador, que pode ser usada para criar uma assinatura de acesso partilhado para um recipiente ou bolha que é assinado com credenciais AZure AD. Para mais informações, consulte [Criar uma delegação de utilizador SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). [Saiba mais](https://docs.microsoft.com/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizador para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="storage-file-data-smb-share-contributor"></a>Contribuinte de Partilhas SMB de Dados de Ficheiros de Armazenamento

Permite ler, escrever e eliminar o acesso em ficheiros/diretórios em ações de ficheiros Azure. Esta função não tem equivalente incorporado nos servidores de ficheiros Windows. [Saiba mais](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/read | Devolve um ficheiro/pasta ou uma lista de ficheiros/pastas. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/write | Devolve o resultado de escrever um ficheiro ou de criar uma pasta. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/delete | Devolve o resultado da eliminação de um ficheiro/pasta. |
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

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Contribuinte Elevado de Partilhas SMB de Dados de Ficheiros de Armazenamento

Permite ler, escrever, excluir e modificar ACLs em ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de alteração nos servidores de ficheiros do Windows. [Saiba mais](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/read | Devolve um ficheiro/pasta ou uma lista de ficheiros/pastas. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/write | Devolve o resultado de escrever um ficheiro ou de criar uma pasta. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/delete | Devolve o resultado da eliminação de um ficheiro/pasta. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/modificar as suas participações/ação | Devolve o resultado da modificação da permissão num ficheiro/pasta. |
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

### <a name="storage-file-data-smb-share-reader"></a>Leitor de Partilhas SMB de Dados de Ficheiros de Armazenamento

Permite o acesso de leitura em ficheiros/diretórios em ações de ficheiros Azure. Esta função equivale a uma partilha de ficheiros ACL de leitura em servidores de ficheiros Windows. [Saiba mais](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/fileServices/fileshares/files/read | Devolve um ficheiro/pasta ou uma lista de ficheiros/pastas. |
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

### <a name="storage-queue-data-contributor"></a>Colaborador de dados de fila de armazenamento

Leia, escreva e elimine as filas de armazenamento do Azure e as mensagens de fila. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/delete | Apague uma fila. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/read | Devolva uma fila ou uma lista de filas. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/write | Modificar metadados ou propriedades de fila. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/messages/delete | Apague uma ou mais mensagens de uma fila. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/messages/read | Espreite ou recupere uma ou mais mensagens de uma fila. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/messages/write | Adicione uma mensagem a uma fila. |
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

### <a name="storage-queue-data-message-processor"></a>Processador de mensagens de dados de fila de armazenamento

Espreite, recupere e elimine uma mensagem de uma fila de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/messages/read | Espreite uma mensagem. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/messages/process/action | Recupere e apague uma mensagem. |
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

Adicione mensagens a uma fila de armazenamento Azure. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/messages/add/action | Adicione uma mensagem a uma fila. |
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

### <a name="storage-queue-data-reader"></a>Leitor de dados de fila de armazenamento

Leia e enuma as filas de armazenamento do Azure e as mensagens de fila. Para saber quais as ações necessárias para uma determinada operação de [dados, consulte permissões para chamadas de operações de dados de bolhas e filas](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcounts/queueServices/queues/messages/read | Espreite ou recupere uma ou mais mensagens de uma fila. |
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


### <a name="azure-maps-data-reader"></a>Leitor de dados Azure Maps

Concede acesso a ler dados relacionados com mapas a partir de uma conta de mapas Azure.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
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
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Colaborador do Serviço de Pesquisa

Permite-lhe gerir os serviços de Pesquisa, mas não o acesso aos mesmos. [Saiba mais](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Criar e gerir serviços de pesquisa |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite-lhe gerir os planos web para sites, mas não acesso aos mesmos.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Criar e gerir quintas de servidores |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Junta-se a um Ambiente de Serviço de Aplicações |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite-lhe gerir websites (não planos web), mas não acesso aos mesmos.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/componentes/* | Criar e gerir componentes insights |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificados/* | Criar e gerir certificados de website |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Obtenha nomes de sites designados para nome de anfitrião. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Junta-se a um Plano de Serviço de Aplicações |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Obtenha as propriedades num Plano de Serviço de Aplicações |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Criar e gerir websites (a criação do site também requer permissões de escrita para o Plano de Serviço de Aplicações associado) |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="containers"></a>Contentores


### <a name="acrdelete"></a>AcrDelete

acr excluir [Saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artefactos/delete | Apague o artefacto num registo de contentores. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

acr sinaleiro de imagem [Saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Empurre/puxe os metadados de confiança do conteúdo para um registo de contentores. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="acrpull"></a>Rio AcrPull

acr puxar [Saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Retire ou obtenha imagens de um registo de contentores. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

acr push [Saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Retire ou obtenha imagens de um registo de contentores. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Empurre ou escreva imagens para um registo de contentores. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

leitor de dados de quarentena acr

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarentena/read | Puxe ou obtenha imagens em quarentena do registo de contentores |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="acrquarantinewriter"></a>Escritor de AcrQuarantineWriter

acr escritor de dados de quarentena

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarentena/read | Puxe ou obtenha imagens em quarentena do registo de contentores |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarentena/write | Escrever/Modificar o estado de quarentena das imagens em quarentena |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service Cluster Função de Administrador

Lista de ação de credencial de administrador de agrupamento. [Saiba mais](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | Listar o clusterAdenão credencial de um cluster gerido |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Obtenha um perfil de acesso ao cluster gerido por nome de função usando credencial de lista |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Papel de utilizador do cluster de serviço Azure Kubernetes

Listar ação credencial do utilizador do cluster. [Saiba mais](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="azure-kubernetes-service-contributor-role"></a>Papel de Colaborador de Serviço Azure Kubernetes

Concede acesso à leitura e escreva clusters de serviçoS Azure Kubernetes

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Obtenha um cluster gerido |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Cria um novo cluster gerido ou atualiza um existente |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Serviço RBAC Administrador

Permite-lhe gerir todos os recursos em cluster/namespace, exceto atualizar ou eliminar quotas de recursos e espaços de nome. [Saiba mais](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Cria ou atualiza uma implementação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Escreve recursos |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Elimina os recursos |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Escreve espaços de nome |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Elimina espaços de nome |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Serviço RBAC Cluster Admin

Permite-lhe gerir todos os recursos do cluster. [Saiba mais](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Cria ou atualiza uma implementação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC Reader

Permite-lhe ver todos os recursos no cluster/namespace, exceto segredos. [Saiba mais](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Cria ou atualiza uma implementação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/read |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/read |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/write |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/secrets/*"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC Writer

Permite-lhe atualizar tudo no cluster/namespace, exceto quotas de recursos, espaços de nome, políticas de segurança de pod, pedidos de assinatura de certificados, (cluster)funções e (cluster)role bindings. [Saiba mais](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Cria ou atualiza uma implementação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/read |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/write |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/read |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/write |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Escreve espaços de nome |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Escreve recursos |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/certificates.k8s.io/certificatesigningrequests/write | Escreve certificados que adignando requerisse |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/podsecuritypolicies/write | Escreve podsecuritypolicies |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except resource quotas, namespaces, pod security policies, certificate signing requests, (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read",
        "Microsoft.ContainerService/managedClusters/*/write"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/write",
        "Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/write"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Bases de Dados


### <a name="cosmos-db-account-reader-role"></a>Papel do leitor de conta de cosm de Cosmos DB

Pode ler os dados da conta DB da Azure Cosmos. Consulte [o Contribuinte de Conta DocumentDB](#documentdb-account-contributor) para gerir as contas DB da Azure Cosmos. [Saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/ler | Leia qualquer coleção |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/base de dadosAcons/readonlykeys/ação | Lê as chaves da conta da base de dados. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Ler definições métricas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Métricas/ler | Ler métricas |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="cosmos-db-operator"></a>Operador de DB cosmos

Permite-lhe gerir as contas DB da Azure Cosmos, mas não aceder aos dados nelas. Impede o acesso às chaves da conta e às cordas de ligação. [Saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/base de dadosAconsolhos/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma sub-rede. Não é alerta. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAcounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/base de dadosAconse/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/base de dadosAcontas/listConnectionStrings/* |  |
> | **DataActions** |  |
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

Pode submeter pedido de restauro para uma base de dados Cosmos DB ou um recipiente para uma conta [Saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/base de dadosAconsu contas/backup/action | Submeter um pedido de configuração de backup |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/base de dadosAconsundo/restauro/ação | Submeter um pedido de restauro |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="documentdb-account-contributor"></a>Colaborador de Conta DocumentDB

Pode gerir as contas de DB da Azure Cosmos. Azure Cosmos DB é anteriormente conhecido como DocumentDB. [Saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/base de dadosAconsolhos/* | Criar e gerir contas DB da Azure Cosmos |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma sub-rede. Não é alerta. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="redis-cache-contributor"></a>Contribuidor redis cache

Permite-te gerir caches Redis, mas não acesso a eles.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Regista o fornecedor de recursos 'Microsoft.Cache' com uma subscrição |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Criar e gerir caches Redis |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.Cache/register/action",
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

### <a name="sql-db-contributor"></a>Colaborador do SQL DB

Permite-lhe gerir bases de dados SQL, mas não acesso a elas. Além disso, não é possível gerir as suas políticas relacionadas com a segurança ou os seus servidores SQL. [Saiba mais](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/localizações/*/ler |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/* | Criar e gerir bases de dados SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/ler | Devolva a lista de servidores ou obtenha as propriedades do servidor especificado. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Ler métricas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Ler definições métricas |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditoriaPolicies/* | Editar políticas de auditoria |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditingSettings/* | Editar definições de auditoria |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditoriaRecords/read | Recuperar os registos de auditoria de blob de base de dados |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/conexãoPolicies/* | Editar políticas de conexão |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/CurrentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/dataMaskingPolicies/* | Editar políticas de mascaramento de dados |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/recomendadoSensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/esquemas/tabelas/colunas/sensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/securityAlertPolicies/* | Editar políticas de alerta de segurança |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/segurançaMetrics/* | Editar métricas de segurança |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/sensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/vulnerabilidadeAssessments/* |  |
> | **DataActions** |  |
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

### <a name="sql-managed-instance-contributor"></a>Colaborador de instância gerida da SQL

Permite-lhe gerir as Ocorrências Geridas SQL e a configuração de rede necessária, mas não pode dar acesso a outros.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/localizações/*/ler |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Ler métricas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Ler definições métricas |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
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

Permite-lhe gerir as políticas relacionadas com a segurança dos servidores e bases de dados SQL, mas não o acesso aos mesmos. [Saiba mais](../sql-database/sql-database-advanced-data-security.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Junta recursos como conta de armazenamento ou base de dados SQL a uma sub-rede. Não é alerta. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/auditoriaPolicies/* | Criar e gerir políticas de auditoria de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/auditoriaS/* | Criar e gerir a definição de auditoria de servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria do blob do servidor alargado configurado num determinado servidor |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditoriaPolicies/* | Criar e gerir políticas de auditoria de bases de dados de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditingSettings/* | Criar e gerir definições de auditoria de bases de dados de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditoriaRecords/read | Recuperar os registos de auditoria de blob de base de dados |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/conexãoPolicies/* | Criar e gerir políticas de ligação à base de dados de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/CurrentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/dataMaskingPolicies/* | Criar e gerir políticas de mascaramento de dados de bases de dados de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob alargada configurada numa determinada base de dados |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/leitura | Devolva a lista de bases de dados ou obtenha as propriedades para a base de dados especificada. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/recomendadoSensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/schemas/read | Arranja um esquema de base de dados. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/esquemas/tabelas/colunas/leitura | Arranja uma coluna de base de dados. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/esquemas/tabelas/colunas/sensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/esquemas/tabelas/leitura | Arranja uma mesa de base de dados. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/securityAlertPolicies/* | Criar e gerir políticas de alerta de segurança da base de dados de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/segurançaMetrics/* | Criar e gerir métricas de segurança da base de dados de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/sensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/DataEncrycryption transparente/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/ler | Devolva a lista de servidores ou obtenha as propriedades do servidor especificado. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/securityAlertPolicies/* | Criar e gerir políticas de alerta de segurança do servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/vulnerabilidadeAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="sql-server-contributor"></a>Colaborador do Servidor SQL

Permite-lhe gerir servidores e bases de dados SQL, mas não acesso aos mesmos, e não às suas políticas relacionadas com a segurança. [Saiba mais](../sql-database/sql-database-aad-authentication-configure.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/localizações/*/ler |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/* | Criar e gerir servidores SQL |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Ler métricas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Ler definições métricas |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/auditoriaPolicies/* | Editar políticas de auditoria de servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/auditoriaS/* | Editar definições de auditoria de servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditoriaPolicies/* | Editar políticas de auditoria de bases de dados de servidores SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditingSettings/* | Editar definições de auditoria de base de dados de servidorES SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/auditoriaRecords/read | Recuperar os registos de auditoria de blob de base de dados |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/conexãoPolicies/* | Editar políticas de ligação à base de dados de servidorES SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/CurrentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/dataMaskingPolicies/* | Editar políticas de mascaramento de dados de bases de dados de servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/recomendadoSensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/esquemas/tabelas/colunas/sensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/securityAlertPolicies/* | Editar políticas de alerta de segurança da base de dados do servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/segurançaMetrics/* | Editar métricas de segurança da base de dados do servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/sensibilidadeLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/bases de dados/vulnerabilidadeAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/securityAlertPolicies/* | Editar políticas de alerta de segurança do servidor SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servidores/vulnerabilidadeAssessments/* |  |
> | **DataActions** |  |
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

## <a name="analytics"></a>Análise


### <a name="azure-event-hubs-data-owner"></a>Proprietário de dados Azure Event Hubs

Permite o acesso total aos recursos do Azure Event Hubs. [Saiba mais](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Recetor de dados Azure Event Hubs

Permite ter acesso aos recursos do Azure Event Hubs. [Saiba mais](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receber/ação |  |
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

### <a name="azure-event-hubs-data-sender"></a>Ender de dados do Azure Event Hubs

Permite enviar acesso aos recursos do Azure Event Hubs. [Saiba mais](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
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

### <a name="data-factory-contributor"></a>Contribuinte da Fábrica de Dados

Criar e gerir fábricas de dados, bem como recursos infantis dentro delas. [Saiba mais](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Criar e gerir fábricas de dados, e recursos infantis dentro delas. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/fábricas/* | Criar e gerir fábricas de dados, e recursos infantis dentro delas. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Criar ou atualizar um eventoSubscrição |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Pode purgar dados de análise [Saiba mais](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/componentes/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/componentes/purga/ação | Purgar dados da Application Insights |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Ver dados de análise de registo |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purga/ação | Eliminar dados especificados do espaço de trabalho |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite-lhe ler e modificar configurações de cluster HDInsight. [Saiba mais](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Obtenha definições de gateway para HDInsight Cluster |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | Atualizar as definições do gateway para o HdInsight Cluster |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurações/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtém ou lista operações de implantação. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="hdinsight-domain-services-contributor"></a>HdInsight Domain Services Colaborador

Pode ler, criar, modificar e eliminar as operações relacionadas com os serviços de domínio necessários para o pacote de segurança da empresa HDInsight [Saiba mais](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Log Analytics Contributor pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VM; leitura das chaves da conta de armazenamento para poder configurar a recolha de registos do Azure Storage; criar e configurar contas de automação; adição de soluções; e configurar diagnósticos Azure em todos os recursos da Azure. [Saiba mais](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensões/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensões/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/máquinas/extensões/write | Instala ou Atualiza uma extensão do Arco Azure |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/grupos de recursos/implementações/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/listKeys/ação | Devolve as chaves de acesso à conta de armazenamento especificada. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.HybridCompute/machines/extensions/write",
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

O Log Analytics Reader pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração dos diagnósticos Azure em todos os recursos do Azure. [Saiba mais](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/consulta/ação | Procure com motor novo. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Executa uma consulta de pesquisa |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Recupera as chaves partilhadas para o espaço de trabalho. Estas teclas são usadas para ligar os agentes da Microsoft Operational Insights ao espaço de trabalho. |
> | **DataActions** |  |
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


### <a name="blockchain-member-node-access-preview"></a>Acesso ao nó do membro blockchain (visualização)

Permite o acesso aos nóns de membro blockchain [Saiba mais](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Obtém ou lista os nodes de transação do membro blockchain existentes. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Conecta-se a um nó de transação do membro blockchain. |
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

## <a name="ai--machine-learning"></a>IA + aprendizagem automática


### <a name="cognitive-services-contributor"></a>Colaborador dos Serviços Cognitivos

Permite criar, ler, atualizar, eliminar e gerir chaves dos Serviços Cognitivos. [Saiba mais](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Obtém as características de uma subscrição. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/fornecedores/features/read | Obtém a funcionalidade de uma subscrição num determinado fornecedor de recursos. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Leia definições de registo |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Ler definições métricas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Ler métricas |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtém ou lista operações de implantação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/grupos de recursos/implementações/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="cognitive-services-data-reader-preview"></a>Leitor de dados de serviços cognitivos (pré-visualização)

Permite-lhe ler os dados dos Serviços Cognitivos.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
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

Permite-lhe ler e listar as chaves dos Serviços Cognitivos. [Saiba mais](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | Chaves da lista |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Leia um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Leia uma definição de diagnóstico de recursos |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Leia definições de registo |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Ler definições métricas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Ler métricas |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtém ou lista operações de implantação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
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

## <a name="mixed-reality"></a>Realidade mista


### <a name="remote-rendering-administrator"></a>Administrador de renderização remota

Fornece ao utilizador capacidades de conversão, gestão de sessão, renderização e diagnóstico para renderização remota Azure [Saiba mais](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Iniciar a conversão de ativos |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Obtenha propriedades de conversão de ativos |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Parar a conversão de ativos |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesess/read | Obtenha propriedades de sessão |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesess/action | Início das sessões |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesess/delete | Parar sessões |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Conecte-se a uma sessão |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Ligue-se ao inspetor de renderização remota |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Cliente de renderização remota

Fornece ao utilizador capacidades de gestão, renderização e diagnóstico para renderização remota Azure. [Saiba mais](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesess/read | Obtenha propriedades de sessão |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesess/action | Início das sessões |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesess/delete | Parar sessões |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Conecte-se a uma sessão |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Ligue-se ao inspetor de renderização remota |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Contribuinte da Conta De Âncoras Espaciais

Permite-lhe gerir âncoras espaciais na sua conta, mas não apagá-las [Saiba mais](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Descubra âncoras espaciais próximas |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Obtenha propriedades de âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/consulta/read | Localizar âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço Azure Spatial Anchors |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
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

### <a name="spatial-anchors-account-owner"></a>Proprietário de conta de âncoras espaciais

Permite-lhe gerir âncoras espaciais na sua conta, incluindo apagá-las [Saiba mais](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Eliminar âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Descubra âncoras espaciais próximas |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Obtenha propriedades de âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/consulta/read | Localizar âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço Azure Spatial Anchors |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |
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

Permite localizar e ler propriedades de âncoras espaciais na sua conta [Saiba mais](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Descubra âncoras espaciais próximas |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Obtenha propriedades de âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/consulta/read | Localizar âncoras espaciais |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço Azure Spatial Anchors |
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

## <a name="integration"></a>Integração


### <a name="api-management-service-contributor"></a>Colaborador do Serviço de Gestão da API

Pode gerir o serviço e as APIs [Saiba mais](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | Criar e gerir o serviço de Gestão de API |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="api-management-service-operator-role"></a>Função do operador de serviço de gestão da API

Pode gerir o serviço, mas não as APIs [Saiba mais](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Ler instâncias do Serviço de Gestão da API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Serviço de Gestão de API de backup para o recipiente especificado numa conta de armazenamento fornecida pelo utilizador |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Eliminar instância do Serviço de Gestão da API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implementações regionais do Serviço de Gestão da API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Leia metadados para uma instância do Serviço de Gestão da API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | Restaurar o Serviço de Gestão da API a partir do contentor especificado numa conta de armazenamento fornecida pelo utilizador |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | Upload TLS/SSL certificado para um Serviço de Gestão de API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizados para um Serviço de Gestão de API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Criar ou atualizar a instância do Serviço de Gestão de API |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Obtenha chaves associadas ao utilizador |
> | **DataActions** |  |
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

### <a name="api-management-service-reader-role"></a>Função de leitor de serviço de gestão da API

Acesso apenas de leitura ao serviço e APIs [Saiba mais](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Ler instâncias do Serviço de Gestão da API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Leia metadados para uma instância do Serviço de Gestão da API |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Obtenha chaves associadas ao utilizador |
> | **DataActions** |  |
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

### <a name="app-configuration-data-owner"></a>Proprietário de dados de configuração de aplicativos

Permite o acesso total aos dados de Configuração de Aplicações. [Saiba mais](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
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

Permite ler o acesso aos dados de Configuração de Aplicações. [Saiba mais](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
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

### <a name="azure-service-bus-data-owner"></a>Proprietário de dados de ônibus de serviço Azure

Permite o acesso total aos recursos do Azure Service Bus. [Saiba mais](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
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

### <a name="azure-service-bus-data-receiver"></a>Recetor de dados de ônibus da Azure Service

Permite ter acesso aos recursos do Azure Service Bus. [Saiba mais](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/filas/ler |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscrições/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receber/ação |  |
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

Permite enviar acesso aos recursos do Azure Service Bus. [Saiba mais](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/filas/ler |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscrições/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
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

### <a name="azure-stack-registration-owner"></a>Proprietário de registo de pilha de Azure

Permite-lhe gerir as inscrições do Azure Stack.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Obtenha as propriedades de uma subscrição de Azure Stack Edge |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Obtém as propriedades de um produto Azure Stack Marketplace |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Obtém as propriedades de um registo Azure Stack |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.AzureStack/edgeSubscriptions/read",
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

### <a name="eventgrid-eventsubscription-contributor"></a>Colaborador de EventosGrid EventSubscription

Permite-lhe gerir as operações de subscrição de eventos EventGrid. [Saiba mais](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Listar subscrições globais de eventos por tipo de tópico |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Listar subscrições de eventos regionais por topictype |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="eventgrid-eventsubscription-reader"></a>Leitor de subscrição de eventos Com EventosGrid

Permite-lhe ler subscrições de eventosGrid. [Saiba mais](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Leia um eventoSSubscrição |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Listar subscrições globais de eventos por tipo de tópico |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Listar subscrições de eventos regionais por topictype |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="fhir-data-contributor"></a>Colaborador de dados da FHIR

Função permite ao utilizador ou principal acesso total aos dados do FHIR [Saiba mais](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>Exportador de dados FHIR

Função permite ao utilizador ou principal ler e exportar dados do FHIR [Saiba mais](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Leia os recursos fHIR (inclui pesquisa e história em versão).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Operação de exportação ($export). |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>Leitor de dados FHIR

Função permite ao utilizador ou principal ler dados do FHIR [Saiba mais](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Leia os recursos fHIR (inclui pesquisa e história em versão).  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>Escritor de dados da FHIR

Função permite ao utilizador ou principal ler e escrever dados FHIR [Saiba mais](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Hard Delete (incluindo o histórico da versão). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Colaborador do Ambiente do Serviço de Integração

Permite-lhe gerir ambientes de serviços de integração, mas não acesso aos mesmos. [Saiba mais](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Desenvolvedor de Ambiente de Serviço de Integração

Permite que os desenvolvedores criem e atualizem fluxos de trabalho, contas de integração e conexões API em ambientes de serviços de integração. [Saiba mais](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Lê o ambiente de serviço de integração. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/join/action | Junta-se ao Ambiente de Serviço de Integração. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Contribuinte de Conta de Sistemas Inteligentes

Permite-lhe gerir contas de Sistemas Inteligentes, mas não acesso a elas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | Microsoft.IntelligentSystems/accounts/* | Criar e gerir contas inteligentes de sistemas |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="logic-app-contributor"></a>Contribuidor de Aplicativos Lógicos

Permite-lhe gerir aplicações lógicas, mas não alterar o acesso às suas. [Saiba mais](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Devolva a conta de armazenamento com a conta dada. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Esta permissão é necessária para os utilizadores que necessitem de acesso aos Registos de Atividade através do portal. Listar categorias de registo em Registo de Atividade. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Leia definições métricas (lista de tipos métricos disponíveis para um recurso). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Gere os recursos de Aplicações Lógicas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/listkeys/ação | Devolve as chaves de acesso à conta de armazenamento especificada. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/ler | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Criar e gerir um Gateway de Ligação. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/conexões/* | Criar e gerir uma Ligação. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Cria e gere uma API personalizada. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Junta-se a um Plano de Serviço de Aplicações |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Obtenha as propriedades num Plano de Serviço de Aplicações |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/funções/listSecrets/action | Listar segredos da função. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="logic-app-operator"></a>Operador de aplicativos lógica

Permite-lhe ler, ativar e desativar aplicações lógicas, mas não editá-las ou atualizá-las. [Saiba mais](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Leia as regras de alerta de Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Obtém definições de diagnóstico para Apps Lógicas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Obtém as métricas disponíveis para Apps Lógicas. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Lê recursos de Aplicações Lógicas. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Desativa o fluxo de trabalho. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Permite o fluxo de trabalho. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/valide/action | Valida o fluxo de trabalho. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtém ou lista operações de implantação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/operaçãoresults/read | Obtenha os resultados da operação de subscrição. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Leia os Gateways de Ligação. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Leia as Ligações. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Leia API personalizada. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Obtenha as propriedades num Plano de Serviço de Aplicações |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="identity"></a>Identidade


### <a name="managed-identity-contributor"></a>Colaborador de Identidade Gerido

Criar, Ler, Atualizar e Eliminar Identidade Atribuída do Utilizador [Saiba mais](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Obtém uma identidade atribuída ao utilizador existente |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Cria uma nova identidade atribuída ao utilizador ou atualiza as tags associadas a uma identidade atribuída ao utilizador existente |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Elimina uma identidade atribuída ao utilizador existente |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="managed-identity-operator"></a>Operador de identidade gerido

Ler e atribuir identidade atribuída ao utilizador [Saiba mais](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="security"></a>Segurança


### <a name="azure-sentinel-contributor"></a>Colaborador Azure Sentinel

Azure Sentinel Colaborador [Saiba mais](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/consulta/ação | Procure com motor novo. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Ver dados de análise de registo |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/saveSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Obter a solução OMS de saída |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/consulta/read | Faça consultas sobre os dados no espaço de trabalho |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/consulta/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenha fontes de dados num espaço de trabalho. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="azure-sentinel-reader"></a>Azure Sentinel Reader

Azure Sentinel Reader [Saiba mais](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/ação | Verifique a autorização e licença do utilizador |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/consulta/ação | Procure com motor novo. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Ver dados de análise de registo |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Obtenha serviços ligados em espaço de trabalho. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/saveSearches/read | Obtém uma consulta de pesquisa guardada |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Obter a solução OMS de saída |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/consulta/read | Faça consultas sobre os dados no espaço de trabalho |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/consulta/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenha fontes de dados num espaço de trabalho. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leia um livro |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
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

### <a name="azure-sentinel-responder"></a>Azure Sentinel Responder

Azure Sentinel Responder [Saiba mais](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/ação | Verifique a autorização e licença do utilizador |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/consulta/ação | Procure com motor novo. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Ver dados de análise de registo |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenha fontes de dados num espaço de trabalho. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/saveSearches/read | Obtém uma consulta de pesquisa guardada |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Obter a solução OMS de saída |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/consulta/read | Faça consultas sobre os dados no espaço de trabalho |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/consulta/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenha fontes de dados num espaço de trabalho. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leia um livro |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
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

### <a name="key-vault-contributor"></a>Contribuidor do Cofre Chave

Permite-lhe gerir cofres chave, mas não acesso a eles. [Saiba mais](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purga/ação | Purgue um cofre de chaves apagado suave |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | **DataActions** |  |
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

Ver e atualizar permissões para o Centro de Segurança. As mesmas permissões que a função de Leitor de Segurança também podem atualizar a política de segurança e rejeitar alertas e recomendações. [Saiba mais](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Criar e gerir atribuições políticas |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Criar e gerir definições políticas |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Criar e gerir conjuntos de políticas |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listar grupos de gestão para o utilizador autenticado. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Ver dados de análise de registo |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Criar e gerir componentes e políticas de segurança |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite-lhe fazer avaliações para o Centro de Segurança

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Criar ou atualizar avaliações de segurança na sua subscrição |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Este é um papel antigo. Por favor, use o Administrador de Segurança.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Leia as máquinas virtuais clássicas de configuração |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Escreva configuração para máquinas virtuais clássicas |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Leia informações de configuração sobre rede clássica |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Criar e gerir componentes e políticas de segurança |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Ver permissões para o Centro de Segurança. Pode ver recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações. [Saiba mais](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Ver dados de análise de registo |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Ler componentes e políticas de segurança |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listar grupos de gestão para o utilizador autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Utilizador de Laboratórios DevTest

Permite ligar, iniciar, reiniciar e desligar as suas máquinas virtuais nos seus Laboratórios Azure DevTest. [Saiba mais](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | Obtenha as propriedades de um conjunto de disponibilidade |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | Leia as propriedades de uma máquina virtual (tamanhos VM, estado de funcionação, extensões VM, etc.) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | Alimenta a máquina virtual e liberta os recursos computativos |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Obtenha as propriedades de uma máquina virtual |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Reinicia a máquina virtual |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Inicia a máquina virtual |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | Leia as propriedades de um laboratório |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Reivindicar uma máquina virtual aleatória e reivindicada no laboratório. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Criar máquinas virtuais num laboratório. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/ação | Certifique-se de que o utilizador atual tem um perfil válido no laboratório. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Apague fórmulas. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Leia fórmulas. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Adicione ou modifique fórmulas. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/assessPolicies/action | Avalia a política do laboratório. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Aproprie-se de uma máquina virtual existente |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Lista os horários de início/paragem aplicáveis, caso existam. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Obtém uma corda que representa o conteúdo do ficheiro RDP para a máquina virtual |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Junta-se a um conjunto de endereços de backend de reequilibrador de carga. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Junta-se a uma regra de entrada de natação de carregamento. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | Leia as propriedades de uma interface de rede (por exemplo, todos os equilibradores de carga dos que a interface de rede faz parte) |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Junta uma Máquina Virtual a uma interface de rede. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | Leia as propriedades de um endereço IP público |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Junta-se a um endereço IP público. Não é alerta. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não é alerta. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtém ou lista operações de implantação. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Recebe ou lista destacamentos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/listKeys/ação | Devolve as chaves de acesso à conta de armazenamento especificada. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Listas de tamanhos disponíveis a máquina virtual pode ser atualizada para |
> | **DataActions** |  |
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

### <a name="lab-creator"></a>Criador de Laboratório

Permite criar novos laboratórios sob as suas contas do Azure Lab. [Saiba mais](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Criar um laboratório numa conta de laboratório. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | Obtenha o preço e disponibilidade de combinações de tamanhos, geografias e sistemas operativos para a conta de laboratório. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestritionsAndUsage/action | Obtenha restrições fundamentais e uso para esta subscrição |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
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

## <a name="monitor"></a>Monitor


### <a name="application-insights-component-contributor"></a>Contribuinte componente de insights de aplicação

Pode gerir componentes de Insights de Aplicação [Saiba mais](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir regras clássicas de alerta |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Criar e gerir novas regras de alerta |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/componentes/* | Criar e gerir componentes insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Criar e gerir testes web insights |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Dá permissão ao utilizador para visualizar e descarregar instantâneos de depurg recolhidos com o Debugger Debugger Do Snapshot da Aplicação. Note que estas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Contribuinte.](#contributor) Ao atribuir aos utilizadores o papel de Debugger Snapshot Debugger do Application Insights, deve conceder a função diretamente ao utilizador. O papel não é reconhecido quando é adicionado a um papel personalizado. [Saiba mais](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/componentes/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="monitoring-contributor"></a>Colaborador de monitorização

Pode ler todos os dados de monitorização e editar as definições de monitorização. Ver também [Começar com papéis, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). [Saiba mais](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/componentes/* | Criar e gerir componentes insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Cria, atualiza ou lê a definição de diagnóstico para o Servidor de Análise |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Liste eventos de Registo de Atividade (eventos de gestão) numa subscrição. Esta permissão aplica-se tanto ao acesso programático como ao portal ao Registo de Atividades. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Esta permissão é necessária para os utilizadores que necessitem de acesso aos Registos de Atividade através do portal. Listar categorias de registo em Registo de Atividade. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Leia definições métricas (lista de tipos métricos disponíveis para um recurso). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Métricas/* | Leia as métricas para um recurso. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registar o fornecedor Microsoft Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Criar e gerir testes web insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Cria um novo espaço de trabalho ou liga-se a um espaço de trabalho existente, fornecendo o id do cliente a partir do espaço de trabalho existente. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Ler/escrever/eliminar pacotes de soluções de análise de registo. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/saveSearches/* | Ler/escrever/apagar analíticas de registos guardou as pesquisas. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Executa uma consulta de pesquisa |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Recupera as chaves partilhadas para o espaço de trabalho. Estas teclas são usadas para ligar os agentes da Microsoft Operational Insights ao espaço de trabalho. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Leia/escreva/elimine as configurações de armazenamento de análise de registo. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitores/* |  |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/notificationSettings/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
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

Permite publicar métricas contra recursos Azure [Saiba mais](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registar o fornecedor Microsoft Insights |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Escrever métricas |
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

Pode ler todos os dados de monitorização (métricas, registos, etc.). Ver também [Começar com papéis, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). [Saiba mais](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Executa uma consulta de pesquisa |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="workbook-contributor"></a>Contribuinte do livro

Pode salvar livros partilhados. [Saiba mais](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Criar ou atualizar um livro |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Eliminar um livro |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leia um livro |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="workbook-reader"></a>Leitor de livros de trabalho

Pode ler livros. [Saiba mais](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leia um livro |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="management--governance"></a>Gestão + governação


### <a name="automation-job-operator"></a>Operador de emprego de automação

Criar e Gerir Empregos utilizando livros de automação. [Saiba mais](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAs contas/hybridRunbookWorkerGroups/read | Lê recursos operários híbridos runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém um emprego de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Retoma um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Para um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém um fluxo de trabalho da Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Suspende um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Cria um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém a saída de um trabalho |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Operadores de automação são capazes de iniciar, parar, suspender e retomar [empregos Saiba mais](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAs contas/hybridRunbookWorkerGroups/read | Lê recursos operários híbridos runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém um emprego de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Retoma um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Para um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém um fluxo de trabalho da Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Suspende um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Cria um trabalho de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAs contas/trabalhoSchedules/read | Obtém um horário de trabalho da Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Cria um horário de trabalho da Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAs contas/linkedWorkspace/read | Obtém o espaço de trabalho ligado à conta de automação |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém uma conta de Automação Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationS/runbooks/read | Obtém um runbook da Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém um ativo de agendação Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Cria ou atualiza um ativo de agendação Azure Automation |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automation | Obtém a saída de um trabalho |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="automation-runbook-operator"></a>Operador de runbook de automação

Leia as propriedades do Runbook - para ser capaz de criar Jobs of the runbook. [Saiba mais](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationS/runbooks/read | Obtém um runbook da Azure Automation |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="azure-connected-machine-onboarding"></a>Máquina conectada Azure a bordo

Pode embarcar máquinas ligadas a Azure. [Saiba mais](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Leia todas as máquinas do Arco Azul |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Escreve uma máquina de Arco Azul |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Obtenha a atribuição de configuração de hóspedes. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Pode ler, escrever, apagar e voltar a bordo máquinas ligadas a azure.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Leia todas as máquinas do Arco Azul |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Escreve uma máquina de Arco Azul |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Elimina máquinas Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/religação/ação | Reconecta uma máquina Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/máquinas/extensões/write | Instala ou Atualiza uma extensão do Arco Azure |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.HybridCompute/machines/extensions/write",
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

### <a name="billing-reader"></a>Leitor de Faturação

Permite ler acesso a dados de faturação [Saiba mais](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Ler informações de Faturação |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listar grupos de gestão para o utilizador autenticado. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="blueprint-contributor"></a>Contribuidor de Projeto

Pode gerir definições de planta, mas não atribuí-las. [Saiba mais](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Criar e gerir definições de plantas ou artefactos de planta. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="blueprint-operator"></a>Operador de blueprint

Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. Note que isto só funciona se a atribuição for feita com uma identidade gerida atribuída pelo utilizador. [Saiba mais](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Criar e gerir tarefas de planta. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="cost-management-contributor"></a>Contribuidor para a Gestão de Custos

Pode ver custos e gerir a configuração de custos (por exemplo, orçamentos, exportações) [Saiba mais](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurações/ler | Obter configurações |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Lê recomendações |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listar grupos de gestão para o utilizador autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Pode ver dados de custos e configuração (por exemplo, orçamentos, exportações) [Saiba mais](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/ler | Obtém a lista de assinaturas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurações/ler | Obter configurações |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Lê recomendações |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listar grupos de gestão para o utilizador autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="hierarchy-settings-administrator"></a>Administrador de Definições de Hierarquia

Permite aos utilizadores editar e eliminar Definições de Hierarquia

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Cria ou atualiza as definições da hierarquia do grupo de gestão. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Elimina as definições da hierarquia do grupo de gestão. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Papel de Contribuinte de Aplicação Gerido

Permite criar recursos de aplicação geridos.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/aplicações/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Registe-se em Soluções. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/grupos de recursos/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="managed-application-operator-role"></a>Papel do operador de aplicação gerido

Permite-lhe ler e executar ações sobre recursos de aplicação gerida

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/aplicações/ler | Recupera uma lista de aplicações. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/ação |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Atribuição de registo de serviços geridos Eliminar Função

Atribuição de Registo de Serviços Geridos Eliminar Função permite que os utilizadores inquilinos gestores apaguem a atribuição de inscrição atribuída ao seu inquilino. [Saiba mais](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Recupera uma lista de atribuições de registo de Serviços Geridos. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Remove a atribuição de registo de Serviços Geridos. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Lê o estado de funcionamento do recurso. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Função contributiva do Grupo de Gestão [Saiba mais](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Excluir grupo de gestão. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listar grupos de gestão para o utilizador autenticado. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscrições/delete | Assinatura de desa associados do grupo de gestão. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscrições/write | Associados subscrição existente com o grupo de gestão. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Criar ou atualizar um grupo de gestão. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscrições/read | Lista a subscrição no âmbito do grupo de gestão. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
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

Função de leitor de grupo de gestão

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listar grupos de gestão para o utilizador autenticado. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscrições/read | Lista a subscrição no âmbito do grupo de gestão. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
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

### <a name="new-relic-apm-account-contributor"></a>Novo contribuinte da conta APM da relíquia

Permite-lhe gerir as contas e aplicações de Gestão de Desempenho de Aplicações Novas Relíquias, mas não o acesso às contas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="policy-insights-data-writer-preview"></a>Escritor de dados de insights de política (pré-visualização)

Permite ler o acesso às políticas de recursos e escrever acesso a eventos de política de componentes de recursos. [Saiba mais](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Obtenha informações sobre uma atribuição de política. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Obtenha informações sobre uma definição de política. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Obtenha informações sobre uma definição de definição de definição de política. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Verifique o estado de conformidade de um determinado componente contra as políticas de dados. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Registar os eventos de política de componente de recursos. |
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

Utilizadores com direitos de criar/modificar a política de recursos, criar bilhete de apoio e ler recursos/hierarquia. [Saiba mais](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */ler | Leia recursos de todos os tipos, exceto segredos. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Criar e gerir atribuições políticas |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Criar e gerir definições políticas |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Criar e gerir conjuntos de políticas |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="site-recovery-contributor"></a>Colaborador de Recuperação de Sítios

Permite-lhe gerir o serviço de Recuperação de Sítios exceto a criação de abóbada e atribuição de funções [Saiba mais](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/alocedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | Alocarmps é uma operação interna utilizada pelo serviço |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/certificados/write | A operação 'Certificado de Recurso de actualização' atualiza o certificado de credencial de recurso/cofre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Criar e gerir informações estendidas relacionadas com o cofre |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A operação Get Vault obtém um objeto que representa o recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Criar ou atualizar definições de alerta de replicação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationSvents/read | Ler quaisquer Eventos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Criar e gerir tecidos de replicação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Criar e gerir trabalhos de replicação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Criar e gerir políticas de replicação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Criar e gerir planos de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Criar e gerir a configuração de armazenamento do cofre dos Serviços de Recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/utilizações/ler | Devolve detalhes de utilização para um Cofre de Serviços de Recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | A operação Vault Token pode ser usada para obter o Vault Token para operações de backend de nível de cofre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Leia alertas para o cofre dos serviços de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/ler | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/cofres/replicationOperationStatus/read | Leia qualquer estado de operação de replicação de abóbada |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="site-recovery-operator"></a>Operador de Recuperação de Sítio

Permite-lhe falhar e falhar mas não realizar outras operações de gestão de Recuperação de [Sítios Saiba mais](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenha a definição de rede virtual |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/alocedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | Alocarmps é uma operação interna utilizada pelo serviço |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A operação Get Extended Info obtém informações estendidas de um objeto que representa o recurso Azure do tipo ?cofre? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A operação Get Vault obtém um objeto que representa o recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser usada obter o estado de funcionamento e o resultado para a operação assíncronea submetida |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada obter os contentores registados para um recurso. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Ler quaisquer definições de alertas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationSvents/read | Ler quaisquer Eventos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Verifica a consistência do tecido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Leia quaisquer Tecidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Reassociate Gateway |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Renovar certificado de tecido |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer Redes |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Leia quaisquer mapeamentos de rede |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer recipientes de proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Leia quaisquer itens protegidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Compromisso de Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Ativação Pós-falha Planeada |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replicação de reparação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Artigo protegido reProtectet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Recipiente de proteção do interruptor |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Ativação Pós-falha de Teste |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza de Failover test |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Ativação pós-falha |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Serviço de Mobilidade atualizada |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Leia quaisquer mapeamentos de contentores de proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler quaisquer Prestadores de Serviços de Recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Provedor de Atualização |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Leia quaisquer classificações de armazenamento |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassifications/read | Leia quaisquer mapeamentos de classificação de armazenamento |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Leia quaisquer vCenters |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Criar e gerir trabalhos de replicação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Ler quaisquer Políticas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de Recuperação de Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de Recuperação de Failover Planeado |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Plano de Recuperação ReProtect |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Plano de Recuperação de Failover test |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plano de recuperação de limpeza de falha de teste |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/UnplannedFailover/action | Plano de Recuperação de Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Leia alertas para o cofre dos serviços de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/utilizações/ler | Devolve detalhes de utilização para um Cofre de Serviços de Recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | A operação Vault Token pode ser usada para obter o Vault Token para operações de backend de nível de cofre. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAcontas/ler | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="site-recovery-reader"></a>Leitor de Recuperação de Site

Permite-lhe ver o estado de Recuperação do Site mas não realizar outras operações de gestão [Saiba mais](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/alocedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A operação Get Extended Info obtém informações estendidas de um objeto que representa o recurso Azure do tipo ?cofre? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recebe os alertas para o cofre dos serviços de recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A operação Get Vault obtém um objeto que representa o recurso Azure do tipo 'cofre' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A operação Get Operation Results pode ser usada obter o estado de funcionamento e o resultado para a operação assíncronea submetida |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A operação Get Containers pode ser utilizada obter os contentores registados para um recurso. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Ler quaisquer definições de alertas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationSvents/read | Ler quaisquer Eventos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Leia quaisquer Tecidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer Redes |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Leia quaisquer mapeamentos de rede |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer recipientes de proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Leia quaisquer itens protegidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Leia quaisquer mapeamentos de contentores de proteção |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler quaisquer Prestadores de Serviços de Recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Leia quaisquer classificações de armazenamento |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassifications/read | Leia quaisquer mapeamentos de classificação de armazenamento |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Leia quaisquer vCenters |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Leia quaisquer Empregos |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Ler quaisquer Políticas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Cofres/utilizações/ler | Devolve detalhes de utilização para um Cofre de Serviços de Recuperação. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | A operação Vault Token pode ser usada para obter o Vault Token para operações de backend de nível de cofre. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

Permite criar e gerir pedidos de suporte [Saiba mais](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="tag-contributor"></a>Colaborador da Tag

Permite-lhe gerir etiquetas em entidades, sem fornecer acesso às próprias entidades. [Saiba mais](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/recursosGroups/recursos/read | Obtém os recursos para o grupo de recursos. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/recursos/read | Obtém recursos de uma assinatura. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Outros


### <a name="biztalk-contributor"></a>Colaborador bizTalk

Permite-lhe gerir os serviços BizTalk, mas não ter acesso aos mesmos.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerir serviços BizTalk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

### <a name="desktop-virtualization-user"></a>Utilizador de Virtualização de Desktop

Permite ao utilizador utilizar as aplicações num grupo de aplicações. [Saiba mais](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.DesktopVirtualization/applicationGroups/useApplications/action | Utilizar o Grupo de Aplicações |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Colaborador de Coleções de Trabalho do Programador

Permite-lhe gerir as coleções de emprego do Scheduler, mas não ter acesso a elas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.Autorização](resource-provider-operations.md#microsoftauthorization)/*/ler | Ler papéis e atribuições de funções |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerir um alerta métrico clássico |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtém os estatutos de disponibilidade para todos os recursos no âmbito especificado |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Criar e gerir uma implantação |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscrições/resourceGroups/read | Obtém ou lista grupos de recursos. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Criar e gerir coleções de emprego |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um bilhete de apoio |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
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

## <a name="next-steps"></a>Próximos passos

- [Combine o fornecedor de recursos ao serviço](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Funções personalizadas do Azure](custom-roles.md)
- [Permissions in Azure Security Center](../security-center/security-center-permissions.md) (Permissões no Centro de Segurança do Azure)
