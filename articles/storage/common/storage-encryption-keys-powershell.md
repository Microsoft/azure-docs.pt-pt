---
title: Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure a partir do PowerShell
description: Saiba como utilizar o PowerShell para configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure. Chaves geridas pelo cliente permitem-lhe criar, girar, desativar e revogar os controlos de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: be876b370cd476bee2af7d90a9f0433fd80de3b4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233691"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure a partir do PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves geridas pelo cliente através do PowerShell.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade para a conta de armazenamento

Para ativar as chaves geridas pelo cliente para a sua conta de armazenamento, primeiro de atribuir uma identidade gerida sistema atribuído à conta de armazenamento. Irá utilizar esta identidade gerida para conceder as permissões de conta de armazenamento para aceder ao Cofre de chaves.

Para atribuir uma identidade gerida com o PowerShell, chame [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Para obter mais informações sobre como configurar atribuído de sistema de identidades geridas com o PowerShell, consulte [configurar geridos identidades para recursos do Azure na VM do Azure com o PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

Para criar um novo cofre de chaves com o PowerShell, chame [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O Cofre de chaves que utiliza para armazenar as chaves geridas pelo cliente para encriptação de armazenamento do Azure tem de ter duas definições de proteção por chave ativadas, **eliminação de forma recuperável** e **fazer não remover**. 

Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do Cofre de chaves

Em seguida, configure a política de acesso para o Cofre de chaves para que a conta de armazenamento tem permissões para aceder ao mesmo. Neste passo, vai utilizar a identidade gerida que tiver atribuído à conta de armazenamento.

Para definir a política de acesso do Cofre de chaves, chame [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Lembre-se de que substitui os valores de marcador de posição entre parênteses Retos pelos seus próprios valores e como utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma chave nova

Em seguida, crie uma nova chave no Cofre de chaves. Para criar uma nova chave, chame [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de que substitui os valores de marcador de posição entre parênteses Retos pelos seus próprios valores e como utilizar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a encriptação com chaves geridas pelo cliente

Por predefinição, a encriptação de armazenamento do Azure utiliza as chaves geridas pela Microsoft. Neste passo, configure a conta de armazenamento do Azure para utilizar chaves geridas pelo cliente e especifique a chave para associar a conta de armazenamento.

Chamar [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) ao atualizar as definições de encriptação da conta de armazenamento. Lembre-se de que substitui os valores de marcador de posição entre parênteses Retos pelos seus próprios valores e como utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Atualizar a versão da chave

Quando cria uma nova versão de uma chave, terá de atualizar a conta de armazenamento a utilizar a nova versão. Em primeiro lugar, chamar [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, chame [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento para utilizar a versão nova da chave, como mostrado na secção anterior.

## <a name="next-steps"></a>Passos Seguintes

- [Encriptação de armazenamento do Azure para dados Inativos](storage-service-encryption.md) 
- [O que é o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
