---
title: Utilize a PowerShell para criar uma delegação de utilizadores SAS para um recipiente ou bolha
titleSuffix: Azure Storage
description: Saiba como criar uma delegação de utilizadores SAS com credenciais de Diretório Ativo Azure utilizando o PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79536178"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Criar uma delegação de utilizadores SAS para um recipiente ou bolha com powerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar credenciais de Diretório Ativo Azure (Azure AD) para criar uma delegação de utilizadores SAS para um recipiente ou bolha com a Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instale o módulo PowerShell

Para criar uma delegação de utilizadores SAS com PowerShell, instale a versão 1.10.0 ou mais tarde do módulo Az.Storage. Siga estes passos para instalar a versão mais recente do módulo:

1. Desinstale quaisquer instalações anteriores da Azure PowerShell:

    - Remova quaisquer instalações anteriores do Azure PowerShell do Windows utilizando as **aplicações & definição** de definições em **Definições**.
    - Retire todos os módulos **Azure** de `%Program Files%\WindowsPowerShell\Modules`.

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Certifique-se de que instalou a versão 3.2.0 do Azure PowerShell ou mais tarde. Executar o seguinte comando para instalar a versão mais recente do módulo PowerShell de armazenamento Azure:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Feche e reabra a janela PowerShell.

Para verificar qual a versão do módulo Az.Storage, execute o seguinte comando:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Para mais informações sobre a instalação do Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Inscreva-se na Azure PowerShell com a Azure AD

Ligue para o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para iniciar sessão com a sua conta AD Azure:

```powershell
Connect-AzAccount
```

Para mais informações sobre a contratação com a PowerShell, consulte O Signin com a [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Atribuir permissões com RBAC

Para criar uma delegação de utilizadores SAS a partir do Azure PowerShell, a conta Azure AD utilizada para iniciar sessão no PowerShell deve ser atribuída uma função que inclua a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Esta permissão permite que a conta Azure AD solicite a chave da *delegação*do utilizador . A chave da delegação de utilizadores é utilizada para assinar a delegação de utilizadores SAS. A função que fornece a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationAacçãoA** ação deve ser atribuída ao nível da conta de armazenamento, do grupo de recursos ou da subscrição. Para obter mais informações sobre as permissões RBAC para a criação de uma delegação de utilizadores SAS, consulte as **permissões** de atribuição com a secção RBAC em [Criar uma delegação de utilizadores SAS](/rest/api/storageservices/create-user-delegation-sas).

Se não tiver permissões suficientes para atribuir funções RBAC a um diretor de segurança da AD Azure, poderá ter de pedir ao proprietário ou administrador da conta que atribua as permissões necessárias.

O exemplo seguinte atribui a função de Contribuinte de **Dados do Blob de Armazenamento,** que inclui a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** O papel é traçado ao nível da conta de armazenamento.

Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções incorporadas que incluem a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** action, consulte [as funções incorporadas para os recursos Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Use credenciais Azure AD para garantir um SAS

Quando cria uma delegação de utilizadores SAS com a Azure PowerShell, a chave de delegação de utilizadores que é usada para assinar o SAS é criada para si implicitamente. O tempo de início e o tempo de validade que especifica para o SAS também são utilizados como hora de início e tempo de validade para a chave da delegação do utilizador. 

Uma vez que o intervalo máximo sobre o qual a chave da delegação do utilizador é válido é de 7 dias a contar da data de início, deve especificar um prazo de validade para o SAS que se encontra no prazo de 7 dias após a hora de início. O SAS é inválido após o termo da chave da delegação do utilizador, pelo que um SAS com um prazo de validade superior a 7 dias ainda será válido apenas por 7 dias.

Para criar uma delegação de utilizadores SAS para um recipiente ou bolha com a Azure `-UseConnectedAccount` PowerShell, crie primeiro um novo objeto de contexto de armazenamento Azure, especificando o parâmetro. O `-UseConnectedAccount` parâmetro especifica que o comando cria o objeto de contexto sob a conta Azure AD com a qual inscreveu.

Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Criar uma delegação de utilizadores SAS para um recipiente

Para devolver uma delegação de utilizadores, token SAS para um recipiente, ligue para o comando [New-AzStorageContainerSASToken,](/powershell/module/az.storage/new-azstoragecontainersastoken) passando no objeto de contexto de armazenamento Azure que criou anteriormente.

O exemplo seguinte devolve um símbolo da delegação de utilizadores SAS para um recipiente. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

A delegação de utilizadores SAS token devolvido será semelhante a:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Criar uma delegação de utilizadores SAS para uma bolha

Para devolver uma delegação de utilizadores, token SAS para uma bolha, ligue para o comando [New-AzStorageBlobSASToken,](/powershell/module/az.storage/new-azstorageblobsastoken) passando no objeto de contexto de Armazenamento Azure que criou anteriormente.

A seguinte sintaxe devolve uma delegação de utilizadores SAS para uma bolha. O exemplo especifica `-FullUri` o parâmetro, que devolve o blob URI com o token SAS anexado. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

A delegação de utilizadores que a SAS URI devolveu será semelhante a:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Uma delegação de utilizadores SAS não suporta a definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma delegação de utilizadores SAS

Para revogar uma delegação de utilizadores SAS da Azure PowerShell, ligue para o comando **Revoke-AzStorageAccountUserUserKeys.** Este comando revoga todas as chaves da delegação do utilizador associadas à conta de armazenamento especificada. Quaisquer assinaturas de acesso partilhadaassociadas a essas chaves são invalidadas.

Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Tanto a chave da delegação do utilizador como as atribuições de funções RBAC são em cache pelo Armazenamento Azure, pelo que pode haver um atraso entre quando iniciar o processo de revogação e quando uma delegação de utilizadores existente SAS se tornar inválida.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Obtenha a operação chave da delegação do utilizador](/rest/api/storageservices/get-user-delegation-key)
