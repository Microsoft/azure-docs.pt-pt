---
title: Mude o ID do inquilino do cofre chave após um movimento de subscrição - Azure Key Vault / Microsoft Docs
description: Saiba como mudar o ID do inquilino para um cofre de chaves depois de mover uma subscrição para um inquilino diferente
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 9e35f5c9288860056a910f54f9601b2178a628bb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828088"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Alterar um ID do inquilino do cofre de chaves após a movimentação de uma subscrição

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


Ao criar um novo cofre de chaves numa subscrição, é automaticamente associado ao ID de inquilino do Azure Active Directory predefinido nessa subscrição. Todas as entradas de política de acesso também estão associadas a este ID de inquilino. 

Se transferir a sua assinatura Azure do inquilino A para o inquilino B, os seus cofres-chave existentes são inacessíveis pelos principais (utilizadores e aplicações) no inquilino B. Para resolver este problema, é preciso:

* Mude a identificação do inquilino associada a todos os cofres-chave existentes na subscrição do inquilino B.
* Remover todas as entradas de política de acesso existentes.
* Adicione novas entradas de política de acesso associadas ao inquilino B.

Por exemplo, se você tiver o cofre chave 'myvault' em uma subscrição que foi transferida do inquilino A para o inquilino B, você pode usar Azure PowerShell para alterar o ID do inquilino e remover políticas de acesso antigas.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Ou pode usar o Azure CLI.

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Agora que o seu cofre está associado ao ID correto do inquilino e as entradas antigas da política de acesso são removidas, detete novas entradas de política de acesso com o Cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) ou o comando de definição de teclado Azure CLI [az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

Se estiver a usar uma identidade gerida para os recursos do Azure, também terá de a atualizar para o novo inquilino da AD Azure. Para obter mais informações sobre identidades geridas, consulte [A autenticação do Cofre-Chave com uma identidade gerida](managed-identity.md).


Se estiver a usar o MSI, também terá de atualizar a identidade MSI, uma vez que a identidade antiga deixará de estar no inquilino aD correto.

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas sobre o Cofre de Chaves Azure, visite o Microsoft Q&Uma página de [perguntas para o Cofre de Chaves Azure](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
