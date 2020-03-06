---
title: Criar espaços de trabalho com o Azure CLI
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Azure CLI para criar um novo espaço de trabalho azure machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 5e90416b23b057ad5079a551242895802ac641c9
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399126"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Criar um espaço de trabalho para azure machine learning com azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a criar um espaço de trabalho azure machine learning utilizando o Azure CLI. O Azure CLI fornece comandos para a gestão dos recursos Azure. A extensão de aprendizagem automática ao CLI fornece comandos para trabalhar com recursos de Aprendizagem Automática Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição Azure.** Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Para utilizar os comandos CLI neste documento a partir do seu **ambiente local,** você precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se utilizar a [Concha de Nuvem Azure,](https://azure.microsoft.com//features/cloud-shell/)o CLI é acedido através do navegador e vive na nuvem.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ligue o CLI à sua subscrição Azure

> [!IMPORTANT]
> Se estiver a utilizar a Casca de Nuvem Azure, pode saltar esta secção. A casca de nuvem autentica-o automaticamente utilizando a conta que inicia na sua subscrição Azure.

Existem várias formas de autenticar a sua subscrição Azure a partir do CLI. O mais básico é autenticar interativamente usando um browser. Para autenticar interativamente, abra uma linha de comando ou terminal e utilize o seguinte comando:

```azurecli
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, tem de abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem navegar para [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduzir um código de autorização.

Para outros métodos de autenticação, consulte [Iniciar sessão com o Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Instale a extensão de aprendizagem automática

Para instalar a extensão de aprendizagem automática, utilize o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Criar uma área de trabalho

O espaço de trabalho Azure Machine Learning conta com os seguintes serviços ou entidades Azure:

> [!IMPORTANT]
> Se não especificar um serviço Azure existente, um será criado automaticamente durante a criação do espaço de trabalho. Deve sempre especificar um grupo de recursos.

| Serviço | Parâmetro para especificar uma instância existente |
| ---- | ---- |
| **Grupo de recursos Azure** | `-g <resource-group-name>`
| **Conta de Armazenamento do Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Cofre de Chaves do Azure** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

O espaço de trabalho Azure Machine Learning deve ser criado dentro de um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo. Para criar um novo grupo de __recursos,__ utilize o seguinte comando. Substitua `<resource-group-name>` com o nome a utilizar para este grupo de recursos. Substitua `<location>` pela região azure para utilizar para este grupo de recursos:

> [!TIP]
> Deve selecionar uma região onde o Azure Machine Learning está disponível. Para obter informações, consulte [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

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

Para obter mais informações sobre o trabalho com grupos de recursos, consulte [o grupo AZ](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Criar automaticamente recursos necessários

Para criar um novo espaço de trabalho onde os __serviços são automaticamente criados,__ utilize o seguinte comando:

> [!TIP]
> Os comandos nesta secção criam um espaço de trabalho de edição básica. Para criar um espaço de trabalho empresarial, utilize o interruptor `--sku enterprise` com o comando `az ml workspace create`. Para mais informações sobre as edições de Machine Learning Azure, consulte [o que é o Azure Machine Learning.](overview-what-is-azure-ml.md#sku)

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> O nome do espaço de trabalho é insensível a casos.

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

### <a name="use-existing-resources"></a>Utilizar os recursos existentes

Para criar um espaço de trabalho que utilize os recursos existentes, deve fornecer o ID para os recursos. Utilize os seguintes comandos para obter o ID para os serviços:

> [!IMPORTANT]
> Não precisa especificar todos os recursos existentes. Pode especificar um ou mais. Por exemplo, pode especificar uma conta de armazenamento existente e o espaço de trabalho irá criar os outros recursos.

+ Conta de **Armazenamento Azure**: `az storage account show --name <storage-account-name> --query "id"`

    A resposta deste comando é semelhante ao seguinte texto, e é o ID para a sua conta de armazenamento:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Insights de aplicação azure:**

    1. Instale a extensão de insights da aplicação:

        ```bash
        az extension add -n application-insights
        ```

    2. Obtenha a identificação do seu serviço de informação de aplicação:

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A resposta deste comando é semelhante ao seguinte texto, e é o ID para o seu serviço de insights de aplicação:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Cofre chave Azure**: `az keyvault show --name <key-vault-name> --query "ID"`

    A resposta deste comando é semelhante ao seguinte texto, e é o ID para o seu cofre chave:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registo de contentores de Azure**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A resposta deste comando é semelhante ao seguinte texto, e é o ID para o registo de contentores:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > O registo do recipiente deve ter a [conta de administração](/azure/container-registry/container-registry-authentication#admin-account) ativada antes de poder ser utilizada com um espaço de trabalho de Aprendizagem automática Azure.

Uma vez que tenha os IDs para os recursos(s) que pretende utilizar com o espaço de trabalho, use a base `az workspace create -w <workspace-name> -g <resource-group-name>` comando e adicione os parâmetros(s) e ID(s) para os recursos existentes. Por exemplo, o seguinte comando cria um espaço de trabalho que utiliza um registo de contentores existente:

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

Para mais informações, consulte a documentação da lista de [trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)

## <a name="get-workspace-information"></a>Obtenha informações sobre espaço de trabalho

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

Para mais informações, consulte a documentação do [espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)

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

Para mais informações, consulte a documentação da atualização do [espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)

## <a name="share-a-workspace-with-another-user"></a>Partilhar um espaço de trabalho com outro utilizador

Para partilhar um espaço de trabalho com outro utilizador na sua subscrição, utilize o seguinte comando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC) com o Azure Machine Learning, consulte [Gerir utilizadores e funções](how-to-assign-roles.md).

Para mais informações, consulte a documentação do [espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)

## <a name="sync-keys-for-dependent-resources"></a>Chaves de sincronização para recursos dependentes

Se alterar as teclas de acesso para um dos recursos utilizados pelo seu espaço de trabalho, utilize o seguinte comando para sincronizar as novas teclas com o espaço de trabalho:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Para obter mais informações sobre as chaves de troca, consulte [Reregenerar as chaves](how-to-change-storage-access-key.md)de acesso ao armazenamento .

Para mais informações, consulte a documentação de chaves de sincronização do [espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Para eliminar um espaço de trabalho depois de já não ser necessário, utilize o seguinte comando:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A eliminação de um espaço de trabalho não elimina a informação da aplicação, a conta de armazenamento, o cofre-chave ou o registo de contentores utilizados pelo espaço de trabalho.

Também pode eliminar o grupo de recursos, que elimina o espaço de trabalho e todos os outros recursos Do Azure no grupo de recursos. Para eliminar o grupo de recursos, utilize o seguinte comando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Para mais informações, consulte o espaço de [trabalho az ml para apagar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) documentação.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Movendo o espaço de trabalho

> [!WARNING]
> A mudança do seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou a mudança da subscrição própria para um novo inquilino, não é suportada. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Apagando o Registo de Contentores Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo de Contentores Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisar primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a extensão Azure CLI para aprendizagem automática, consulte a documentação [az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)
