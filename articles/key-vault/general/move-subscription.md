---
title: Azure Key Vault movendo um cofre para uma subscrição diferente | Microsoft Docs
description: Orientação sobre a mudança de um cofre chave para uma subscrição diferente.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 1a1cd8c051f9e04c09ef2986805873d8e7fea54e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817635"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Mover um Azure Key Vault para outra subscrição

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição Geral

> [!IMPORTANT]
> **Mover um cofre chave para outra subscrição irá causar uma mudança de rutura no seu ambiente.**
> Certifique-se de que compreende o impacto desta mudança e siga cuidadosamente as orientações deste artigo antes de decidir mover o cofre-chave para uma nova subscrição.
> Se estiver a utilizar identidades de serviço gerido (MSI), leia as instruções pós-movimento no final do documento. 

[O Azure Key Vault](overview.md) está automaticamente ligado ao ID do inquilino [Azure Ative Directory](../../active-directory/fundamentals/active-directory-whatis.md) padrão para a subscrição em que é criado. Pode encontrar o ID do inquilino associado à sua subscrição seguindo este [guia.](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md) Todas as entradas de políticas de acesso e atribuições de funções também estão ligadas a este ID do inquilino.  Se mudar a sua assinatura Azure do inquilino A para o inquilino B, os seus cofres-chave existentes serão inacessíveis pelos principais serviços (utilizadores e aplicações) no inquilino B. Para resolver este problema, tem de:

* Mude a identificação do inquilino associada a todos os cofres chave existentes na subscrição do inquilino B.
* Remover todas as entradas de política de acesso existentes.
* Adicione novas entradas de política de acesso associadas ao inquilino B.

Para obter mais informações sobre a Azure Key Vault e o Azure Ative Directory, consulte
- [Acerca do Azure Key Vault](overview.md)
- [O que é o Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Como localizar o ID do inquilino](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Limitações

> [!IMPORTANT]
> **Os cofres-chave utilizados para encriptação do disco não podem ser movidos** Se estiver a utilizar o cofre-chave com encriptação de disco para um VM, o cofre de teclas não pode ser movido para um grupo de recursos diferente ou uma subscrição enquanto a encriptação do disco estiver ativada. Tem de desativar a encriptação do disco antes de mover o cofre de chaves para um novo grupo de recursos ou subscrição. 

Alguns diretores de serviço (utilizadores e aplicações) estão ligados a um inquilino específico. Se você mudar o seu cofre chave para uma subscrição em outro inquilino, há a chance de que você não será capaz de restaurar o acesso a um diretor de serviço específico. Verifique se todos os diretores de serviço essenciais existem no inquilino para onde você está movendo o seu cofre chave.

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso ao](../../role-based-access-control/built-in-roles.md#contributor) nível do contribuinte ou superior à subscrição atual onde existe o cofre-chave. Pode atribuir funções através do [portal Azure](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)ou [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* [Acesso](../../role-based-access-control/built-in-roles.md#contributor) ao nível do contribuinte ou superior à subscrição onde pretende mover o cofre da chave. Pode atribuir funções através do [portal Azure](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)ou [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Um grupo de recursos na nova subscrição. Pode criar um utilizando o [portal Azure,](../../azure-resource-manager/management/manage-resource-groups-portal.md) [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)ou [Azure CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md).

Pode verificar as funções existentes utilizando o [portal Azure,](../../role-based-access-control/role-assignments-list-portal.md) [PowerShell,](../../role-based-access-control/role-assignments-list-powershell.md) [Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)ou [REST API](../../role-based-access-control/role-assignments-list-rest.md).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Mover um cofre chave para uma nova subscrição

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Navegue até ao cofre [da chave](overview.md)
3. Clique no separador "Visão Geral"
4. Selecione o botão "Mover"
5. Selecione "Mover para outra subscrição" a partir das opções de dropdown
6. Selecione o grupo de recursos onde pretende mover o cofre de chaves
7. Reconheça o aviso sobre os recursos em movimento
8. Selecione "OK".

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Passos adicionais quando a subscrição está em um novo inquilino

Se você mudou o seu cofre chave para uma subscrição em um novo inquilino, você precisa atualizar manualmente o ID do inquilino e remover antigas políticas de acesso e atribuições de funções. Aqui estão os tutoriais para estes passos em PowerShell e Azure CLI. Se estiver a utilizar o PowerShell, poderá ter de executar o comando Clear-AzContext documentado abaixo para lhe permitir ver recursos fora do âmbito atual selecionado. 

### <a name="update-tenant-id-in-a-key-vault"></a>Atualizar iD do inquilino em um cofre chave

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
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
### <a name="update-access-policies-and-role-assignments"></a>Atualizar políticas de acesso e atribuições de funções

> [!NOTE]
> Se o Key Vault estiver a utilizar o modelo de permissão [Azure RBAC.](../../role-based-access-control/overview.md) Também precisa remover as tarefas de papel chave do cofre. Pode remover atribuições de funções utilizando [o Portal Azure,](../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../role-based-access-control/role-assignments-cli.md)ou [PowerShell](../../role-based-access-control/role-assignments-powershell.md). 

Agora que o seu cofre está associado com o ID do inquilino correto e as antigas entradas de políticas de acesso ou atribuições de funções são removidas, definir novas entradas de políticas de acesso ou atribuições de funções.

Para atribuir políticas, consulte:
- [Atribuir uma política de acesso usando o Portal](assign-access-policy-portal.md)
- [Atribuir uma política de acesso usando o Azure CLI](assign-access-policy-cli.md)
- [Atribuir uma política de acesso utilizando o PowerShell](assign-access-policy-powershell.md)

Para adicionar atribuições de funções, consulte:
- [Atribuir funções Azure usando o portal Azure](../../role-based-access-control/role-assignments-portal.md)
- [Atribuir funções Azure usando Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Atribuir funções Azure usando PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Atualizar identidades geridas

Se você está transferindo toda a subscrição e usando uma identidade gerida para recursos Azure, você precisará atualizá-lo para o novo inquilino do Azure Ative Directory também. Para obter mais informações sobre identidades geridas, [visão geral da identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md).

Se estiver a usar a identidade gerida, também terá de atualizar a identidade porque a identidade antiga deixará de estar no inquilino correto do Azure Ative Directory. Consulte os seguintes documentos para ajudar a resolver esta questão. 

* [Atualizar o MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Subscrição de transferência para novo diretório](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-certificates.md)
- Para obter informações conceptuais, incluindo como interpretar registos do Key Vault, consulte a [sessão do Key Vault](logging.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
- [Funcionalidades de segurança Azure Key Vault](security-features.md)
- [Configurar firewalls de cofre de chaves Azure e redes virtuais](network-security.md)