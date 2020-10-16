---
title: Use o PowerShell para criar uma delegação de utilizador SAS para um recipiente ou bolha
titleSuffix: Azure Storage
description: Saiba como criar uma delegação de utilizador SAS com credenciais de Diretório Azure Ative utilizando o PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714532"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Criar uma delegação de utilizador SAS para um recipiente ou bolha com PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar credenciais do Azure Ative Directory (Azure AD) para criar uma delegação de utilizador SAS para um recipiente ou bolha com a Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instale o módulo PowerShell

Para criar uma delegação de utilizador SAS com PowerShell, instale a versão 1.10.0 ou posterior do módulo Az.Storage. Siga estes passos para instalar a versão mais recente do módulo:

1. Desinstalar quaisquer instalações anteriores da Azure PowerShell:

    - Remova quaisquer instalações anteriores do Azure PowerShell do Windows utilizando as **funcionalidades & aplicações** de aplicações em **Definições**.
    - Remova todos os módulos **Azure** de `%Program Files%\WindowsPowerShell\Modules` .

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Certifique-se de que instalou a versão 3.2.0 ou posterior do Azure PowerShell. Executar o seguinte comando para instalar a versão mais recente do módulo Azure Storage PowerShell:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Feche e reabra a janela PowerShell.

Para verificar qual a versão do módulo Az.Storage, execute o seguinte comando:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Para obter mais informações sobre a instalação do Azure PowerShell, consulte [instalar a Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Inscreva-se no Azure PowerShell com Azure AD

Ligue para o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para iniciar sação com a sua conta AZure AD:

```powershell
Connect-AzAccount
```

Para obter mais informações sobre a sessão com a PowerShell, consulte [Iniciar sessão com a Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Atribuir permissões com o Azure RBAC

Para criar uma delegação de utilizador SAS da Azure PowerShell, a conta AZure AD utilizada para assinar no PowerShell deve ser atribuída a uma função que inclua a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Esta permissão permite que a conta Azure AD solicite a chave da *delegação*do utilizador . A chave da delegação de utilizadores é utilizada para assinar a delegação de utilizadores SAS. A função que fornece a ação **Microsoft.Storage/storageAcounts/blobServices/generateUserDelegationKey** deve ser atribuída ao nível da conta de armazenamento, do grupo de recursos ou da subscrição. Para obter mais informações sobre permissões Azure RBAC para a criação de uma delegação de utilizadores SAS, consulte as permissões de Atribuição com secção **Azure RBAC** na [Criação de uma delegação de utilizador SAS](/rest/api/storageservices/create-user-delegation-sas).

Se não tiver permissões suficientes para atribuir funções Azure a um responsável de segurança Azure, poderá ter de pedir ao proprietário ou administrador da conta para atribuir as permissões necessárias.

O exemplo a seguir atribui a função **de Contribuinte de Dados blob de armazenamento,** que inclui a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** A função é traçada ao nível da conta de armazenamento.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções incorporadas que incluem a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** consulte [as funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Use credenciais AD AD para garantir um SAS

Quando cria uma delegação de utilizador SAS com Azure PowerShell, a chave da delegação do utilizador que é usada para assinar o SAS é criada implicitamente para si. O tempo de início e o prazo de validade que especifica para o SAS também são utilizados como o tempo de início e o prazo de validade para a chave da delegação do utilizador. 

Uma vez que o intervalo máximo sobre o qual a chave da delegação do utilizador é válida é de 7 dias a contar da data de início, deve especificar um prazo de validade para o SAS que se encontra no prazo de 7 dias a contar da hora de início. O SAS é inválido após o termo da chave da delegação do utilizador, pelo que um SAS com um prazo de validade superior a 7 dias será válido apenas por 7 dias.

Para criar uma delegação de utilizador SAS para um recipiente ou bolha com Azure PowerShell, primeiro crie um novo objeto de contexto de armazenamento Azure, especificando o `-UseConnectedAccount` parâmetro. O `-UseConnectedAccount` parâmetro especifica que o comando cria o objeto de contexto sob a conta Azure AD com a qual se inscreveu.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Criar uma delegação de utilizador SAS para um recipiente

Para devolver um token SAS de delegação de utilizador para um recipiente, ligue para o comando [New-AzStorageContainerSASASToken,](/powershell/module/az.storage/new-azstoragecontainersastoken) passando no objeto de contexto de armazenamento Azure que criou anteriormente.

O exemplo a seguir devolve um símbolo SAS da delegação de utilizadores para um recipiente. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

A delegação de utilizadores sas token devolvida será semelhante a:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Criar uma delegação de utilizador SAS para uma bolha

Para devolver um símbolo de designação sas para uma bolha, ligue para o comando [New-AzStorageBlobSASToken,](/powershell/module/az.storage/new-azstorageblobsastoken) passando no objeto de contexto de armazenamento Azure que criou anteriormente.

A sintaxe que se segue devolve uma delegação de utilizador SAS para uma bolha. O exemplo especifica o `-FullUri` parâmetro, que devolve o blob URI com o símbolo SAS anexado. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

A delegação de utilizadores SAS URI devolvida será semelhante a:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Uma delegação de utilizadores SAS não suporta a definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma delegação de utilizadores SAS

Para revogar uma delegação de utilizador SAS da Azure PowerShell, ligue para o comando **Revoke-AzStorageAccountUserDelegationKeys.** Este comando revoga todas as chaves da delegação do utilizador associadas à conta de armazenamento especificada. Quaisquer assinaturas de acesso partilhada associadas a essas chaves são invalidadas.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Tanto a chave da delegação do utilizador como as atribuições de funções Azure são armazenadas pela Azure Storage, pelo que pode haver um atraso entre quando inicia o processo de revogação e quando uma delegação de utilizador existente SAS se torna inválida.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Obtenha a operação chave da delegação de utilizadores](/rest/api/storageservices/get-user-delegation-key)
