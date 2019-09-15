---
title: Criar espaços de trabalho com CLI do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar o CLI do Azure para criar um novo espaço de trabalho do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 08/30/2019
ms.openlocfilehash: 75487906e4323ea12a47d75164617212bd3e65d9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002636"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Criar um espaço de trabalho para Azure Machine Learning com CLI do Azure

Neste artigo, você aprenderá a criar um espaço de trabalho do Azure Machine Learning usando o CLI do Azure. O CLI do Azure fornece comandos para gerenciar recursos do Azure. A extensão de aprendizado de máquina para a CLI fornece comandos para trabalhar com Azure Machine Learning recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se você não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se você usar o [Azure cloud Shell](https://azure.microsoft.com//features/cloud-shell/), a CLI será acessada por meio do navegador e residirá na nuvem.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conectar a CLI à sua assinatura do Azure

> [!IMPORTANT]
> Se você estiver usando o Azure Cloud Shell, poderá ignorar esta seção. O Cloud Shell autentica automaticamente você usando a conta que você faz logon em sua assinatura do Azure.

Há várias maneiras de se autenticar em sua assinatura do Azure por meio da CLI. O mais básico é autenticar-se interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, você precisa abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a [https://aka.ms/devicelogin](https://aka.ms/devicelogin) navegação e a inserção de um código de autorização.

Para outros métodos de autenticação, consulte [entrar com CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Instalar a extensão do Machine Learning

Para instalar a extensão de Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Criar uma área de trabalho

O espaço de trabalho Azure Machine Learning se baseia nos seguintes serviços ou entidades do Azure:

> [!IMPORTANT]
> Se você não especificar um serviço existente do Azure, um será criado automaticamente durante a criação do espaço de trabalho. Você sempre deve especificar um grupo de recursos.

| Serviço | Parâmetro para especificar uma instância existente |
| ---- | ---- |
| **Grupo de recursos do Azure** | `-g <resource-group-name>`
| **Conta de Armazenamento do Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Cofre de Chaves do Azure** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O espaço de trabalho Azure Machine Learning deve ser criado dentro de um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo. Para __criar um novo grupo de recursos__, use o comando a seguir. Substituir `<resource-group-name>` pelo nome a ser usado para este grupo de recursos. Substitua `<location>` pela região do Azure a ser usada para este grupo de recursos:

> [!TIP]
> Você deve selecionar uma região onde Azure Machine Learning está disponível. Para obter informações, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta desse comando é semelhante ao JSON a seguir:

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

Para obter mais informações sobre como trabalhar com grupos de recursos, consulte [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Criar automaticamente os recursos necessários

Para criar um novo espaço de trabalho em que os __serviços são criados automaticamente__, use o seguinte comando:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

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

### <a name="use-existing-resources"></a>Usar recursos existentes

Para criar um espaço de trabalho que usa recursos existentes, você deve fornecer a ID para os recursos. Use os comandos a seguir para obter a ID para os serviços:

> [!IMPORTANT]
> Você não precisa especificar todos os recursos existentes. Você pode especificar um ou mais. Por exemplo, você pode especificar uma conta de armazenamento existente e o espaço de trabalho criará os outros recursos.

+ **Conta de armazenamento do Azure**:`az storage account show --name <storage-account-name> --query "id"`

    A resposta desse comando é semelhante ao texto a seguir e é a ID da sua conta de armazenamento:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Informações do aplicativo Azure**:

    1. Instale a extensão do Application insights:

        ```bash
        az extension add -n application-insights
        ```

    2. Obtenha a ID do seu serviço Application insights:

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A resposta desse comando é semelhante ao texto a seguir e é a ID para o serviço do Application insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: ' AZ keyvault show--Name < Key-Vault-Name >--consulta "ID"

    A resposta desse comando é semelhante ao texto a seguir e é a ID para o cofre de chaves:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registro de contêiner do Azure**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A resposta desse comando é semelhante ao texto a seguir e é a ID para o registro de contêiner:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > O registro de contêiner deve ter a [conta de administrador](/azure/container-registry/container-registry-authentication#admin-account) habilitada para poder ser usada com um espaço de trabalho Azure Machine Learning.

Depois que você tiver as IDs para os recursos que deseja usar com o espaço de trabalho, use o comando base `az workspace create -w <workspace-name> -g <resource-group-name>` e adicione os parâmetros e as IDs para os recursos existentes. Por exemplo, o comando a seguir cria um espaço de trabalho que usa um registro de contêiner existente:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

A saída desse comando é semelhante ao JSON a seguir:

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

## <a name="list-workspaces"></a>Listar áreas de trabalho

Para listar todos os espaços de trabalho para sua assinatura do Azure, use o seguinte comando:

```azurecli-interactive
az ml workspace list
```

A saída desse comando é semelhante ao JSON a seguir:

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

Para obter mais informações, consulte a documentação da [lista de espaços de trabalho AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Obter informações do espaço de trabalho

Para obter informações sobre um espaço de trabalho, use o seguinte comando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

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

Para obter mais informações, consulte a documentação [AZ ml Workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) Documentation.

## <a name="update-a-workspace"></a>Atualizar um espaço de trabalho

Para atualizar um espaço de trabalho, use o seguinte comando:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

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

Para obter mais informações, consulte a documentação de [atualização do espaço de trabalho AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Compartilhar um espaço de trabalho com outro usuário

Para compartilhar um espaço de trabalho com outro usuário em sua assinatura, use o seguinte comando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Para obter mais informações sobre o controle de acesso baseado em funções (RBAC) com Azure Machine Learning, consulte [gerenciar usuários e funções](how-to-assign-roles.md).

Para obter mais informações, consulte a documentação de [compartilhamento do espaço de trabalho AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) .

## <a name="sync-keys-for-dependent-resources"></a>Sincronizar chaves para recursos dependentes

Se você alterar as chaves de acesso de um dos recursos usados pelo seu espaço de trabalho, use o seguinte comando para sincronizar as novas chaves com o espaço de trabalho:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Para obter mais informações sobre como alterar chaves, consulte [regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md).

Para obter mais informações, consulte a documentação [AZ ml Workspace Sync-Keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Para excluir um espaço de trabalho depois que ele não for mais necessário, use o seguinte comando:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A exclusão de um espaço de trabalho não exclui o Application Insight, a conta de armazenamento, o cofre de chaves ou o registro de contêiner usado pelo espaço de trabalho.

Você também pode excluir o grupo de recursos, que exclui o espaço de trabalho e todos os outros recursos do Azure no grupo de recursos. Para excluir o grupo de recursos, use o seguinte comando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Para obter mais informações, consulte a documentação [AZ ml Workspace Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) .

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a extensão de CLI do Azure para aprendizado de máquina, consulte a documentação [AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) .
