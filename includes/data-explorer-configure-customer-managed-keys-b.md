---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297889"
---
## <a name="create-a-new-key-vault"></a>Criar um novo cofre chave

Para criar um novo cofre de chaves usando powerShell, ligue para [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O cofre chave que utiliza para armazenar chaves geridas pelo cliente para encriptação do Azure Data Explorer deve ter duas definições de proteção de teclas ativadas, **Soft Delete** e **Não Purgar**. Substitua os valores do espaço reservado nos suportes por valores próprios, por exemplo abaixo.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre chave

Em seguida, configure a política de acesso para o cofre chave para que o cluster tenha permissões para acessá-lo. Neste passo, utilizará a identidade gerida atribuída pelo sistema que anteriormente atribuiu ao cluster. Para definir a política de acesso para o cofre chave, ligue para [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Substitua os valores do espaço reservado nos suportes com os seus próprios valores e utilize as variáveis definidas nos exemplos anteriores.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre chave. Para criar uma nova tecla, ligue para [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Substitua os valores do espaço reservado nos suportes com os seus próprios valores e utilize as variáveis definidas nos exemplos anteriores.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
