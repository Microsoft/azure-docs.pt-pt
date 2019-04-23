---
title: Migrar para o acesso granular baseada em funções para configurações de cluster - Azure HDInsight
description: Saiba mais sobre as alterações necessárias para migrar para o acesso granular baseada em funções para configurações de cluster.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006238"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para o acesso granular baseada em funções para configurações de cluster

Introduzimos algumas alterações importantes para dar suporte a mais acesso baseado em funções para obter informações confidenciais detalhado. Como parte de um destes for alterado, alguns **ação poderá ser necessária** se estiver a utilizar um da [afetados entidades/cenários](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>O que está a mudar?

Anteriormente, segredos podem ser obtidos através da API do HDInsight por possessing o proprietário, contribuinte ou leitor de utilizadores de cluster [funções RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
Doravante, estes segredos já não estará acessíveis aos utilizadores com a função de leitor. Também estamos estará introduzindo uma nova função de "Operador de Cluster do Hdinsight", que é ser capaz de obter segredos sem a ser concedidas as permissões administrativas do Contribuidor ou proprietário. Para resumir:

| Função                                  | Anteriormente                                                                                       | Agora       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | -Acesso de leitura, incluindo segredos                                                                   | -Acesso de leitura, **excluindo** segredos |           |   |   |
| Operador de Cluster do HDInsight<br>(Nova função) | N/A                                                                                              | -Acesso de leitura/gravação, incluindo segredos         |   |   |
| Contribuinte                           | -Acesso de leitura/gravação, incluindo segredos<br>-Criar e gerir todos os tipos de recursos do Azure.     | Sem alteração |
| Proprietário                                 | -Acesso de leitura/gravação, incluindo segredos<br>-Total acesso a todos os recursos<br>-Delegar o acesso a outras pessoas | Sem alteração |

Para obter informações sobre como adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador para conceder-lhes acesso de leitura/escrita a segredos de cluster, consulte a abaixo da secção [adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Estou afetado por estas alterações?

As entidades e os cenários seguintes são afetados:

- [API](#api): Os utilizadores que utilizam o `/configurations` ou `/configurations/{configurationName}` pontos de extremidade.
- [Ferramentas do HDInsight do Azure para Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) _ de versão e abaixo.
- [Azure Toolkit para IntelliJ](#azure-toolkit-for-intellij) _ de versão e abaixo.
- [Azure Toolkit para Eclipse](#azure-toolkit-for-eclipse) _ de versão e abaixo.
- [SDK para .NET](#sdk-for-net)
    - [as versões 1.x ou 2.x](#versions-1x-and-2x): Os utilizadores que utilizam o `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` ou `DisableHttp` métodos da classe ConfigurationsOperationsExtensions.
    - [versões 3.x e até](#versions-3x-and-up): Os utilizadores que utilizam o `EnableHttp`, `DisableHttp`, `Update`, ou `Get` métodos a partir do `ConfigurationsOperationsExtensions` classe.
- [SDK para Python](#sdk-for-python): Os utilizadores que utilizam o `get` ou `update` métodos da classe ConfigurationsOperations.
- [SDK para Java](#sdk-for-java): Os utilizadores que utilizam o `update` ou `get` métodos da classe ConfigurationsInner.
- [SDK para Go](#sdk-for-go): Os utilizadores que utilizam o `Get` ou `Update` métodos da estrutura ConfigurationsClient.

Veja as secções de abaixo (ou use os links acima) para ver a migração passos para o seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou preteridas:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informações confidenciais removidas) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Anteriormente, é usado para obter os tipos de configuração individuais (incluindo segredos).
    - Esta chamada de API agora irá devolver os tipos de configuração individuais com segredos omitidos. Para obter todas as configurações, incluindo segredos, utilize a nova [POST /configurations]() chamar. Para obter apenas as definições do gateway, utilize a nova [POST /getGatewaySettings]() chamar.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (preterido) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Utilizado anteriormente para obter todas as configurações de (incluindo segredos)
    - Esta chamada à API já não ser suportada. Para obter todas as configurações no futuro, utilize a nova [POST /configurations]() chamar. Para obter as configurações com os parâmetros com diferenciação for omitidos, utilize o [GET /configurations/ {configurationName}]() chamar.
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (deprecated) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Anteriormente, é utilizado para atualizar as credenciais de gateway.
    - Esta chamada à API será preterida e já não é suportada. Utilizar a nova [POST /updateGatewaySettings]() em vez disso.

A substituição seguinte que APIs foram adicionadas:</span>

- **POST /configurations** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Utilize esta API para obter todas as configurações, incluindo segredos.
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - Utilize esta API para obter as definições do gateway.
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - Utilize esta API para atualizar as definições do gateway (nome de utilizador e/ou palavra-passe).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Ferramentas do Azure HDInsight para Visual Studio Code

Se estiver a utilizar a versão 1.1.1 ou o mais antigo, atualize para o [versão mais recente do Azure HDInsight Tools para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Se estiver a utilizar a versão 3.21.0 ou o mais antigo, atualize para o [a versão mais recente do Azure Toolkit para IntelliJ Plug-in do](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit para Eclipse

Se estiver a utilizar a versão de 2019-03-29 ou o mais antigo, atualize para a versão mais recente do Azure Toolkit para Eclipse evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>As versões 1.x e 2.x

Atualize para [a versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do SDK do HDInsight para .NET. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations` serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize `ClusterOperationsExtensions.ListConfigurations` daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster.
    - Para obter credenciais de gateway HTTP apenas, utilize `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` foi agora preterida e foi substituído por `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` foi agora preterida e foi substituído por `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` foram preteridas. Está agora sempre ativado HTTP, para que esses métodos já não são necessários.

#### <a name="versions-3x-and-up"></a>Versões 3.x e até

Atualize para [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) do SDK do HDInsight para .NET. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize `ConfigurationOperationsExtensions.List` daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) foi agora preterida e foi substituído por `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) foram preteridas. Está agora sempre ativado HTTP, para que esses métodos já não são necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualize para [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) do SDK do HDInsight para Python. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) foi agora preterida e foi substituído por [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK para Java

Atualize para [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) do SDK do HDInsight para Java. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize `ConfigurationsInner.list` daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize `ConfigurationsOperations.get_gateway_settings`.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) foi agora preterida e foi substituído por `ClusterOperationsExtensions.update_gateway_settings`.

### <a name="sdk-for-go"></a>SDK para Go

Atualize para [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) do HDInsight SDK para Go. Modificações do código mínimo poderão ser necessárias se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) foi agora preterida e foi substituído por [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador

Um utilizador com o [contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ou [proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) função pode conceder a função de operador de Cluster do HDInsight para os utilizadores que pretende ter acesso de leitura/gravação a segredos de cluster do HDInsight, como credenciais de gateway do cluster e as chaves de conta de armazenamento.

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

É a maneira mais simples de adicionar esta atribuição de função utilizando o seguinte comando na CLI do Azure:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> Este comando deve ser executado por um utilizador com as funções de Contribuidor ou proprietário, como apenas o que podem conceder estas permissões. O `--assignee` é o endereço de e-mail do utilizador a quem pretende atribuir a função de operador de Cluster do HDInsight.

O comando acima concede esta função ao nível da subscrição. Em vez disso, basta conceder esta função ao nível do grupo de recursos, pode modificar o comando da seguinte forma:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Em alternativa pode utilizar o portal do Azure para adicionar a atribuição de função de operador de Cluster do HDInsight para um utilizador. Consulte a documentação, [gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure - adicionar uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
