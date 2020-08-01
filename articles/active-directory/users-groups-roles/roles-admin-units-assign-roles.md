---
title: Atribuir e listar funções com âmbito de unidade administrativa (pré-visualização) - Diretório Ativo Azure Microsoft Docs
description: Utilização de unidades administrativas para restringir o âmbito das atribuições de funções no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 918675b111b7b1b85669692b63fed683ea2831f8
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475639"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções âmbito a uma unidade administrativa

No Azure Ative Directory (Azure AD), pode atribuir os utilizadores a uma função AD Azure com um âmbito limitado a uma ou mais unidades administrativas (AUs) para um controlo administrativo mais granular.

Para obter os passos que se preparem para utilizar o PowerShell e o Microsoft Graph para a gestão da unidade administrativa, consulte [Get start](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Funções disponíveis

Função  |  Descrição
----- |  -----------
Administrador de Autenticação  |  Tem acesso a visualização, definição e reposição de informações do método de autenticação para qualquer utilizador não administrativo apenas na unidade administrativa atribuída.
Administrador de Grupos  |  Pode gerir todos os aspetos de grupos e grupos configurações como a definição e expiração apenas na unidade administrativa atribuída.
Administrador helpdesk  |  Pode redefinir palavras-passe para administradores não administradores e helpdesk apenas na unidade administrativa atribuída.
Administrador de Licença  |  Pode atribuir, remover e atualizar as atribuições de licença apenas dentro da unidade administrativa.
Administrador de password  |  Pode redefinir palavras-passe apenas para administradores não administradores e administradores de passwords dentro da unidade administrativa atribuída.
Administrador do Utilizador  |  Pode gerir todos os aspetos dos utilizadores e grupos, incluindo a reposição de palavras-passe para administradores limitados apenas dentro da unidade administrativa atribuída.

## <a name="assign-a-scoped-role"></a>Atribuir uma função de âmbito

### <a name="azure-portal"></a>Portal do Azure

Vá ao **Azure AD > Unidades Administrativas** no portal. Selecione a unidade administrativa sobre a qual pretende atribuir a função a um utilizador. No painel esquerdo, selecione Funções e administradores para listar todas as funções disponíveis.

![Selecione uma unidade administrativa para alterar o âmbito de função](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selecione a função a atribuir e, em seguida, **selecione atribuir atribuições .** Abre-se um painel à direita, onde pode selecionar um ou mais utilizadores a atribuir à função.

![Selecione a função para o âmbito e, em seguida, selecione atribuições adicionar](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Listar os administradores de âmbito numa AU

### <a name="azure-portal"></a>Portal do Azure

Todas as atribuições de funções escruladas com um âmbito de unidade administrativa podem ser vistas na [secção de unidades administrativas da Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Vá ao **Azure AD > Unidades Administrativas** no portal. Selecione a unidade de administração para as atribuições de funções que pretende listar. Selecione **Funções e administradores** e abra uma função para visualizar as atribuições na unidade de administração.

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Passos seguintes

- [Use grupos de nuvem para gerir atribuições de funções](roles-groups-concept.md)
- [Funções de resolução de problemas atribuídas a grupos de nuvem](roles-groups-faq-troubleshooting.md)
