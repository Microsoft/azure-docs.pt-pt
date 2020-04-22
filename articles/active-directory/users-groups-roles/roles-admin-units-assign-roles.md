---
title: Atribuir e listar funções com âmbito de unidade administrativa (pré-visualização) - Diretório Ativo Azure / Microsoft Docs
description: Utilização de unidades administrativas para restringir o âmbito das atribuições de funções no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c900b2a363a4ff271e7a436b358ecf170daca163
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687028"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções de âmbito a uma unidade administrativa

No Azure Ative Directory (Azure AD), pode atribuir aos utilizadores uma função Azure AD com um âmbito limitado a uma ou mais unidades administrativas (UA) para um controlo administrativo mais granular.

Para que as medidas se preparem para utilizar o PowerShell e o Microsoft Graph para gestão de unidades administrativas, consulte [Iniciar](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Funções disponíveis

Função  |  Descrição
----- |  -----------
Administrador de Autenticação  |  Tem acesso a visualização, conjunto e redefinição de informações do método de autenticação para qualquer utilizador não administrador apenas na unidade administrativa atribuída.
Administrador de Grupos  |  Pode gerir todos os aspetos de grupos e grupos, como políticas de nomeação e expiração apenas na unidade administrativa atribuída.
Administrador de Helpdesk  |  Pode redefinir palavras-passe para administradores não administradores e administradores de Helpdesk apenas na unidade administrativa atribuída.
Administrador de Licença  |  Pode atribuir, remover e atualizar atribuições de licença seleções apenas dentro da unidade administrativa.
Administrador de palavra-passe  |  Pode redefinir palavras-passe para não administradores e administradores de palavras-passe apenas na unidade administrativa atribuída.
Administrador de Utilizadores  |  Pode gerir todos os aspetos dos utilizadores e grupos, incluindo a reposição de senhas para administradores limitados apenas na unidade administrativa atribuída.

## <a name="assign-a-scoped-role"></a>Atribuir um papel de âmbito

### <a name="azure-portal"></a>Portal do Azure

Vá a **Azure AD > Unidades Administrativas** no portal. Selecione a unidade administrativa sobre a qual pretende atribuir a função a um utilizador. No painel esquerdo, selecione Funções e administradores para listar todas as funções disponíveis.

![Selecione uma unidade administrativa para alterar o âmbito de funções](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selecione a função a atribuir e, em seguida, **selecione Adicionar atribuições**. Isto irá deslizar um painel à direita onde pode selecionar um ou mais utilizadores para serem designados para a função.

![Selecione a função para o âmbito e, em seguida, selecione adicionar atribuições](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Enumerar os administradores de âmbito de aplicação numa UA

### <a name="azure-portal"></a>Portal do Azure

Todas as atribuições de funções efetuadas com um âmbito de unidade administrativa podem ser vistas na [secção unidades administrativas da AD Azure.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) Vá a **Azure AD > Unidades Administrativas** no portal. Selecione a unidade de administração para as atribuições de funções que pretende enumerar. Selecione **Funções e administradores** e abra um papel para visualizar as atribuições na unidade de administração.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas de unidades administrativas e FAQ](roles-admin-units-faq-troubleshoot.md)
