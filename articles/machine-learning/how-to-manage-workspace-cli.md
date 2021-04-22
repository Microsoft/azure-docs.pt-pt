---
title: Criar espaços de trabalho com O Azure CLI
titleSuffix: Azure Machine Learning
description: Aprenda a usar a extensão Azure CLI para machine learning para criar um novo espaço de trabalho de Aprendizagem automática Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 8a00f5474fb73677125b85e48fcc2a42f34fdeb0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876408"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Criar um espaço de trabalho para a Azure Machine Learning com Azure CLI


Neste artigo, você aprende a criar um espaço de trabalho Azure Machine Learning usando o Azure CLI. O Azure CLI fornece comandos para a gestão dos recursos da Azure. A extensão de aprendizagem automática ao CLI fornece comandos para trabalhar com os recursos de Aprendizagem automática Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para utilizar os comandos CLI neste documento a partir do seu **ambiente local,** necessita do [Azure CLI](/cli/azure/install-azure-cli).

    Se utilizar o [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)o CLI é acedido através do navegador e vive na nuvem.

## <a name="limitations"></a>Limitações

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ligue o CLI à sua subscrição Azure

> [!IMPORTANT]
> Se estiver a utilizar a Azure Cloud Shell, pode saltar esta secção. A concha da nuvem autentica-o automaticamente utilizando a conta que inicia sessão na sua subscrição Azure.

