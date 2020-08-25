---
title: Azure Key Vault movendo um cofre para uma subscrição diferente Microsoft Docs
description: Orientação sobre a mudança de um cofre chave para uma subscrição diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: b37b327a535b716bbce845cd5883e58ec5379c48
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782724"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Mover um Cofre chave Azure para outra subscrição

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> **Mover um cofre chave para outra subscrição irá causar uma mudança de rutura no seu ambiente.**
> Certifique-se de que compreende o impacto desta mudança e siga cuidadosamente as orientações deste artigo antes de decidir mover o cofre-chave para uma nova subscrição.
> Se estiver a utilizar identidades de serviço gerido (MSI), leia as instruções pós-movimento no final do documento. 

Quando cria um cofre chave, está automaticamente ligado ao ID do inquilino Azure Ative Directory predefinido para a subscrição em que é criado. Todas as entradas de política de acesso também estão associadas a este ID de inquilino. Se mudar a sua assinatura Azure do inquilino A para o inquilino B, os seus cofres-chave existentes serão inacessíveis pelos principais serviços (utilizadores e aplicações) no inquilino B. Para resolver este problema, tem de:

* Mude a identificação do inquilino associada a todos os cofres chave existentes na subscrição do inquilino B.
* Remover todas as entradas de política de acesso existentes.
* Adicione novas entradas de política de acesso associadas ao inquilino B.

## <a name="limitations"></a>Limitações

Alguns diretores de serviço (utilizadores e aplicações) estão ligados a um inquilino específico. Se você mudar o seu cofre chave para uma subscrição em outro inquilino, há a chance de que você não será capaz de restaurar o acesso a um diretor de serviço específico. Verifique se todos os diretores de serviço essenciais existem no inquilino para onde você está movendo o seu cofre chave.

## <a name="design-considerations"></a>Considerações de conceção

A sua organização pode ter implementado a Política Azure com aplicação ou exclusões ao nível da subscrição. Pode haver um conjunto diferente de atribuições de política na subscrição onde o seu cofre-chave existe atualmente e a subscrição para onde está a mover o cofre principal. Um conflito nos requisitos políticos tem o potencial de quebrar as suas aplicações.

### <a name="example"></a>Exemplo

Tem uma aplicação ligada ao cofre-chave que cria certificados válidos por dois anos. A subscrição onde está a tentar mover o cofre tem uma atribuição de política que bloqueia a criação de certificados válidos por mais de um ano. Depois de mover o seu cofre chave para a nova subscrição, a operação para criar um certificado válido por dois anos será bloqueada por uma atribuição de política Azure.

### <a name="solution"></a>Solução

Certifique-se de que vai à página da Política Azure no portal Azure e veja as atribuições políticas para a sua subscrição atual, bem como a subscrição para a qual está a mover-se e certifique-se de que não existem desencontros.

## <a name="prerequisites"></a>Pré-requisitos

* Acesso ao nível do contribuinte ou superior à subscrição atual onde existe o cofre-chave.
* Acesso ao nível do contribuinte ou superior à subscrição onde pretende mover o cofre da chave.
* Um grupo de recursos na nova subscrição.

## <a name="procedure"></a>Procedimento

Se 

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>Mover o Cofre-Chave para uma nova subscrição dentro do mesmo inquilino

1. Iniciar sessão no portal do Azure
2. Navegue até ao cofre da chave
3. Clique no separador "Visão Geral"
4. Selecione o botão "Mover"
5. Selecione "Mover para outra subscrição" a partir das opções de dropdown
6. Selecione o grupo de recursos onde pretende mover o cofre de chaves
7. Reconheça o aviso sobre os recursos em movimento
8. Selecione "OK"

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>Passos adicionais se você mudou o cofre chave para uma subscrição em um novo inquilino

Se você mudou o seu cofre chave para uma subscrição em um novo inquilino, você precisa atualizar manualmente o ID do inquilino e remover as antigas políticas de acesso. Aqui estão os tutoriais para estes passos em PowerShell e Azure CLI. Se estiver a utilizar o PowerShell, poderá ter de executar o comando Clear-AzContext documentado abaixo para lhe permitir ver recursos fora do âmbito atual selecionado. 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Agora que o seu cofre está associado com o ID do inquilino correto e as entradas antigas da política de acesso são removidas, deslote novas entradas de política de acesso com o cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) ou o comando [de política de definição de keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Azure CLI az.

Se você está usando uma identidade gerida para recursos Azure, você precisará atualizá-lo para o novo inquilino AZURE AD também. Para obter mais informações sobre identidades geridas, consulte [fornecer autenticação key vault com uma identidade gerida](managed-identity.md).

Se você estiver usando MSI, você também terá que atualizar a identidade MSI, uma vez que a identidade antiga não estará mais no inquilino correto da AAD. Consulte os seguintes documentos para ajudar a resolver esta questão. 

* [Atualizar o MSI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)
* [Subscrição de transferência para novo diretório](https://docs.microsoft.com/azure/role-based-access-control/transfer-subscription)


