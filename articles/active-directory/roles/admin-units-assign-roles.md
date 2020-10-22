---
title: Atribuir e listar funções com âmbito de unidade administrativa - Azure Ative Directory / Microsoft Docs
description: Utilização de unidades administrativas para restringir o âmbito das atribuições de funções no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a4810b3a84cac55a49744025b6ac71c3f1c0a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377950"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções âmbito a uma unidade administrativa

No Azure Ative Directory (Azure AD), pode atribuir os utilizadores a uma função AD Azure com um âmbito limitado a uma ou mais unidades administrativas (AUs) para um controlo administrativo mais granular.

Para obter os passos que se preparem para utilizar o PowerShell e o Microsoft Graph para a gestão da unidade administrativa, consulte [Get start](admin-units-manage.md#get-started).

## <a name="roles-available"></a>Funções disponíveis

Função  |  Descrição
----- |  -----------
Administrador de Autenticação  |  Tem acesso a visualização, definição e reposição de informações do método de autenticação para qualquer utilizador não administrativo apenas na unidade administrativa atribuída.
Administrador de Grupos  |  Pode gerir todos os aspetos de grupos e grupos configurações como a definição e expiração apenas na unidade administrativa atribuída.
Administrador helpdesk  |  Pode redefinir palavras-passe para administradores não administradores e helpdesk apenas na unidade administrativa atribuída.
Administrador de Licença  |  Pode atribuir, remover e atualizar as atribuições de licença apenas dentro da unidade administrativa.
Administrador de password  |  Pode redefinir palavras-passe apenas para administradores não administradores e administradores de passwords dentro da unidade administrativa atribuída.
Administrador de Utilizadores  |  Pode gerir todos os aspetos dos utilizadores e grupos, incluindo a reposição de palavras-passe para administradores limitados apenas dentro da unidade administrativa atribuída.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Princípios de segurança que podem ser atribuídos a uma função de âmbito

Os seguintes princípios de segurança podem ser atribuídos a uma função com âmbito de unidade administrativa:

* Utilizadores
* Grupos de nuvem atribuíveis por função (pré-visualização)
* Nome do Principal do Serviço (SPN)

## <a name="assign-a-scoped-role"></a>Atribuir uma função de âmbito

### <a name="azure-portal"></a>Portal do Azure

Vá ao **Azure AD > Unidades Administrativas** no portal. Selecione a unidade administrativa sobre a qual pretende atribuir a função a um utilizador. No painel esquerdo, selecione Funções e administradores para listar todas as funções disponíveis.

![Selecione uma unidade administrativa para alterar o âmbito de função](./media/admin-units-assign-roles/select-role-to-scope.png)

Selecione a função a atribuir e, em seguida, **selecione atribuir atribuições .** Abre-se um painel à direita, onde pode selecionar um ou mais utilizadores a atribuir à função.

![Selecione a função para o âmbito e, em seguida, selecione atribuições adicionar](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Para atribuir um papel numa unidade administrativa utilizando a PIM, siga os passos [aqui](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
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
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Passos seguintes

- [Use grupos de nuvem para gerir atribuições de funções](groups-concept.md)
- [Resolver problemas de funções atribuídas a grupos de cloud](groups-faq-troubleshooting.md)