Existem várias formas de autenticar a sua subscrição Azure a partir do CLI. O mais simples é autenticar interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e utilize o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, é necessário abrir um browser e seguir as instruções na linha de comando. As instruções envolvem navegar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduzir um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Para outros métodos de autenticação, consulte [iniciar sing com Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

O espaço de trabalho Azure Machine Learning conta com os seguintes serviços ou entidades da Azure:

> [!IMPORTANT]
> Se não especificar um serviço Azure existente, um deles será criado automaticamente durante a criação do espaço de trabalho. Deve sempre especificar um grupo de recursos. Ao anexar a sua própria conta de armazenamento, certifique-se de que satisfaz os seguintes critérios:
>
> * A conta de armazenamento _não_ é uma conta premium (Premium_LRS e Premium_GRS)
> * Tanto as capacidades de Azure Blob como Azure File ativadas
> * O espaço hierárquico namespace (ADLS Gen 2) está desativado
>
> Estes requisitos destinam-se apenas à conta de armazenamento _predefinido_ utilizada pelo espaço de trabalho.

| Serviço | Parâmetro para especificar uma instância existente |
| ---- | ---- |
| **Grupo de recursos Azure** | `-g <resource-group-name>`
| **Conta de Armazenamento Azure** | `--storage-account <service-id>` |
| **Insights de Aplicação Azure** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

O Registo de Contentores Azure (ACR) não suporta atualmente caracteres unicode em nomes de grupos de recursos. Para mitigar este problema, utilize um grupo de recursos que não contenha estes caracteres.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O espaço de trabalho Azure Machine Learning deve ser criado dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um novo. Para __criar um novo grupo de recursos,__ utilize o seguinte comando. `<resource-group-name>`Substitua-o pelo nome a utilizar para este grupo de recursos. `<location>`Substitua-a pela região Azure para utilizar para este grupo de recursos:

> [!TIP]
> Deve selecionar uma região onde o Azure Machine Learning está disponível. Para obter informações, consulte [os Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta deste comando é semelhante à seguinte JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Para obter mais informações sobre o trabalho com grupos de recursos, consulte [o grupo AZ](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Criar automaticamente recursos necessários

Para criar um novo espaço de trabalho onde os __serviços são criados automaticamente,__ utilize o seguinte comando:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> O nome do espaço de trabalho é insensível a caso.

A saída deste comando é semelhante à seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Rede virtual e ponto final privado

> [!IMPORTANT]
> A utilização de um espaço de trabalho de aprendizagem automática Azure com ligação privada não está disponível nas regiões do Governo Azure.

Se pretender restringir o acesso ao seu espaço de trabalho a uma rede virtual, pode utilizar os seguintes parâmetros:

* `--pe-name`: O nome do ponto final privado que é criado.
* `--pe-auto-approval`: Se as ligações privadas ao espaço de trabalho devem ser automaticamente aprovadas.
* `--pe-resource-group`: O grupo de recursos para criar o ponto final privado em. Deve ser o mesmo grupo que contém a rede virtual.
* `--pe-vnet-name`: A rede virtual existente para criar o ponto final privado em.
* `--pe-subnet-name`: O nome da sub-rede para criar o ponto final privado em. O valor predefinido é `default`.

Para obter mais informações sobre a utilização de um ponto final privado e rede virtual com o seu espaço de trabalho, consulte [o isolamento da rede virtual e a visão geral da privacidade.](how-to-network-security-overview.md)

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Chave gerida pelo cliente e espaço de trabalho de alto impacto do negócio

Por predefinição, os metadados para o espaço de trabalho são armazenados num caso DB Azure Cosmos que a Microsoft mantém. Estes dados são encriptados utilizando as teclas geridas pela Microsoft.

> [!NOTE]
> O Azure Cosmos DB __não__ é utilizado para armazenar informações como desempenho de modelos, informações registadas por experiências ou informações registadas a partir das implementações do seu modelo. Para obter mais informações sobre a monitorização destes itens, consulte a secção de [Monitorização e Registo](concept-azure-machine-learning-architecture.md) do artigo de arquitetura e conceitos.

Em vez de utilizar a chave gerida pela Microsoft, pode utilizar a sua própria chave. Ao fazê-lo, cria a instância DB do Azure Cosmos que armazena metadados na sua subscrição Azure. Utilize o `--cmk-keyvault` parâmetro para especificar o Cofre da Chave Azure que contém a chave e `--resource-cmk-uri` especificar o URL da chave dentro do cofre.

Antes de utilizar os `--cmk-keyvault` parâmetros e `--resource-cmk-uri` parâmetros, deve primeiro executar as seguintes ações:

1. Autorize a __App de Aprendizagem automática__ (em Gestão de Identidade e Acesso) com permissões de colaboradores na sua subscrição.
1. Siga os passos em [Chaves geridas pelo cliente](../cosmos-db/how-to-setup-cmk.md) para:
    * Registe-se no fornecedor Azure Cosmos DB
    * Criar e configurar um Cofre de Chaves Azure
    * Gerar uma chave

Não precisa de criar manualmente o exemplo DB do Azure Cosmos, um deles será criado para si durante a criação do espaço de trabalho. Esta instância DB Azure Cosmos será criada num grupo de recursos separado usando um nome baseado neste padrão: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Para limitar os dados que a Microsoft recolhe no seu espaço de trabalho, utilize o `--hbi-workspace` parâmetro. 

> [!IMPORTANT]
> A seleção de um impacto de alto negócio só pode ser feita quando se cria um espaço de trabalho. Não é possível alterar esta definição após a criação do espaço de trabalho.

Para obter mais informações sobre chaves geridas pelo cliente e espaço de trabalho de alto impacto empresarial, consulte [a segurança da Empresa para a Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Utilizar os recursos existentes

Para criar um espaço de trabalho que utilize os recursos existentes, você deve fornecer o ID para os recursos. Utilize os seguintes comandos para obter o ID para os serviços:

> [!IMPORTANT]
> Não tens de especificar todos os recursos existentes. Pode especificar um ou mais. Por exemplo, pode especificar uma conta de armazenamento existente e o espaço de trabalho criará outros recursos.

+ **Conta de Armazenamento Azure:**`az storage account show --name <storage-account-name> --query "id"`

    A resposta deste comando é semelhante ao seguinte texto, e é o ID para a sua conta de armazenamento:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Se quiser utilizar uma conta de Armazenamento Azure existente, não pode ser uma conta premium (Premium_LRS e Premium_GRS). Também não pode ter um espaço hierárquico de nomes (usado com Azure Data Lake Storage Gen2). Nem o armazenamento premium nem o espaço hierárquico são suportados com a conta de armazenamento _predefinido_ do espaço de trabalho. Pode utilizar um espaço de identificação premium ou hierárquico com contas de armazenamento _não padrão._

+ **Insights de aplicação da Azure:**

    1. Instale a extensão de insights de aplicação:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Obtenha a ID do seu serviço de insights de aplicação:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A resposta deste comando é semelhante ao seguinte texto, e é o ID para o seu serviço de insights de aplicação:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Cofre da Chave Azure:**`az keyvault show --name <key-vault-name> --query "ID"`

    A resposta deste comando é semelhante ao seguinte texto, e é o ID para o seu cofre chave:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registo do Contentor de Azure:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A resposta deste comando é semelhante ao seguinte texto, e é o ID para o registo do contentor:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > O registo do contentor deve ter a [conta de administração](../container-registry/container-registry-authentication.md#admin-account) ativada antes de poder ser utilizada com um espaço de trabalho de Aprendizagem automática Azure.

Assim que tiver os IDs para os recursos que pretende utilizar com o espaço de trabalho, utilize o `az workspace create -w <workspace-name> -g <resource-group-name>` comando base e adicione os parâmetros e iD(s) para os recursos existentes. Por exemplo, o seguinte comando cria um espaço de trabalho que utiliza um registo de contentores existente:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

A saída deste comando é semelhante à seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Lista de espaços de trabalho

Para listar todos os espaços de trabalho para a sua subscrição Azure, utilize o seguinte comando:

```azurecli-interactive
az ml workspace list
```

A saída deste comando é semelhante à seguinte JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Para mais informações, consulte a documentação da [lista de espaços de trabalho az ml.](/cli/azure/ml/workspace#az_ml_workspace_list)

## <a name="get-workspace-information"></a>Obtenha informações sobre o espaço de trabalho

Para obter informações sobre um espaço de trabalho, utilize o seguinte comando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante à seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Para mais informações, consulte a documentação do [espaço de trabalho az ml.](/cli/azure/ml/workspace#az_ml_workspace_show)

## <a name="update-a-workspace"></a>Atualizar um espaço de trabalho

Para atualizar um espaço de trabalho, utilize o seguinte comando:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante à seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Para obter mais informações, consulte a documentação [de atualização do espaço de trabalho az ml.](/cli/azure/ml/workspace#az_ml_workspace_update)

## <a name="share-a-workspace-with-another-user"></a>Partilhe um espaço de trabalho com outro utilizador

Para partilhar um espaço de trabalho com outro utilizador na sua subscrição, utilize o seguinte comando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Para obter mais informações sobre o controlo de acesso baseado em funções Azure (Azure RBAC) com Azure Machine Learning, consulte [Gerir utilizadores e funções](how-to-assign-roles.md).

Para mais informações, consulte o [espaço de trabalho az ml partilhar](/cli/azure/ml/workspace#az_ml_workspace_share) a documentação.

## <a name="sync-keys-for-dependent-resources"></a>Chaves sincronizadas para recursos dependentes

Se alterar as teclas de acesso para um dos recursos utilizados pelo seu espaço de trabalho, leva cerca de uma hora para o espaço de trabalho sincronizar com a nova chave. Para forçar o espaço de trabalho a sincronizar imediatamente as novas teclas, utilize o seguinte comando:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Para obter mais informações sobre a mudança de teclas, consulte [as teclas](how-to-change-storage-access-key.md)de acesso ao armazenamento Regenerar .

Para obter mais informações, consulte a documentação [az ml workspace sync-keys.](/cli/azure/ml/workspace#az_ml_workspace_sync-keys)

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Para eliminar um espaço de trabalho depois de já não ser necessário, utilize o seguinte comando:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A eliminação de um espaço de trabalho não elimina a visão da aplicação, a conta de armazenamento, o cofre ou o registo do contentor utilizado pelo espaço de trabalho.

Também pode eliminar o grupo de recursos, que elimina o espaço de trabalho e todos os outros recursos Azure no grupo de recursos. Para eliminar o grupo de recursos, utilize o seguinte comando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Para obter mais informações, consulte o [espaço de trabalho az ml para eliminar](/cli/azure/ml/workspace#az_ml_workspace_delete) a documentação.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Mover o espaço de trabalho

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Eliminação do Registo do Contentor de Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo do Contentor Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisa primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a extensão do Azure CLI para machine learning, consulte a documentação [az ml.](/cli/azure/ml)