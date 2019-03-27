---
title: Utilizar o Azure PowerShell para gerir os direitos de acesso do Azure AD para dados de BLOBs e filas com RBAC - armazenamento do Azure
description: Utilize o Azure PowerShell para atribuir acesso a contentores e de filas com controlo de acesso baseado em funções (RBAC). O armazenamento do Azure suporta funções do RBAC incorporadas e personalizadas para a autenticação através do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f14c6625a36356a6882e1596db13c1749a9a292a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450038"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Conceder acesso a dados de BLOBs e filas do Azure com o RBAC com o PowerShell

Azure Active Directory (Azure AD) autoriza direitos de acesso a recursos protegidos por meio [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões utilizados para aceder aos contentores ou filas. 

Quando uma função RBAC é atribuída a um principal de segurança do Azure AD, o Azure concede acesso a esses recursos para aquela entidade de segurança. Acesso pode ser confinado ao nível da subscrição, o grupo de recursos, a conta de armazenamento, ou um contentor individual ou fila. Um principal de segurança do Azure AD pode ser um utilizador, um grupo, um principal de serviço de aplicações, ou uma [identidade de recursos do Azure gerida](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como utilizar o Azure PowerShell para listar as funções incorporadas do RBAC e atribuí-las aos utilizadores. Para obter mais informações sobre como utilizar o Azure PowerShell, consulte [descrição geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções do RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Funções RBAC disponíveis de lista

Para listar as funções RBAC incorporadas disponíveis com o Azure PowerShell, utilize o [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) comando:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Verá as funções incorporadas de dados de armazenamento do Azure listadas, juntamente com outras funções incorporadas do Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Atribuir uma função RBAC a um utilizador

Para atribuir uma função RBAC a um utilizador, utilize o [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) comando. O formato do comando pode divergir com base no âmbito da atribuição. Os exemplos seguintes mostram como atribuir uma função a um utilizador em vários âmbitos.

### <a name="container-scope"></a>Âmbito do contentor

Para atribuir uma função no âmbito de um contentor, especifique uma cadeia de caracteres contendo o âmbito do contentor para o `--scope` parâmetro. O âmbito de um contentor está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

O exemplo seguinte atribui o **contribuinte de dados de Blob de armazenamento** função para um utilizador, para um contentor com o nome de âmbito *exemplo-container*. Certifique-se substituir os valores de exemplo e os valores de marcador de posição entre parênteses Retos pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Âmbito da fila

Para atribuir uma função no âmbito de uma fila, especifique uma cadeia de caracteres contendo o âmbito da fila para o `--scope` parâmetro. O âmbito de uma fila está sob a forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

O exemplo seguinte atribui o **contribuinte de dados de fila de armazenamento** função para um utilizador, a uma fila com o nome de âmbito *fila de exemplo*. Certifique-se substituir os valores de exemplo e os valores de marcador de posição entre parênteses Retos pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Âmbito da conta de armazenamento

Para atribuir uma função de um âmbito para a conta de armazenamento, especifique o âmbito do recurso de conta de armazenamento para o `--scope` parâmetro. O âmbito de uma conta de armazenamento está sob a forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A exemplo a seguir mostra como escopo o **leitor de dados de Blob de armazenamento** função para um utilizador ao nível da conta de armazenamento. Certifique-se substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Âmbito do grupo de recursos

Para atribuir uma função no âmbito do grupo de recursos, especifique o nome do grupo de recursos ou o ID para o `--resource-group` parâmetro. O exemplo seguinte atribui o **leitor de dados de fila de armazenamento** função para um utilizador ao nível do grupo de recursos. Certifique-se substituir os valores de exemplo e os valores de marcador de posição entre parênteses Retos pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Âmbito da subscrição

Para atribuir uma função no âmbito da subscrição, especifique o âmbito para a subscrição para o `--scope` parâmetro. O âmbito de uma subscrição está no formato:

```
/subscriptions/<subscription>
```

O exemplo seguinte mostra como atribuir o **leitor de dados de Blob de armazenamento** função para um utilizador ao nível da conta de armazenamento. Certifique-se substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passos Seguintes

- [Gerir o acesso aos recursos do Azure através do RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Conceder acesso a dados de BLOBs e filas do Azure com o RBAC com a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
