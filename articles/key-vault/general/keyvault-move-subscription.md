---
title: Cofre chave Azure movendo um cofre para uma subscrição diferente Microsoft Docs
description: Orientação sobre mover um cofre chave para uma subscrição diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 450aa58d4ad9cbb721e621ec3db8b4ca7e914aa1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121189"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Mover um Cofre chave Azure para outra subscrição

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

**Mover um cofre chave para outra subscrição causará uma mudança de rutura no seu ambiente.**

Certifique-se de que compreende cuidadosamente o impacto desta mudança e siga a orientação deste artigo antes de decidir mover o cofre chave para uma nova subscrição.

Quando cria um cofre chave, está automaticamente ligado ao ID de inquilino do Azure Ative Diretório padrão para a subscrição em que é criado. Todas as entradas de política de acesso também estão associadas a este ID de inquilino. Se transferir a sua assinatura Azure do inquilino A para o inquilino B, os seus cofres-chave existentes serão inacessíveis pelos diretores de serviço (utilizadores e aplicações) no inquilino B. Para resolver este problema, é preciso:

* Mude a identificação do inquilino associada a todos os cofres-chave existentes na subscrição do inquilino B.
* Remover todas as entradas de política de acesso existentes.
* Adicione novas entradas de política de acesso associadas ao inquilino B.

## <a name="limitations"></a>Limitações

Alguns diretores de serviço (utilizadores e aplicações) estão ligados a um inquilino específico. Se você mudar o seu cofre chave para uma subscrição em outro inquilino, há a possibilidade de você não ser capaz de restaurar o acesso a um diretor de serviço específico. Verifique se todos os diretores de serviço essenciais existem no inquilino para onde está a mover o seu cofre chave.

## <a name="design-considerations"></a>Considerações de conceção

A sua organização pode ter implementado a Política Azure com aplicação ou exclusões ao nível da subscrição. Pode haver um conjunto diferente de atribuições políticas na subscrição onde o seu cofre chave existe atualmente e a subscrição onde você está movendo o seu cofre chave. Um conflito nos requisitos políticos tem o potencial de quebrar as suas aplicações.

### <a name="example"></a>Exemplo

Tem uma aplicação ligada ao cofre chave que cria certificados válidos por dois anos. A subscrição onde está a tentar mover o seu cofre chave tem uma atribuição de política que bloqueia a criação de certificados válidos por mais de um ano. Depois de mover o seu cofre chave para a nova subscrição, a operação para criar um certificado válido por dois anos será bloqueada por uma atribuição de política do Azure.

### <a name="solution"></a>Solução

Certifique-se de que vai à página de Política Azure no portal Azure e analise as atribuições políticas para a sua subscrição atual, bem como a subscrição para a qual está a deslocar-se e certifique-se de que não existem desfasamentos.

## <a name="prerequisites"></a>Pré-requisitos

* Acesso ao nível do contribuinte ou superior à subscrição atual onde o seu cofre chave existe.
* Acesso ao nível do colaborador ou superior à subscrição onde pretende mover o seu cofre chave.
* Um grupo de recursos na nova subscrição.

## <a name="procedure"></a>Procedimento

### <a name="initial-steps-moving-key-vault"></a>Etapas iniciais (cofre chave em movimento)

1. Iniciar sessão no portal do Azure
2. Navegue para o seu cofre chave
3. Clique no separador "Visão Geral"
4. Selecione o botão "Mover"
5. Selecione "Mover-se para outra subscrição" a partir das opções de dropdown
6. Selecione o grupo de recursos onde pretende mover o seu cofre chave
7. Selecione o grupo de recursos onde pretende mover o seu cofre chave
8. Reconheça o aviso sobre os recursos móveis
9. Selecione "OK"

### <a name="additional-steps-post-move"></a>Passos adicionais (movimento pós-movimento)

Agora que mudou o seu cofre chave para a nova subscrição, precisa atualizar o ID do inquilino e remover as antigas políticas de acesso. Aqui estão os tutoriais para estes passos na PowerShell e Azure CLI.

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

Agora que o seu cofre está associado ao ID correto do inquilino e as entradas antigas da política de acesso são removidas, detete novas entradas de política de acesso com o Cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) ou o comando de definição de teclado Azure CLI [az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

Se estiver a usar uma identidade gerida para os recursos do Azure, também terá de a atualizar para o novo inquilino da AD Azure. Para obter mais informações sobre identidades geridas, consulte [A autenticação do Cofre-Chave com uma identidade gerida](managed-identity.md).

Se estiver a usar o MSI, também terá de atualizar a identidade MSI, uma vez que a identidade antiga deixará de estar no inquilino aD correto.


