---
title: Utilize o PowerShell para configurar as chaves geridas pelo cliente
titleSuffix: Azure Storage
description: Aprenda a usar o PowerShell para configurar as chaves geridas pelo cliente para encriptação de Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 264dbbaedca5a28c8741d699a683b3e2b2385383
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061145"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configure as chaves geridas pelo cliente com o Cofre de Chaves Azure utilizando a PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Cofre de Chave Azure com chaves geridas pelo cliente usando o PowerShell. Para aprender a criar um cofre chave usando o Azure CLI, consulte [Quickstart: set and retrieve a secret from Azure Key Vault usando powerShell](../../key-vault/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para ativar as chaves geridas pelo cliente para a sua conta de armazenamento, primeiro atribua uma identidade gerida atribuída pelo sistema à conta de armazenamento. Vaiusar esta identidade gerida para conceder permissões à conta de armazenamento para aceder ao cofre chave.

Para atribuir uma identidade gerida utilizando powerShell, ligue para [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Para obter mais informações sobre a configuração de identidades geridas atribuídas pelo sistema com a PowerShell, consulte [as identidades geridas pela Configure para os recursos Azure num Azure VM utilizando powerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre chave

Para criar um novo cofre de chaves usando powerShell, ligue para [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O cofre chave que utiliza para armazenar chaves geridas pelo cliente para encriptação de armazenamento azure deve ter duas definições de proteção chave ativadas, **Soft Delete** e **Não Purgar**.

Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Para aprender a ativar o **Soft Delete** e **não purgar** num cofre de chaves existente com a PowerShell, consulte as secções intituladas **Enableing soft-delete** e **Enableing Purge Protection** in How to use [soft-delete with PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre chave

Em seguida, configure a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para acessá-lo. Neste passo, utilizará a identidade gerida que anteriormente atribuiu à conta de armazenamento.

Para definir a política de acesso para o cofre chave, ligue para [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre chave. Para criar uma nova tecla, ligue para [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configure encriptação com chaves geridas pelo cliente

Por padrão, a encriptação do Armazenamento Azure utiliza chaves geridas pela Microsoft. Neste passo, configure a sua conta de Armazenamento Azure para utilizar as chaves geridas pelo cliente e especificar a chave para associar à conta de armazenamento.

Ligue para o [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento, como mostra o exemplo seguinte. Inclua a opção **-KeyvaultEncryption** para ativar as chaves geridas pelo cliente para a conta de armazenamento. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, terá de atualizar a conta de armazenamento para utilizar a nova versão. Primeiro, ligue para [get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, ligue para [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento para utilizar a nova versão da chave, como mostrado na secção anterior.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação de Armazenamento Azure, ligue para [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) como mostrado na [encriptação Configure com chaves geridas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão chave. Se a nova chave estiver num cofre diferente, também atualize o cofre de chaves URI.

## <a name="revoke-customer-managed-keys"></a>Revogar as chaves geridas pelo cliente

Se acredita que uma chave pode ter sido comprometida, pode revogar as chaves geridas pelo cliente removendo a política de acesso ao cofre chave. Para revogar uma chave gerida pelo cliente, ligue para o comando [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) como mostra o seguinte exemplo. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as chaves geridas pelo cliente, a sua conta de armazenamento é novamente encriptada com chaves geridas pela Microsoft. Para desativar as chaves geridas pelo cliente, `-StorageEncryption` ligue para [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) com a opção, como mostra o seguinte exemplo. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Passos seguintes

- [Encriptação azure storage para dados em repouso](storage-service-encryption.md)
- [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
