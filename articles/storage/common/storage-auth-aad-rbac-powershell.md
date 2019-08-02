---
title: Use Azure PowerShell para gerenciar os direitos de acesso do Azure AD aos dados de BLOB e de fila com o armazenamento do Azure RBAC
description: Use Azure PowerShell para atribuir acesso a contêineres e filas com RBAC (controle de acesso baseado em função). O armazenamento do Azure dá suporte a funções RBAC personalizadas e internas para autenticação por meio do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f34e82a0011260ace4ffeed095903b033529a58d
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514984"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Conceder acesso ao blob do Azure e a dados da fila com o RBAC usando o PowerShell

O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio [do RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC internas que abrangem conjuntos comuns de permissões usadas para acessar contêineres ou filas. 

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível da assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar Azure PowerShell para listar funções RBAC internas e atribuí-las aos usuários. Para obter mais informações sobre como usar Azure PowerShell, consulte [visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções de RBAC para BLOBs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Listar funções RBAC disponíveis

Para listar as funções RBAC internas disponíveis com Azure PowerShell, use o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Você verá as funções de dados de armazenamento do Azure internas listadas, junto com outras funções internas do Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Atribuir uma função de RBAC a uma entidade de segurança

Para atribuir uma função de RBAC a uma entidade de segurança, use o comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . O formato do comando pode diferir com base no escopo da atribuição. Os exemplos a seguir mostram como atribuir uma função a um usuário em vários escopos, mas você pode usar o mesmo comando para atribuir uma função a qualquer entidade de segurança.

### <a name="container-scope"></a>Escopo do contêiner

Para atribuir uma função com escopo a um contêiner, especifique uma cadeia de caracteres que contenha o escopo do `--scope` contêiner para o parâmetro. O escopo de um contêiner está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

O exemplo a seguir atribui a função de **colaborador de dados de blob de armazenamento** a um usuário, cujo escopo é um contêiner chamado *de contêiner de exemplo*. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Escopo da fila

Para atribuir uma função com escopo a uma fila, especifique uma cadeia de caracteres que contenha o escopo da `--scope` fila para o parâmetro. O escopo de uma fila está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

O exemplo a seguir atribui a função de **colaborador de dados da fila de armazenamento** a um usuário, com escopo para uma fila chamada *Sample-Queue*. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Escopo da conta de armazenamento

Para atribuir uma função com escopo à conta de armazenamento, especifique o escopo do recurso de conta de armazenamento para `--scope` o parâmetro. O escopo de uma conta de armazenamento está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O exemplo a seguir mostra como fazer o escopo da função de **leitor de dados de blob de armazenamento** para um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Escopo do grupo de recursos

Para atribuir uma função com escopo ao grupo de recursos, especifique o nome ou a ID do grupo de `--resource-group` recursos para o parâmetro. O exemplo a seguir atribui a função de **leitor de dados de fila de armazenamento** a um usuário no nível do grupo de recursos. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes por seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Escopo da assinatura

Para atribuir uma função com escopo à assinatura, especifique o escopo da assinatura para o `--scope` parâmetro. O escopo de uma assinatura está no formato:

```
/subscriptions/<subscription>
```

O exemplo a seguir mostra como atribuir a função de **leitor de dados de blob de armazenamento** a um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passos Seguintes

- [Gerenciar o acesso aos recursos do Azure usando RBAC e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Grant access to Azure blob and queue data with RBAC using Azure CLI](storage-auth-aad-rbac-cli.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através da CLI do Azure)
- [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC no portal do Azure)
