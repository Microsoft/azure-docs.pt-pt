---
title: Atribuir uma política de acesso a Azure Key Vault
description: Como utilizar o portal Azure, Azure CLI ou Azure PowerShell para atribuir uma política de acesso ao Cofre chave a um principal de serviço ou identidade de aplicação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 817b9bfc0af054b344ca9f770085ac022a8e6eac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381275"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Atribua uma política de acesso ao Cofre de Chaves utilizando o Azure PowerShell

Uma política de acesso ao Cofre-Chave determina se um dado responsável de serviço, nomeadamente uma aplicação ou grupo de utilizadores, pode realizar diferentes operações em [segredos](../secrets/index.yml)do Cofre chave, [chaves](../keys/index.yml)e [certificados](../certificates/index.yml). Pode atribuir políticas de acesso utilizando o [portal Azure,](assign-access-policy-portal.md)o [Azure CLI](assign-access-policy-cli.md)ou a Azure PowerShell (este artigo).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para obter mais informações sobre a criação de grupos no Azure Ative Directory utilizando a Azure PowerShell, consulte [o New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) e [o Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Configurar PowerShell e iniciar seduca

1. Para executar comandos localmente, instale [a Azure PowerShell](/powershell/azure/) se ainda não o fez.

    Para executar comandos diretamente na nuvem, utilize a Concha da [Nuvem Azure](/azure/cloud-shell/overview).

1. Apenas powershell local:

    1. Instale o [módulo PowerShell do Diretório Ativo Azure](https://www.powershellgallery.com/packages/AzureAD).

    1. Inscreva-se em Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Adquira o ID do objeto

Determine o ID do objeto da aplicação, grupo ou utilizador ao qual pretende atribuir a política de acesso:

- Aplicações e outros principais de serviço: utilize o cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) com o `-SearchString` parâmetro para filtrar os resultados para o nome do chefe de serviço pretendido:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Grupos: utilize o cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) com o `-SearchString` parâmetro para filtrar os resultados para o nome do grupo pretendido:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    Na saída, o ID do objeto está listado como `Id` .

- Utilizadores: utilize o [cmdlet Get-AzADUser,](/powershell/module/az.resources/get-azaduser) passando o endereço de e-mail do utilizador para o `-UserPrincipalName` parâmetro.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    Na saída, o ID do objeto está listado como `Id` .

## <a name="assign-the-access-policy"></a>Atribuir a política de acesso

Utilize o [cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para atribuir a política de acesso:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Só é necessário incluir `-PermissionsToSecrets` , e ao atribuir `-PermissionsToKeys` `-PermissionsToCertificates` permissões a esses tipos específicos. Os valores admissíveis `<secret-permissions>` `<key-permissions>` para, e `<certificate-permissions>` são dados na documentação [Set-AzKeyVaultAccessPolicy - Parâmetros.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters)

## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre Azure Key: Gestão de identidade e acesso](overview-security.md#identity-and-access-management)
- [Prenda o cofre da chave.](secure-your-key-vault.md)
- [Guia de desenvolvedores do Azure Key Vault](developers-guide.md)
- [As melhores práticas do Azure Key Vault](best-practices.md)
