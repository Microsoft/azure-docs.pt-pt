---
title: Alterar a ID de locatário do cofre de chaves após uma Azure Key Vault de uma assinatura | Microsoft Docs
description: Saiba como mudar o ID do inquilino para um cofre de chaves depois de mover uma subscrição para um inquilino diferente
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 3819742e82fe6877b6a1aa58e52eec01b6b05515
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001254"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Alterar um ID do inquilino do cofre de chaves após a movimentação de uma subscrição

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Ao criar um novo cofre de chaves numa subscrição, é automaticamente associado ao ID de inquilino do Azure Active Directory predefinido nessa subscrição. Todas as entradas de política de acesso também estão associadas a este ID de inquilino. 

Se você mover sua assinatura do Azure do locatário A para o locatário B, seus cofres de chaves existentes ficarão inacessíveis pelas entidades de segurança (usuários e aplicativos) no locatário B. Para corrigir esse problema, você precisa:

* Altere a ID de locatário associada a todos os cofres de chaves existentes na assinatura para o locatário B.
* Remover todas as entradas de política de acesso existentes.
* Adicione novas entradas de política de acesso associadas ao locatário B.

Por exemplo, se você tiver o cofre de chaves ' myvault ' em uma assinatura que foi movida do locatário A para o locatário B, você poderá usar Azure PowerShell para alterar a ID do locatário e remover as políticas de acesso antigas.

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

Ou você pode usar o CLI do Azure.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Agora que o cofre está associado à ID de locatário correta e as entradas de política de acesso antigas foram removidas, defina novas entradas de política de acesso com o cmdlet Azure PowerShell [set-AzKeyVaultAccessPolicy](https://powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) ou o comando CLI do Azure [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

Se você estiver usando uma identidade gerenciada para recursos do Azure, será necessário atualizá-la para o novo locatário do Azure AD também. Para obter mais informações sobre identidades gerenciadas, consulte [fornecer autenticação de Key Vault com uma identidade gerenciada](managed-identity.md).


Se você estiver usando o MSI, também precisará atualizar a identidade do MSI, já que a identidade antiga não estará mais no locatário do AAD correto.

## <a name="next-steps"></a>Passos seguintes

Se tiver perguntas sobre o Cofre de Chaves do Azure, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
