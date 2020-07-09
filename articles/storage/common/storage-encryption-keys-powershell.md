---
title: Utilize o PowerShell para configurar chaves geridas pelo cliente
titleSuffix: Azure Storage
description: Aprenda a usar o PowerShell para configurar chaves geridas pelo cliente para encriptação de armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 6b2983bbaf22ae1b9e09ff3362a4bc06e6658b33
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506208"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configure as chaves geridas pelo cliente com o Cofre de Chaves Azure utilizando o PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Cofre de Chaves Azure com teclas geridas pelo cliente usando o PowerShell. Para aprender a criar um cofre de chaves utilizando o Azure CLI, consulte [Quickstart: set and retrieve a secret from Azure Key Vault using PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para ativar as chaves geridas pelo cliente para a sua conta de armazenamento, atribua primeiro uma identidade gerida atribuída ao sistema para a conta de armazenamento. Usará esta identidade gerida para conceder permissão à conta de armazenamento para aceder ao cofre das chaves.

Para atribuir uma identidade gerida utilizando o PowerShell, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Para obter mais informações sobre a configuração de identidades geridas atribuídas pelo sistema com a PowerShell, consulte [identidades geridas configure para recursos Azure num VM Azure utilizando o PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre-chave

Para criar um novo cofre de chaves utilizando o PowerShell, ligue para [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O cofre-chave que utiliza para armazenar chaves geridas pelo cliente para encriptação de armazenamento Azure deve ter duas definições de proteção de chaves ativadas, **Soft Delete** e **Não Purgar**.

Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Para aprender a ativar a **eliminação suave** e **não purgar** num cofre de teclas existente com o PowerShell, consulte as secções intituladas **Ativando a eliminação suave** e a **proteção** da purga em [como utilizar a eliminação suave com o PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre chave

Em seguida, configurar a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para aceder a ele. Neste passo, utilizará a identidade gerida que atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, ligue para [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre da chave. Para criar uma nova chave, ligue [para Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

A encriptação de armazenamento Azure suporta chaves RSA e RSA-HSM dos tamanhos 2048, 3072 e 4096. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar encriptação com chaves geridas pelo cliente

Por predefinição, a encriptação do Azure Storage utiliza as teclas geridas pela Microsoft. Neste passo, configufique a sua conta de Armazenamento Azure para utilizar as chaves geridas pelo cliente e especifique a chave para associar à conta de armazenamento.

Ligue [para o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento, como mostra o exemplo seguinte. Inclua a opção **-KeyvaultEncryption** para ativar as chaves geridas pelo cliente para a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, terá de atualizar a conta de armazenamento para utilizar a nova versão. Primeiro, ligue para [o Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento para utilizar a nova versão da chave, como mostrado na secção anterior.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação de armazenamento Azure, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) como mostrado na [encriptação Configure com as teclas geridas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão chave. Se a nova chave estiver num cofre diferente, também atualize o cofre uri de porta-chaves.

## <a name="revoke-customer-managed-keys"></a>Revogar as chaves geridas pelo cliente

Se acredita que uma chave pode ter sido comprometida, pode revogar as chaves geridas pelo cliente removendo a política de acesso ao cofre. Para revogar uma chave gerida pelo cliente, ligue para o comando [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) como mostra o exemplo seguinte. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as teclas geridas pelo cliente, a sua conta de armazenamento é novamente encriptada com as teclas geridas pela Microsoft. Para desativar as teclas geridas pelo cliente, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) com a `-StorageEncryption` opção, como mostra o exemplo a seguir. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Próximos passos

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [O que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
