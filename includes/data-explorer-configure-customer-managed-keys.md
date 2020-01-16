---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021273"
---
O Azure Data Explorer criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar na criptografia de dados. As chaves gerenciadas pelo cliente devem ser armazenadas em um [Azure Key Vault](/azure/key-vault/key-vault-overview). Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar uma API de Azure Key Vault para gerar chaves. O cluster Data Explorer do Azure e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter uma explicação detalhada sobre chaves gerenciadas pelo cliente, consulte [chaves gerenciadas pelo cliente com Azure Key Vault](/azure/storage/common/storage-service-encryption). Este artigo mostra como configurar chaves gerenciadas pelo cliente.

> [!Note]
> Para configurar chaves gerenciadas pelo cliente com o Azure Data Explorer, você deve [definir duas propriedades no cofre de chaves](/azure/key-vault/key-vault-ovw-soft-delete): **exclusão reversível** e **não limpar**. Essas propriedades não são habilitadas por padrão. Para habilitar essas propriedades, use o [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) ou o [CLI do Azure](/azure/key-vault/key-vault-soft-delete-cli). Somente chaves RSA e tamanho de chave 2048 têm suporte.

## <a name="assign-an-identity-to-the-cluster"></a>Atribuir uma identidade ao cluster

Para habilitar chaves gerenciadas pelo cliente para o cluster, primeiro atribua uma identidade gerenciada atribuída pelo sistema ao cluster. Você usará essa identidade gerenciada para conceder as permissões de cluster para acessar o cofre de chaves. Para configurar identidades gerenciadas atribuídas pelo sistema, consulte [identidades gerenciadas](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

Para criar um novo cofre de chaves usando o PowerShell, chame [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O cofre de chaves que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de Data Explorer do Azure deve ter duas configurações de proteção de chave habilitadas, **exclusão reversível** e **não limpar**. Substitua os valores de espaço reservado entre colchetes com seus próprios valores no exemplo abaixo.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do cofre de chaves

Em seguida, configure a política de acesso para o cofre de chaves para que o cluster tenha permissões para acessá-lo. Nesta etapa, você usará a identidade gerenciada atribuída pelo sistema que você atribuiu anteriormente ao cluster. Para definir a política de acesso para o cofre de chaves, chame [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Substitua os valores de espaço reservado entre colchetes por seus próprios valores e use as variáveis definidas nos exemplos anteriores.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma chave nova

Em seguida, crie uma nova chave no cofre de chaves. Para criar uma nova chave, chame [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Substitua os valores de espaço reservado entre colchetes por seus próprios valores e use as variáveis definidas nos exemplos anteriores.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
