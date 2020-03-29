---
title: Acesso granular baseado em funções Azure HDInsight configurações de cluster
description: Conheça as alterações necessárias como parte da migração para o acesso granular baseado em papéis para configurações de cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: f1fdb9dffbe06430ea7e3eb9339e23f5239e4e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310837"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para o acesso granular baseado em funções para as configurações de cluster

Estamos a introduzir algumas alterações importantes para apoiar um acesso mais fino baseado em papéis para obter informações sensíveis. Como parte destas alterações, poderá ser necessária alguma ação até 3 de setembro de **2019** se estiver a utilizar uma das [entidades/cenários afetados.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>O que está a mudar?

Anteriormente, os segredos poderiam ser obtidos através da API HDInsight por utilizadores de cluster que possuem as funções Owner, Contributor ou Reader [RBAC,](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)uma vez que estavam disponíveis para qualquer pessoa com a `*/read` permissão. Os segredos são definidos como valores que poderiam ser usados para obter um acesso mais elevado do que o papel de um utilizador deve permitir. Estes incluem valores como credenciais http de gateway cluster, chaves de conta de armazenamento e credenciais de base de dados.

A partir de 3 de setembro de 2019, o acesso a estes segredos exigirá a permissão, o `Microsoft.HDInsight/clusters/configurations/action` que significa que já não podem ser acedidos pelos utilizadores com a função De Leitor. As funções que têm esta permissão são Contributor, Owner, e o novo papel do Operador de Cluster HDInsight (mais abaixo).

Estamos também a introduzir uma nova função de Operador de [Cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) que será capaz de recuperar segredos sem que lhe sejam concedidas as permissões administrativas do Colaborador ou Proprietário. Em resumo:

| Função                                  | Anteriormente                                                                                       | Indo para a frente       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | - Ler acesso, incluindo segredos                                                                   | - Ler acesso, **excluindo** segredos |           |   |   |
| Operador de cluster HDInsight<br>(Novo Papel) | N/D                                                                                              | - Ler/escrever acesso, incluindo segredos         |   |   |
| Contribuinte                           | - Ler/escrever acesso, incluindo segredos<br>- Criar e gerir todos os tipos de recursos Azure.     | Sem alterações |
| Proprietário                                 | - Ler/escrever acesso incluindo segredos<br>- Acesso total a todos os recursos<br>- Delegar acesso a outros | Sem alterações |

Para obter informações sobre como adicionar a atribuição de função de Operador de Cluster HDInsight a um utilizador para conceder-lhes acesso de leitura/escrita a segredos de cluster, consulte a secção abaixo, Adicione a atribuição de função de Operador de [Cluster HDInsight a um utilizador](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Estou afetado por estas mudanças?

São afetadas as seguintes entidades e cenários:

- [API](#api): Utilizadores `/configurations` `/configurations/{configurationName}` que utilizem os pontos finais ou finais.
- [Ferramentas Azure HDInsight para](#azure-hdinsight-tools-for-visual-studio-code) visual studio code versão 1.1.1 ou abaixo.
- [Kit de ferramentas Azure para intelliJ](#azure-toolkit-for-intellij) versão 3.20.0 ou abaixo.
- [Ferramentas Azure Data Lake e Stream Analytics para Estúdio Visual](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) abaixo da versão 2.3.9000.1.
- [Kit de ferramentas Azure para](#azure-toolkit-for-eclipse) eclipse versão 3.15.0 ou abaixo.
- [SDK para .NET](#sdk-for-net)
    - [versões 1.x ou 2.x](#versions-1x-and-2x): `ConfigureHttpSettings` `EnableHttp` Utilizadores `DisableHttp` que utilizem a `GetClusterConfigurations`classe `GetConnectivitySettings`ConfiguraçõesOperationsExtensions.
    - [versões 3.x e](#versions-3x-and-up)up `Get` `Update`: `EnableHttp`Utilizadores `DisableHttp` que `ConfigurationsOperationsExtensions` usam os métodos da classe.
- [SDK para Python](#sdk-for-python): `get` `update` Utilizadores que `ConfigurationsOperations` utilizem os ou métodos da classe.
- [SDK para Java](#sdk-for-java): `update` `get` Utilizadores que `ConfigurationsInner` utilizem os ou métodos da classe.
- [SDK para Go](#sdk-for-go): `Get` `Update` Utilizadores que `ConfigurationsClient` utilizem os ou métodos da estrutura.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) abaixo da versão 2.0.0.
Consulte as secções abaixo (ou use as ligações acima) para ver os passos de migração para o seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou depreciadas:

- [**GET /configurações/{configuraçãoName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informação sensível removida)
    - Anteriormente utilizado para obter tipos de configuração individuais (incluindo segredos).
    - A partir de 3 de setembro de 2019, esta chamada da API irá agora devolver tipos individuais de configuração com segredos omitidos. Para obter todas as configurações, incluindo segredos, utilize a nova chamada POST/configurações. Para obter apenas definições de gateway, utilize a nova chamada POST /getGatewaySettings.
- [**GET /configurações**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (depreciadas)
    - Anteriormente usado para obter todas as configurações (incluindo segredos)
    - A partir de 3 de setembro de 2019, esta chamada da API será depreciada e já não será apoiada. Para obter todas as configurações em curso, utilize a nova chamada POST/configurações. Para obter configurações com parâmetros sensíveis omitidos, utilize a chamada GET /configurações/{configuraçãoName} .
- [**POST /configurações/{configuraçãoNome}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (depreciado)
    - Anteriormente usado para atualizar credenciais de gateway.
    - A partir de 3 de setembro de 2019, esta chamada da API será depreciada e já não será apoiada. Utilize as novas Definições POST /updateGatewaySettings.

Foram adicionadas as seguintes APIs de substituição:</span>

- [**POST /Configurações**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Utilize esta API para obter todas as configurações, incluindo segredos.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Utilize esta API para obter definições de gateway.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilize este API para atualizar as definições de gateway (nome de utilizador e/ou palavra-passe).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Ferramentas Azure HDInsight para código de estúdio visual

Se estiver a utilizar a versão 1.1.1 ou abaixo, atualize a [versão mais recente do Azure HDInsight Tools for Visual Studio Code para](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Se estiver a utilizar a versão 3.20.0 ou abaixo, atualize a [versão mais recente do Plugin Azure Toolkit para o Plugin IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Ferramentas de Análise de Lago de Dados Azure e Stream para Estúdio Visual

Atualização para a versão 2.3.9000.1 ou posterior de [Azure Data Lake e Stream Analytics Tools para Estúdio Visual para](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) evitar interrupções.  Para obter ajuda na atualização, consulte a nossa documentação, [Atualizar Ferramentas do Lago](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)de Dados para Estúdio Visual .

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Se estiver a utilizar a versão 3.15.0 ou abaixo, atualize a [versão mais recente do Kit de Ferramentas Azure para eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) para evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>Versões 1.x e 2.x

Atualize para a [versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do HDInsight SDK para .NET. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations`**deixará de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, `ClusterOperationsExtensions.ListConfigurations` incluindo parâmetros sensíveis, use o futuro.  Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster.
    - Para recuperar apenas as `ClusterOperationsExtensions.GetGatewaySettings`credenciais de gateway HTTP, use .

- `ClusterOperationsExtensions.GetConnectivitySettings`está agora depreciado e foi `ClusterOperationsExtensions.GetGatewaySettings`substituído por .

- `ClusterOperationsExtensions.ConfigureHttpSettings`está agora depreciado e foi `ClusterOperationsExtensions.UpdateGatewaySettings`substituído por .

- `ConfigurationsOperationsExtensions.EnableHttp`e `DisableHttp` agora são depreciados. Http está agora sempre ativado, pelo que estes métodos já não são necessários.

#### <a name="versions-3x-and-up"></a>Versões 3.x e acima

Atualize para a [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou posterior do HDInsight SDK para .NET. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**deixará de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) incluindo parâmetros sensíveis, use o futuro.Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster. 
    - Para recuperar apenas as [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)credenciais de gateway HTTP, use . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)está agora depreciado e foi [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)substituído por . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)e [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) agora são depreciados. Http está agora sempre ativado, pelo que estes métodos já não são necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualize para a [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou posterior do HDInsight SDK para Python. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**deixará de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) incluindo parâmetros sensíveis, use o futuro.Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster. 
    - Para recuperar apenas as [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)credenciais de gateway HTTP, use .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)está agora depreciado e foi [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)substituído por .

### <a name="sdk-for-java"></a>SDK para Java

Atualize para a [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) ou posterior do HDInsight SDK para Java. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)**deixará de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)está agora depreciado.

### <a name="sdk-for-go"></a>SDK Para Ir

Atualize para a [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) ou mais tarde do HDInsight SDK para Go. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**deixará de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) incluindo parâmetros sensíveis, use o futuro.Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster. 
    - Para recuperar apenas as [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)credenciais de gateway HTTP, use .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)está agora depreciado e foi [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)substituído por .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Atualize a [versão 2.0.0 da Az PowerShell](https://www.powershellgallery.com/packages/Az) ou posteriormente para evitar interrupções.  Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações.
- `Grant-AzHDInsightHttpServicesAccess`é agora depreciado e foi substituído `Set-AzHDInsightGatewayCredential` pelo novo cmdlet.
- `Get-AzHDInsightJobOutput`foi atualizado para apoiar o acesso granular baseado na função da chave de armazenamento.
    - Os utilizadores com as funções Operador de Clusters, Contribuidor ou Proprietário do HDInsight não serão afetados.
    - Os utilizadores com apenas a `DefaultStorageAccountKey` função Reader terão de especificar explicitamente o parâmetro.
- `Revoke-AzHDInsightHttpServicesAccess`está agora depreciado. Http está agora sempre ativado, pelo que este cmdlet já não é necessário.
 Veja o [az. Guia de migração HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) para mais detalhes.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicione a atribuição de função de operador de cluster HDInsight a um utilizador

Um utilizador com a função [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pode atribuir a função de Operador de [Cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) aos utilizadores que desejaria ter acesso a valores sensíveis de configuração do cluster HDInsight (tais como credenciais de gateway de cluster e chaves de conta de armazenamento).

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

A forma mais simples de adicionar esta `az role assignment create` atribuição de funções é usando o comando em Azure CLI.

> [!NOTE]
> Este comando deve ser dirigido por um utilizador com a função Proprietário, pois só eles podem conceder estas permissões. O `--assignee` nome é o principal do serviço ou endereço de e-mail do utilizador a quem pretende atribuir a função de Operador de Cluster HDInsight. Se receber um erro de permissões insuficiente, consulte as FAQ abaixo.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Papel de concessão ao nível do recurso (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Papel de concessão ao nível do grupo de recursos

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Papel de concessão ao nível da subscrição

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Pode utilizar o portal Azure em alternativa para adicionar a atribuição de função de Operador de Cluster HDInsight a um utilizador. Consulte a documentação, [Gerir o acesso aos recursos do Azure utilizando o RBAC e o portal Azure - Adicione uma atribuição](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)de funções .

## <a name="faq"></a>FAQ

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Porque estou a ver uma resposta 403 (Proibido) após atualizar os meus pedidos e/ou ferramenta da API?

As configurações do cluster estão agora por `Microsoft.HDInsight/clusters/configurations/*` trás do controlo de acesso baseado em funções granular e exigem a permissão para aceder às mesmas. Para obter esta permissão, atribuir a função de Operador de Cluster HDInsight, Colaborador ou Proprietário ao utilizador ou principal de serviço que tente aceder às configurações.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Por que vejo "privilégios insuficientes para completar a operação" ao executar o comando Azure CLI para atribuir a função de Operador de Cluster HDInsight a outro utilizador ou diretor de serviço?

Além de ter a função Proprietário, o utilizador ou diretor de serviço que executa o comando precisa de ter permissões AAD suficientes para procurar as IDs do objeto do designado. Esta mensagem indica permissões AAD insuficientes. Tente substituir `-–assignee` o `–assignee-object-id` argumento e forneça o id do objeto do designado como parâmetro em vez do nome (ou o ID principal no caso de uma identidade gerida). Consulte a secção de parâmetros opcionais da atribuição de [funções az criar documentação](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) para mais informações.

Se isto ainda não funcionar, contacte o seu administrador da AAD para obter as permissões corretas.

### <a name="what-will-happen-if-i-take-no-action"></a>O que vai acontecer se eu não tomar medidas?

A partir de 3 de `GET /configurations` setembro `POST /configurations/gateway` de 2019, `GET /configurations/{configurationName}` as chamadas deixarão de devolver qualquer informação e a chamada deixará de devolver parâmetros sensíveis, como as chaves da conta de armazenamento ou a palavra-passe do cluster. O mesmo acontece com os métodos SDK correspondentes e os cmdlets PowerShell.

Se estiver a utilizar uma versão mais antiga de uma das ferramentas para Visual Studio, VSCode, IntelliJ ou Eclipse acima mencionadas, elas deixarão de funcionar até atualizarem.

Para obter informações mais detalhadas, consulte a secção correspondente deste documento para o seu cenário.
