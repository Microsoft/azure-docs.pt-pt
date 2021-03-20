---
title: Use o CLI Azure para atribuir uma função Azure para o acesso a dados
titleSuffix: Azure Storage
description: Saiba como usar o Azure CLI para atribuir permissões a um diretor de segurança do Azure Ative Directory com o controlo de acesso baseado em funções Azure (Azure RBAC). O Azure Storage suporta funções personalizadas incorporadas e Azure para autenticação via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: c42061520b73966f2cd516716039d78c2b9cbeb8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375991"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Use o CLI Azure para atribuir uma função Azure para acesso a dados de bolhas e filas

O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do [controlo de acesso baseado em funções da Azure (Azure RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções incorporadas do Azure que englobam conjuntos comuns de permissões usadas para aceder a dados de bolhas ou filas.

Quando um papel de Azure é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser alargado ao nível da subscrição, do grupo de recursos, da conta de armazenamento ou de um contentor ou fila individual. Um diretor de segurança Azure AD pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos da Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Este artigo descreve como usar o Azure CLI para listar as funções incorporadas do Azure e atribuí-las aos utilizadores. Para obter mais informações sobre a utilização do Azure CLI, consulte [Azure Command-Line Interface (CLI)](/cli/azure).

## <a name="azure-roles-for-blobs-and-queues"></a>Papéis de azul para bolhas e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o âmbito de recursos

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Lista de funções disponíveis do Azure

Para listar as funções disponíveis em incorporado Azure com O Azure CLI, utilize o comando da [lista de definição de função az:](/cli/azure/role/definition#az-role-definition-list)

```azurecli-interactive
az role definition list --out table
```

Você verá as funções de dados de armazenamento Azure incorporados listados, juntamente com outras funções incorporadas para Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Atribua um papel de Azure a um diretor de segurança

Para atribuir um papel de Azure a um principal de segurança, use a [atribuição de funções az criar](/cli/azure/role/assignment#az-role-assignment-create) comando. O formato do comando pode diferir com base no âmbito da atribuição. Os exemplos a seguir mostram como atribuir uma função a um utilizador em vários âmbitos, mas pode usar o mesmo comando para atribuir uma função a qualquer diretor de segurança.

> [!IMPORTANT]
> Quando cria uma conta de Armazenamento Azure, não lhe são atribuídas automaticamente permissões de acesso aos dados através do Azure AD. Deve atribuir-se explicitamente a si mesmo um papel Azure RBAC para o acesso a dados. Pode atribuí-lo ao nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.
>
> Se a conta de armazenamento estiver bloqueada com um bloqueio de leitura do Azure Resource Manager, então o bloqueio impede a atribuição de funções Azure RBAC que são procuradas na conta de armazenamento ou num recipiente de dados (recipiente blob ou fila).

### <a name="container-scope"></a>Âmbito do contentor

Para atribuir uma função a um recipiente, especifique uma cadeia que contenha o âmbito do recipiente para o `--scope` parâmetro. A margem de manobra para um recipiente está na forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

O exemplo a seguir atribui a função **de Contribuinte de Dados blob de armazenamento** a um utilizador, com o alcance do nível do recipiente. Certifique-se de que substitui os valores da amostra e os valores do espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Âmbito de fila

Para atribuir uma função a uma fila, especifique uma cadeia que contenha o âmbito da fila para o `--scope` parâmetro. A margem para uma fila está na forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

O exemplo a seguir atribui a função **de Contribuinte de Dados de Fila de Armazenamento** a um utilizador, com um alcance para o nível da fila. Certifique-se de que substitui os valores da amostra e os valores do espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Âmbito da conta de armazenamento

Para atribuir uma função a uma conta de armazenamento, especifique o âmbito do recurso de conta de armazenamento para o `--scope` parâmetro. A margem para uma conta de armazenamento está na forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O exemplo a seguir mostra como atribuir a função de Leitor de **Dados blob de armazenamento** a um utilizador ao nível da conta de armazenamento. Certifique-se de que substitui os valores da amostra pelos seus próprios valores: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Âmbito de grupo de recursos

Para atribuir uma função ao grupo de recursos, especifique o nome do grupo de recursos ou o ID para o `--resource-group` parâmetro. O exemplo a seguir atribui o papel **de Leitor de Dados de Fila de Armazenamento** a um utilizador ao nível do grupo de recursos. Certifique-se de que substitui os valores da amostra e dos lugares nos parênteses pelos seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Âmbito de subscrição

Para atribuir uma função a uma peça de serviço à subscrição, especifique o âmbito da subscrição do `--scope` parâmetro. A margem para uma subscrição está na forma:

```
/subscriptions/<subscription>
```

O exemplo a seguir mostra como atribuir a função de Leitor de **Dados blob de armazenamento** a um utilizador ao nível da conta de armazenamento. Certifique-se de que substitui os valores da amostra pelos seus próprios valores: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passos seguintes

- [Adicione ou remova atribuições de funções Azure utilizando o módulo Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Utilize o módulo Azure PowerShell para atribuir uma função Azure para acesso a dados de bolhas e filas](storage-auth-aad-rbac-powershell.md)
- [Utilizar o portal do Azure para atribuir uma função do Azure para aceder a dados de blobs e filas](storage-auth-aad-rbac-portal.md)
