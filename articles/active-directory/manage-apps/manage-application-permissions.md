---
title: Gestão de permissões de utilizador e administração - Azure Ative Directory / Microsoft Docs
description: Saiba como rever e gerir permissões para a aplicação no Azure AD. Por exemplo, se quiser revogar todas as permissões concedidas a um pedido.
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
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277649"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Tomar medidas sobre aplicações sobrepriviledgeed ou suspeitas no Azure Ative Directory

Saiba como rever e gerir permissões de aplicação. Com base no cenário, este artigo irá fornecer diferentes ações que pode realizar para garantir a sua aplicação. Isto aplica-se a todas as aplicações adicionadas ao seu inquilino Azure Ative (Azure AD) através do consentimento do utilizador ou administração.

Para obter mais informações sobre o consentimento dos pedidos, consulte [o quadro de consentimento do Diretório Ativo Azure](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

Ser capaz de executar as ações abaixo requer que você inscreva-se como administrador global, administrador de aplicação ou administrador de aplicação na nuvem.

Para restringir o acesso às aplicações, é necessário exigir a atribuição do utilizador e, em seguida, atribuir utilizadores ou grupos à aplicação.  Para obter mais informações, consulte [Métodos para atribuir utilizadores e grupos.](methods-for-assigning-users-and-groups.md)

Pode aceder ao portal AD AZure para obter scripts contextuais do PowerShell para executar as ações.
 
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação que pretende restringir o acesso.
4. Selecione **Permissões**. Na barra de comando, selecione **permissões de revisão**.

![Analisar permissões](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Quero controlar o acesso a uma aplicação

Recomendamos que restrinja o acesso a esta aplicação, ligando a definição de atribuição do Utilizador.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação que pretende restringir o acesso.
4. Selecione **Propriedades** e, em seguida, defina o requisito do utilizador exigido para sim.
5. Selecione **Utilizador e Grupos** e, em seguida, remova utilizadores indesejados atribuídos à aplicação.
6. Atribua o(s) utilizador(s) ou grupo(s) à aplicação.

Opcional, pode remover todos os utilizadores atribuídos à aplicação utilizando o PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Quero revogar todas as permissões para um pedido.

A utilização do PowerShell revoga todas as permissões concedidas a esta aplicação.

> [!NOTE]
> A revogação da permissão atual não impedirá os utilizadores de se reconseciarem nas aplicações. Se pretender impedir que os utilizadores consintam na aplicação, leia [Configure como os utilizadores finais concordam com as aplicações](configure-user-consent.md).

Opcional, pode desativar a aplicação para impedir que os utilizadores acedam à aplicação e à aplicação de aceder aos seus dados.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação que pretende restringir o acesso.
4. Selecione **Propriedades** e, em seguida, descreva ativado para os utilizadores iniciarem a s inscrição? para Não.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>A aplicação é suspeita e eu quero investigar

Recomendamos que restringa o acesso a esta aplicação, ligando a definição de atribuição do Utilizador e revendo as permissões que o utilizador e os administradores concederam à aplicação.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
3. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
5. Selecione a aplicação que pretende restringir o acesso.
6. Selecione **Propriedades** e, em seguida, defina o requisito do utilizador exigido para sim.
7. Selecione **Permissões** e reveja as permissões de administração e do utilizador.

Opcional, pode:

- Utilizando o PowerShell, remova todos os utilizadores designados para impedir que se inscrevam na aplicação.
- Utilizando o PowerShell, invalide os tokens de atualização para utilizadores que tenham acesso à aplicação.
- Utilizando o PowerShell, revogue todas as permissões para esta aplicação
- Desativar a aplicação para bloquear o acesso dos utilizadores e impedir que estas aplicações tenham acesso aos seus dados.


## <a name="application-is-malicious-and-im-compromised"></a>A aplicação é maliciosa e estou comprometida.

Recomendamos que desativar a aplicação para impedir que os utilizadores acedam à aplicação e à aplicação de aceder aos seus dados. Se, em vez disso, eliminar a aplicação, os utilizadores finais poderão reconsutor a aplicação e conceder acesso aos seus dados.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação que pretende restringir o acesso.
4. Selecione **Propriedades** e, em seguida, copie o ID do objeto.

### <a name="powershell-commands"></a>Comandos do PowerShell


Recuperar iD do objeto principal de serviço

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global, administrador de aplicação ou administrador de aplicações na nuvem.
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Selecione a aplicação que pretende restringir o acesso.
4. Selecione **propriedades** e, em seguida, copie o ID do objeto.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Remova todos os utilizadores designados para a aplicação.
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

Revogar permissões concedidas ao pedido

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
Tokens de atualização invalidado
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
## <a name="next-steps"></a>Passos seguintes
- [Gerir o consentimento para os pedidos e avaliar o pedido de consentimento](manage-consent-requests.md)
- [Configurar o consentimento do utilizador](configure-user-consent.md)
- [Configure fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)
