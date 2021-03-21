---
title: Configurações de cluster de suporte baseado em função granular Azure HDInsight
description: Conheça as alterações necessárias como parte da migração para o acesso granular baseado em funções para configurações de cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a30768f4904c9e5be2edc020f12260cf3a54c889
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425894"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para o acesso granular baseado em funções para as configurações de cluster

Estamos a introduzir algumas alterações importantes para apoiar um acesso mais fino baseado em papéis para obter informações sensíveis. Como parte destas alterações, poderão ser necessárias algumas ações até 3 de setembro de **2019** se estiver a utilizar uma das [entidades/cenários afetados.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>O que está a mudar?

Anteriormente, os segredos poderiam ser obtidos através da API HDInsight por utilizadores de clusters que possuíssem as funções de Proprietário, Colaborador ou Leitor [Azure](../role-based-access-control/rbac-and-directory-admin-roles.md), uma vez que estavam disponíveis para qualquer pessoa com `*/read` a permissão. Os segredos são definidos como valores que poderiam ser usados para obter um acesso mais elevado do que o papel de um utilizador deve permitir. Estes incluem valores como credenciais de gateway de cluster HTTP, chaves de conta de armazenamento e credenciais de base de dados.

A partir de 3 de setembro de 2019, o acesso a estes segredos exigirá a `Microsoft.HDInsight/clusters/configurations/action` permissão, o que significa que já não podem ser acedidos pelos utilizadores com a função Reader. As funções que têm esta permissão são o Colaborador, o Proprietário, e a nova função do Operador de Cluster HDInsight (mais sobre isso abaixo).

Estamos também a introduzir uma nova função [de Operador de Cluster HDInsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) que será capaz de recuperar segredos sem que lhes sejam concedidas as permissões administrativas do Colaborador ou Proprietário. Para resumir:

| Função                                  | Anteriormente                                                                                       | Indo para a frente       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | - Leia o acesso, incluindo segredos.                                                                   | - Ler acesso, **excluindo** segredos |           |   |   |
| Operador de cluster HDInsight<br>(Novo Papel) | N/D                                                                                              | - Ler/escrever acesso, incluindo segredos         |   |   |
| Contribuinte                           | - Ler/escrever acesso, incluindo segredos.<br>- Criar e gerir todos os tipos de recursos Azure.<br>- Executar ações de guião.     | Sem alterações |
| Proprietário                                 | - Ler/escrever acesso, incluindo segredos.<br>- Acesso total a todos os recursos<br>- Delegar acesso aos outros.<br>- Executar ações de guião. | Sem alterações |

Para obter informações sobre como adicionar a atribuição de função do Operador do Cluster HDInsight a um utilizador para lhes conceder acesso de leitura/escrita aos segredos do cluster, consulte a secção abaixo, [Adicione a atribuição de função de operador de cluster HDInsight a um utilizador](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sou afetado por estas mudanças?

As seguintes entidades e cenários são afetados:

- [API](#api): Utilizadores que utilizem os `/configurations` `/configurations/{configurationName}` pontos finais ou finais.
- [Ferramentas Azure HDInsight para Código de Estúdio Visual](#azure-hdinsight-tools-for-visual-studio-code) versão 1.1.1 ou abaixo.
- [Azure Toolkit para a versão IntelliJ](#azure-toolkit-for-intellij) 3.20.0 ou abaixo.
- [Azure Data Lake e Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) abaixo versão 2.3.9000.1.
- [Azure Toolkit para a](#azure-toolkit-for-eclipse) versão Eclipse 3.15.0 ou abaixo.
- [SDK para .NET](#sdk-for-net)
    - [versões 1.x ou 2.x](#versions-1x-and-2x): Utilizadores que utilizem o `GetClusterConfigurations` , , ou `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` `DisableHttp` métodos da classe ConfiguraçõesOperationsExtensions.
    - [versões 3.x e até](#versions-3x-and-up): Utilizadores que utilizem o `Get` , , ou `Update` `EnableHttp` `DisableHttp` métodos da `ConfigurationsOperationsExtensions` classe.
- [SDK para Python](#sdk-for-python): Utilizadores que utilizem o `get` ou `update` métodos da `ConfigurationsOperations` classe.
- [SDK para Java](#sdk-for-java): Utilizadores que utilizem o `update` ou `get` métodos da `ConfigurationsInner` classe.
- [SDK para Go](#sdk-for-go): Utilizadores que utilizem o `Get` ou `Update` métodos a partir da `ConfigurationsClient` estrutura.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) abaixo da versão 2.0.0.
Consulte as secções abaixo (ou use os links acima) para ver os passos de migração para o seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou depreciadas:

- [**GET /configurações/{configuraçãoName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informação sensível removida)
    - Anteriormente utilizado para obter tipos de configuração individuais (incluindo segredos).
    - A partir de 3 de setembro de 2019, esta chamada da API irá agora devolver os tipos de configuração individuais com segredos omitidos. Para obter todas as configurações, incluindo segredos, utilize a nova chamada POST/configurações. Para obter apenas definições de gateway, utilize a nova chamada POST/getGatewaySettings.
- [**GET /configurações**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (depreciadas)
    - Anteriormente usado para obter todas as configurações (incluindo segredos)
    - A partir de 3 de setembro de 2019, esta chamada da API será depreciada e deixará de ser apoiada. Para obter todas as configurações em curso, utilize a nova chamada POST/configurações. Para obter configurações com parâmetros sensíveis omitidos, utilize a chamada GET /configurations/{ConfiguraçãoName} .
- [**POST /configurações/{configuraçãoName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (precotado)
    - Anteriormente usado para atualizar credenciais de gateway.
    - A partir de 3 de setembro de 2019, esta chamada da API será depreciada e deixará de ser apoiada. Utilize, em vez disso, os novos POST/updateGatewaySettings.

Foram adicionadas as seguintes APIs de substituição:</span>

- [**POST /configurações**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Utilize esta API para obter todas as configurações, incluindo segredos.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Utilize esta API para obter definições de gateway.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilize esta API para atualizar as definições do gateway (nome de utilizador e/ou palavra-passe).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Ferramentas Azure HDInsight para Código de Estúdio Visual

Se estiver a utilizar a versão 1.1.1 ou abaixo, atualize a [versão mais recente do Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Se estiver a utilizar a versão 3.20.0 ou abaixo, atualize a [versão mais recente do kit de ferramentas Azure para o plugin IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Ferramentas de análise do Lago de Dados Azure e do stream para estúdio visual

Atualização para a versão 2.3.9000.1 ou mais tarde de [Azure Data Lake e Stream Analytics Tools para o Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) para evitar interrupções.  Para obter ajuda na atualização, consulte a nossa documentação, [atualizar ferramentas do Lago de Dados para o Estúdio Visual.](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Se estiver a utilizar a versão 3.15.0 ou abaixo, atualize a [versão mais recente do Azure Toolkit para Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) para evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>Versões 1.x e 2.x

Atualização para a [versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do HDInsight SDK para .NET. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations`**deixarão de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros sensíveis, `ClusterOperationsExtensions.ListConfigurations` utilize-a para a frente.  Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster.
    - Para obter apenas credenciais de gateway HTTP, use `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` está agora precotado e foi substituído por `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` está agora precotado e foi substituído por `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` agora são depreciadas. HTTP está agora sempre ativado, pelo que estes métodos já não são necessários.

#### <a name="versions-3x-and-up"></a>Versões 3.x e acima

Atualize para a [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou mais tarde do HDInsight SDK para .NET. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get)**deixarão de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros sensíveis, [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) utilize-a para a frente.Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster. 
    - Para obter apenas credenciais de gateway HTTP, use [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) está agora precotado e foi substituído por [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) e [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) agora são depreciadas. HTTP está agora sempre ativado, pelo que estes métodos já não são necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualize para a [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou mais tarde do HDInsight SDK para Python. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**deixarão de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros sensíveis, [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) utilize-a para a frente.Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster. 
    - Para obter apenas credenciais de gateway HTTP, use [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) está agora precotado e foi substituído por [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>SDK para Java

Atualização para a [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) ou mais tarde do HDInsight SDK para Java. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- `ConfigurationsInner.get`**deixarão de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
- `ConfigurationsInner.update` está agora depreciada.

### <a name="sdk-for-go"></a>SDK para ir

Atualize para a [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) ou mais tarde do HDInsight SDK para Go. Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get)**deixarão de devolver parâmetros sensíveis** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros sensíveis, [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) utilize-a para a frente.Note que os utilizadores com a função 'Reader' não poderão utilizar este método. Isto permite o controlo granular sobre o qual os utilizadores podem aceder a informações sensíveis para um cluster. 
    - Para obter apenas credenciais de gateway HTTP, use [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) está agora precotado e foi substituído por [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Atualize a [versão 2.0.0](https://www.powershellgallery.com/packages/Az) ou posterior do Az PowerShell para evitar interrupções.  Podem ser necessárias modificações mínimas de código se estiver a utilizar um método afetado por estas alterações.
- `Grant-AzHDInsightHttpServicesAccess` está agora depreciado e foi substituído pelo novo `Set-AzHDInsightGatewayCredential` cmdlet.
- `Get-AzHDInsightJobOutput` foi atualizado para apoiar o acesso granular baseado em funções à chave de armazenamento.
    - Os utilizadores com as funções Operador de Clusters, Contribuidor ou Proprietário do HDInsight não serão afetados.
    - Os utilizadores com apenas a função Reader terão de especificar explicitamente o `DefaultStorageAccountKey` parâmetro.
- `Revoke-AzHDInsightHttpServicesAccess` está agora depreciada. HTTP está agora sempre ativado, pelo que este cmdlet já não é necessário.
 Veja o [az. Guia de migração HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) para mais detalhes.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicione a atribuição de função do operador de cluster HDInsight a um utilizador

Um utilizador com a função [Proprietário](../role-based-access-control/built-in-roles.md#owner) pode atribuir a função [de Operador de Cluster HDInsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) aos utilizadores que gostaria de ter acesso de leitura/escrita a valores sensíveis de configuração do cluster HDInsight (tais como credenciais de gateway de cluster e chaves de conta de armazenamento).

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

A forma mais simples de adicionar esta tarefa de função é usando o `az role assignment create` comando em Azure CLI.

> [!NOTE]
> Este comando deve ser gerido por um utilizador com a função Proprietário, uma vez que só eles podem conceder estas permissões. `--assignee`É o nome do principal de serviço ou endereço de e-mail do utilizador a quem pretende atribuir a função de Operador de Cluster HDInsight. Se receber um erro de permissões insuficiente, consulte as FAQ abaixo.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Papel de subvenção ao nível do recurso (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Papel de subvenção ao nível do grupo de recursos

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Papel de subvenção ao nível da subscrição

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Em alternativa, pode utilizar o portal Azure para adicionar a atribuição de função de cluster hdInsight a um utilizador. Consulte a documentação, [atribua funções Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="faq"></a>FAQ

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Porque estou a ver uma resposta 403 (Proibido) após atualizar os meus pedidos e/ou ferramenta da API?

As configurações do cluster estão agora por trás do controlo de acesso baseado em funções granular e exigem a `Microsoft.HDInsight/clusters/configurations/*` permissão para aceder a elas. Para obter esta permissão, atribua a função hdInsight Cluster Operator, Colaborador ou Proprietário ao utilizador ou principal de serviço que tenta aceder às configurações.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Por que vejo "privilégios insuficientes para completar a operação" ao executar o comando Azure CLI para atribuir o papel de Operador de Cluster HDInsight a outro utilizador ou diretor de serviço?

Além de ter a função Proprietário, o utilizador ou o principal de serviço que executa o comando precisa de ter permissões AD suficientes para procurar as identificações do objeto do destinatário. Esta mensagem indica permissões AD Azure insuficientes. Tente substituir o `-–assignee` argumento e forneça o `–assignee-object-id` ID do objeto do destinatário como parâmetro em vez do nome (ou o ID principal no caso de uma identidade gerida). Consulte a secção de parâmetros opcionais da atribuição de [funções az crie documentação](/cli/azure/role/assignment#az-role-assignment-create) para mais informações.

Se isto ainda não funcionar, contacte o seu administrador Azure AD para obter as permissões corretas.

### <a name="what-will-happen-if-i-take-no-action"></a>O que vai acontecer se eu não tomar nenhuma ação?

A partir de 3 de setembro de 2019, `GET /configurations` as `POST /configurations/gateway` chamadas deixarão de devolver qualquer informação e a `GET /configurations/{configurationName}` chamada deixará de devolver parâmetros sensíveis, como chaves de conta de armazenamento ou a senha de cluster. O mesmo acontece com os métodos SDK correspondentes e os cmdlets PowerShell.

Se estiver a utilizar uma versão mais antiga de uma das ferramentas para Visual Studio, VSCode, IntelliJ ou Eclipse acima mencionadas, elas deixarão de funcionar até atualizar.

Para obter informações mais detalhadas, consulte a secção correspondente deste documento para o seu cenário.