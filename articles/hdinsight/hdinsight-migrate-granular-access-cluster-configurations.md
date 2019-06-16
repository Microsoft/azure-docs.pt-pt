---
title: Migrar para o acesso granular baseada em funções para configurações de cluster - Azure HDInsight
description: Saiba mais sobre as alterações necessárias como parte da migração para o acesso baseado em função granular para configurações de cluster do HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1ec4786291d6e2e5be6785e52cf3ab5bb5bbc690
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754530"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para o acesso granular baseado em funções para as configurações de cluster

Introduzimos algumas alterações importantes para dar suporte a mais acesso baseado em funções para obter informações confidenciais detalhado. Como parte de um destes for alterado, alguns **ação poderá ser necessária** se estiver a utilizar um da [afetados entidades/cenários](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>O que está a mudar?

Anteriormente, segredos podem ser obtidos através da API do HDInsight por possessing o proprietário, contribuinte ou leitor de utilizadores de cluster [funções RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), conforme eles estavam disponíveis para qualquer pessoa com o `*/read` permissão era necessária.
Daqui em diante, aceder a estes segredos exigirá o `Microsoft.HDInsight/clusters/configurations/*` permissão, o que significa que já não podem ser acedidos por utilizadores com a função de leitor. Segredos são definidos conforme devem permitir valores que podem ser utilizados para obter um acesso mais elevado de função de um utilizador. Estes incluem valores como credenciais HTTP do gateway de cluster, as chaves de conta de armazenamento e as credenciais da base de dados.

Estamos a introduzir também um novo [operador de Cluster do Hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) função que será capaz de obter segredos sem a ser concedidas as permissões administrativas do Contribuidor ou proprietário. Para resumir:

| Função                                  | Anteriormente                                                                                       | No futuro       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | -Acesso de leitura, incluindo segredos                                                                   | -Acesso de leitura, **excluindo** segredos |           |   |   |
| Operador de Cluster do HDInsight<br>(Nova função) | N/A                                                                                              | -Acesso de leitura/gravação, incluindo segredos         |   |   |
| Contribuinte                           | -Acesso de leitura/gravação, incluindo segredos<br>-Criar e gerir todos os tipos de recursos do Azure.     | Sem alteração |
| Owner                                 | -Acesso de leitura/gravação, incluindo segredos<br>-Total acesso a todos os recursos<br>-Delegar o acesso a outras pessoas | Sem alteração |

Para obter informações sobre como adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador para conceder-lhes acesso de leitura/escrita a segredos de cluster, consulte a abaixo da secção [adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Estou afetado por estas alterações?

As entidades e os cenários seguintes são afetados:

- [API](#api): Os utilizadores que utilizam o `/configurations` ou `/configurations/{configurationName}` pontos de extremidade.
- [Ferramentas do HDInsight do Azure para Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versão 1.1.1 ou abaixo.
- [Azure Toolkit para IntelliJ](#azure-toolkit-for-intellij) versão 3.20.0 ou abaixo.
- [O Azure Data Lake e do Stream Analytics Tools para Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) inferior à versão 2.3.9000.1.
- [Azure Toolkit para Eclipse](#azure-toolkit-for-eclipse) versão 3.15.0 ou abaixo.
- [SDK para .NET](#sdk-for-net)
    - [as versões 1.x ou 2.x](#versions-1x-and-2x): Os utilizadores que utilizam o `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` ou `DisableHttp` métodos da classe ConfigurationsOperationsExtensions.
    - [versões 3.x e até](#versions-3x-and-up): Os utilizadores que utilizam o `Get`, `Update`, `EnableHttp`, ou `DisableHttp` métodos a partir do `ConfigurationsOperationsExtensions` classe.
- [SDK para Python](#sdk-for-python): Os utilizadores que utilizam o `get` ou `update` métodos a partir do `ConfigurationsOperations` classe.
- [SDK para Java](#sdk-for-java): Os utilizadores que utilizam o `update` ou `get` métodos a partir do `ConfigurationsInner` classe.
- [SDK para Go](#sdk-for-go): Os utilizadores que utilizam o `Get` ou `Update` métodos a partir do `ConfigurationsClient` struct.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) inferior à versão 2.0.0.
Veja as secções abaixo (ou utilize as ligações acima) para ver os passos de migração para o seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou preteridas:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informações confidenciais removidas)
    - Anteriormente, é usado para obter os tipos de configuração individuais (incluindo segredos).
    - Esta chamada de API agora irá devolver os tipos de configuração individuais com segredos omitidos. Para obter todas as configurações, incluindo segredos, utilize a nova chamada de /configurations de publicar. Para obter apenas as definições do gateway, utilize a nova /getGatewaySettings chamada de POST.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (preterido)
    - Utilizado anteriormente para obter todas as configurações de (incluindo segredos)
    - Esta chamada à API já não ser suportada. Para obter todas as configurações no futuro, utilize a nova chamada de /configurations de publicar. Para obter as configurações com os parâmetros com diferenciação for omitidos, utilize a chamada de /configurations/ {configurationName} GET.
- [**POST /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (deprecated)
    - Anteriormente, é utilizado para atualizar as credenciais de gateway.
    - Esta chamada à API será preterida e já não é suportada. Em alternativa, utilize o novo /updateGatewaySettings POST.

A substituição seguinte que APIs foram adicionadas:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Utilize esta API para obter todas as configurações, incluindo segredos.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Utilize esta API para obter as definições do gateway.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilize esta API para atualizar as definições do gateway (nome de utilizador e/ou palavra-passe).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Ferramentas do Azure HDInsight para Visual Studio Code

Se estiver a utilizar a versão 1.1.1 ou abaixo, atualizar para o [versão mais recente do Azure HDInsight Tools para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Se estiver a utilizar a versão 3.20.0 ou abaixo, atualizar para o [a versão mais recente do Azure Toolkit para IntelliJ Plug-in do](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>O Azure Data Lake e do Stream Analytics Tools para Visual Studio

Atualização para a versão 2.3.9000.1 ou versão posterior da [do Azure Data Lake e do Stream Analytics Tools para Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) para evitar interrupções.  Para obter ajuda com a atualização, consulte a nossa documentação, [atualização Data Lake Tools para Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit para Eclipse

Se estiver a utilizar a versão 3.15.0 ou abaixo, atualizar para o [a versão mais recente do Azure Toolkit para Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) para evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>As versões 1.x e 2.x

Atualizar para o [a versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do SDK do HDInsight para .NET. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations` serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize `ClusterOperationsExtensions.ListConfigurations` daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster.
    - Para obter credenciais de gateway HTTP apenas, utilize `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` foi agora preterida e foi substituído por `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` foi agora preterida e foi substituído por `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` foram preteridas. Está agora sempre ativado HTTP, para que esses métodos já não são necessários.

#### <a name="versions-3x-and-up"></a>Versões 3.x e até

Atualizar para o [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou posterior do SDK do HDInsight para .NET. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) foi agora preterida e foi substituído por [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) foram preteridas. Está agora sempre ativado HTTP, para que esses métodos já não são necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualizar para o [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou posterior do SDK do HDInsight para Python. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) foi agora preterida e foi substituído por [ `ClusterOperations.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK para Java

Atualizar para o [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) ou posterior do SDK do HDInsight para Java. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize [ `ClustersInner.getGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) foi agora preterida e foi substituído por [ `ClustersInner.updateGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK para Go

Atualizar para o [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) ou posterior do SDK do HDInsight para Go. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) foi agora preterida e foi substituído por [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Atualizar para o [Az PowerShell versão 2.0.0](https://www.powershellgallery.com/packages/Az) ou posterior para evitar interrupções.  Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por elas.
- `Grant-AzHDInsightHttpServicesAccess` foi agora preterida e foi substituída pela nova `Set-AzHDInsightGatewayCredential` cmdlet.
- `Get-AzHDInsightJobOutput` foi atualizada para suportar o acesso granular baseada em funções para a chave de armazenamento.
    - Os utilizadores com as funções Operador de Clusters, Contribuidor ou Proprietário do HDInsight não serão afetados.
    - Os utilizadores com a função de leitor terão de especificar o `DefaultStorageAccountKey` parâmetro explicitamente.
- `Revoke-AzHDInsightHttpServicesAccess` Agora foi preterido. Está agora sempre ativado HTTP, pelo que este cmdlet já não é necessária.
 Consulte o [az. Guia de migração de HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) para obter mais detalhes.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador

Um utilizador com o [contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ou [proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pode atribuir a função a [operador de Cluster do Hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) função aos utilizadores que gostaria de ter acesso de leitura/gravação para sensíveis Valores de configuração de cluster do HDInsight (por exemplo, as credenciais de gateway do cluster e as chaves de conta de armazenamento).

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

A maneira mais simples de adicionar esta atribuição de função é usando o `az role assignemnt create` comando na CLI do Azure.

> [!NOTE]
> Este comando deve ser executado por um utilizador com as funções de Contribuidor ou proprietário, como apenas o que podem conceder estas permissões. O `--assignee` é o endereço de e-mail do utilizador a quem pretende atribuir a função de operador de Cluster do HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Conceder a função ao nível do recurso (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Conceder a função ao nível do grupo de recursos

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Conceder a função ao nível da subscrição

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Em alternativa pode utilizar o portal do Azure para adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador. Consulte a documentação, [gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure - adicionar uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
