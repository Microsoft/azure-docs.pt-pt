---
title: Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure do PowerShell
description: Saiba como usar o PowerShell para configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 30fed5a75e368e9d87da54d4c75e6f69759813e7
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249276"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure do PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o PowerShell.

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure requer que duas propriedades sejam definidas no cofre de chaves, **exclusão reversível** e **não sejam limpas**. Essas propriedades não são habilitadas por padrão. Para habilitar essas propriedades, use o PowerShell ou o CLI do Azure.
> Somente chaves RSA e tamanho de chave 2048 têm suporte.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para habilitar chaves gerenciadas pelo cliente para sua conta de armazenamento, primeiro atribua uma identidade gerenciada atribuída pelo sistema à conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões de conta de armazenamento para acessar o cofre de chaves.

Para atribuir uma identidade gerenciada usando o PowerShell, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Para obter mais informações sobre como configurar identidades gerenciadas atribuídas pelo sistema com o PowerShell, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

Para criar um novo cofre de chaves usando o PowerShell, chame [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O cofre de chaves que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure deve ter duas configurações de proteção de chave habilitadas, **exclusão reversível** e **não limpar**. 

Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do cofre de chaves

Em seguida, configure a política de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Nesta etapa, você usará a identidade gerenciada que você atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, chame [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre de chaves. Para criar uma nova chave, chame [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Por padrão, a criptografia de armazenamento do Azure usa chaves gerenciadas pela Microsoft. Nesta etapa, configure sua conta de armazenamento do Azure para usar chaves gerenciadas pelo cliente e especifique a chave a ser associada à conta de armazenamento.

Chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao criar uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Primeiro, chame [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, conforme mostrado na seção anterior.

## <a name="next-steps"></a>Passos seguintes

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md) 
- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
