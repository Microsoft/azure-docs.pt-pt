---
title: Criar uma SAS de delegação de usuário para um contêiner ou BLOB com o PowerShell (versão prévia) – armazenamento do Azure
description: Saiba como criar uma assinatura de acesso compartilhado (SAS) usando credenciais de Azure Active Directory no armazenamento do Azure usando o PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 8a455fdb8ef81b0e06d1f77f7a9cdd5bec351b2b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164273"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>Criar uma SAS de delegação de usuário para um contêiner ou BLOB com o PowerShell (versão prévia)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (AD do Azure) para criar uma SAS de delegação de usuário para um contêiner ou BLOB com Azure PowerShell (versão prévia).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>Instalar o módulo de visualização

Para usar o PowerShell para criar uma SAS de delegação de usuário, você deve primeiro instalar o módulo AZ. Storage 1.3.1-Preview. Siga estas etapas para instalar o módulo:

1. Desinstale todas as instalações anteriores do Azure PowerShell:

    - Remova todas as instalações anteriores do Azure PowerShell do Windows usando a configuração **aplicativos & recursos** em **configurações**.
    - Remova todos os módulos do `%Program Files%\WindowsPowerShell\Modules`Azure de.

1. Verifique se você tem a versão mais recente do PowerShellGet instalada. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instalar um módulo de visualização do armazenamento do Azure que dá suporte à SAS de delegação de usuário:

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. Feche e reabra a janela do PowerShell.

Como o PowerShell carrega o módulo AZ. Storage mais recente por padrão, talvez seja necessário carregar explicitamente o módulo 1.3.1-Preview ao iniciar o console. Para carregar explicitamente o módulo de visualização, execute o comando [Import-Module](/powershell/module/microsoft.powershell.core/import-module) :

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Para obter mais informações sobre como instalar Azure PowerShell, consulte [instalar Azure PowerShell com PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Entrar no Azure PowerShell com o Azure AD

Chame o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para entrar com sua conta do Azure AD:

```powershell
Connect-AzAccount
```

Para obter mais informações sobre como entrar com o PowerShell, consulte [entrar com Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Atribuir permissões com o RBAC

Para criar uma SAS de delegação de usuário do Azure PowerShell, a conta do Azure AD usada para entrar no PowerShell deve ser atribuída a uma função que inclui a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . Essa permissão permite que a conta do Azure AD solicite a *chave de delegação do usuário*. A chave de delegação de usuário é usada para assinar a SAS de delegação de usuário. A função que fornece a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** deve ser atribuída no nível da conta de armazenamento, no grupo de recursos ou na assinatura. Para obter mais informações sobre permissões RBAC para criar uma SAS de delegação de usuário, consulte a seção **atribuir permissões com RBAC** em [criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas).

Se você não tiver permissões suficientes para atribuir funções de RBAC a uma entidade de segurança do Azure AD, talvez seja necessário solicitar ao proprietário da conta ou ao administrador que atribua as permissões necessárias.

O exemplo a seguir atribui a função de **colaborador de dados de blob de armazenamento** , que inclui a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . A função tem o escopo no nível da conta de armazenamento.

Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções internas que incluem a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** , consulte [funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usar as credenciais do Azure AD para proteger uma SAS

Quando você cria uma SAS de delegação de usuário com Azure PowerShell, a chave de delegação de usuário usada para assinar a SAS é criada implicitamente. A hora de início e a hora de expiração que você especifica para a SAS também são usadas como a hora de início e a hora de expiração da chave de delegação do usuário. 

Como o intervalo máximo em que a chave de delegação de usuário é válida é de 7 dias a partir da data de início, você deve especificar um tempo de expiração para a SAS que está dentro de 7 dias da hora de início. A SAS é inválida depois que a chave de delegação do usuário expira, portanto, uma SAS com um tempo de expiração superior a 7 dias ainda será válida por sete dias.

Para criar uma SAS de delegação de usuário para um contêiner ou BLOB com Azure PowerShell, primeiro crie um novo objeto de contexto de armazenamento `-UseConnectedAccount` do Azure, especificando o parâmetro. O `-UseConnectedAccount` parâmetro especifica que o comando cria o objeto de contexto na conta do Azure AD com a qual você se conectou.

Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Criar uma SAS de delegação de usuário para um contêiner

Para retornar um token SAS de delegação de usuário para um contêiner, chame o comando [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) , passando o objeto de contexto de armazenamento do Azure que você criou anteriormente.

O exemplo a seguir retorna um token SAS de delegação de usuário para um contêiner. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

O token SAS de delegação de usuário retornado será semelhante a:

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Criar uma SAS de delegação de usuário para um blob

Para retornar um token SAS de delegação de usuário para um blob, chame o comando [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) , passando o objeto de contexto de armazenamento do Azure que você criou anteriormente.

A sintaxe a seguir retorna uma SAS de delegação de usuário para um blob. O exemplo especifica o `-FullUri` parâmetro, que retorna o URI do blob com o token SAS anexado. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

O URI de SAS de delegação de usuário retornado será semelhante a:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Uma SAS de delegação de usuário não dá suporte à definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma SAS de delegação de usuário

Para revogar uma SAS de delegação de usuário do Azure PowerShell, chame o comando **REVOKE-AzStorageAccountUserDelegationKeys** . Esse comando revoga todas as chaves de delegação de usuário associadas à conta de armazenamento especificada. Todas as assinaturas de acesso compartilhado associadas a essas chaves são invalidadas.

Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Tanto a chave de delegação do usuário quanto as atribuições de função do RBAC são armazenadas em cache pelo armazenamento do Azure, portanto, pode haver um atraso entre quando você inicia o processo de revogação e quando uma SAS de delegação de usuário existente se torna inválida.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Operação de obtenção de chave de delegação de usuário](/rest/api/storageservices/get-user-delegation-key)
