---
title: Gerir permissões de utilizador e administração - Azure Ative Directory / Microsoft Docs
description: Saiba como rever e gerir permissões para a aplicação no Azure AD. Por exemplo, revogar todas as permissões concedidas a um pedido.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ff97d0a69efbe624e959f92f5320f921476a306
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658983"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Tomar medidas sobre aplicações sobreprivilevasas ou suspeitas no Azure Ative Directory

Saiba como rever e gerir permissões de aplicação. Este artigo fornece diferentes ações que pode tomar para garantir a sua aplicação de acordo com o cenário. Estas ações aplicam-se a todas as aplicações adicionadas ao seu inquilino Azure Ative (Azure AD) através do consentimento do utilizador ou administração.

Para obter mais informações sobre o consentimento dos pedidos, consulte [o quadro de consentimento do Diretório Ativo Azure](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

Para fazer as seguintes ações, tem de se inscrever como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.

Para restringir o acesso às aplicações, é necessário exigir a atribuição do utilizador e, em seguida, atribuir utilizadores ou grupos à aplicação.  Para obter mais informações, consulte [Métodos para atribuir utilizadores e grupos.](./assign-user-or-group-access-portal.md)

Pode aceder ao portal AD AZure para obter scripts contextuais do PowerShell para executar as ações.
 
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação a que pretende restringir o acesso.
4. Selecione **Permissões**. Na barra de comando, selecione **permissões de revisão**.

![Screenshot da janela de permissões de revisão.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Controlar o acesso a uma aplicação

Recomendamos que restringa o acesso à aplicação ligando a definição de **atribuição do Utilizador.**

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação a que pretende restringir o acesso.
4. Selecione **Propriedades** e, em seguida, desemocione **o requisito do Utilizador exigido** para **Sim**.
5. Selecione **Utilizador e Grupos**, e, em seguida, remova os utilizadores indesejados que estão atribuídos à aplicação.
6. Atribua utilizadores ou grupos à aplicação.

Opcionalmente, pode remover todos os utilizadores que estejam designados para a aplicação utilizando o PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Revogar todas as permissões para uma aplicação

A utilização do script PowerShell revoga todas as permissões concedidas a esta aplicação.

> [!NOTE]
> A revogação da permissão atual não impedirá os utilizadores de re-consentirem com a aplicação. Se pretender bloquear o consentimento dos utilizadores, leia [Configure como os utilizadores concordam com as aplicações](configure-user-consent.md).

Opcionalmente, pode desativar a aplicação para impedir que os utilizadores acedam à aplicação e para impedir que a aplicação aceda aos seus dados.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação a que pretende restringir o acesso.
4. Selecione **Propriedades**, e, em seguida, definir **Habilitado para os utilizadores iniciarem** **a** sação?

## <a name="investigate-a-suspicious-application"></a>Investigue um pedido suspeito

Recomendamos que restringa o acesso à aplicação ligando a definição de **atribuição do Utilizador.** Em seguida, reveja as permissões que os utilizadores e administradores concederam à aplicação.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
3. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
5. Selecione a aplicação a que pretende restringir o acesso.
6. Selecione **Propriedades** e, em seguida, desemocione **o requisito do Utilizador exigido** para **Sim**.
7. Selecione **Permissões**, e reveja as permissões de administração e de consentimento do utilizador.

Opcionalmente, utilizando o PowerShell, pode:

- Remova todos os utilizadores designados para impedir que se inscrevam na aplicação.
- Invalidar fichas de atualização para utilizadores que tenham acesso à aplicação.
- Revogue todas as permissões para o pedido.

Ou pode desativar a aplicação para bloquear o acesso dos utilizadores e impedir o acesso da aplicação aos seus dados.


## <a name="disable-a-malicious-application"></a>Desativar uma aplicação maliciosa 

Recomendamos que desativar a aplicação para bloquear o acesso dos utilizadores e para evitar que a aplicação aceda aos seus dados. Se eliminar a aplicação em vez disso, os utilizadores poderão voltar a consentir a aplicação e conceder acesso aos seus dados.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação a que pretende restringir o acesso.
4. Selecione **Propriedades** e, em seguida, copie o ID do objeto.

### <a name="powershell-commands"></a>Comandos do PowerShell


Recupere a identificação principal do objeto de serviço.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação a que pretende restringir o acesso.
4. Selecione **Propriedades** e, em seguida, copie o ID do objeto.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Remova todos os utilizadores que estejam designados para a aplicação.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Revogar as permissões concedidas ao pedido.

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Invalidar as fichas de atualização.
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Próximos passos
- [Gerir o consentimento para os pedidos e avaliar o pedido de consentimento](manage-consent-requests.md)
- [Configurar o consentimento do utilizador](configure-user-consent.md)
- [Configure fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)