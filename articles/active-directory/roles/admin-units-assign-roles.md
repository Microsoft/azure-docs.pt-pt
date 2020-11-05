---
title: Atribuir e listar funções com âmbito de unidade administrativa - Azure Ative Directory / Microsoft Docs
description: Utilize unidades administrativas para restringir o âmbito das atribuições de funções no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ed2741c7dd754127a57642703b650a70637c63
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393443"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções âmbito a uma unidade administrativa

No Azure Ative Directory (Azure AD), para um controlo administrativo mais granular, pode atribuir os utilizadores a uma função AD Azure com um âmbito limitado a uma ou mais unidades administrativas.

Para preparar a utilização do PowerShell e do Microsoft Graph para a gestão da unidade administrativa, consulte [Começar](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Funções disponíveis

Função  |  Description
----- |  -----------
Administrador de Autenticação  |  Tem acesso a visualização, definição e reposição de informações do método de autenticação para qualquer utilizador não administrativo apenas na unidade administrativa atribuída.
Administrador de Grupos  |  Pode gerir todos os aspetos de grupos e configurações de grupos, tais como as políticas de nomeação e expiração, apenas na unidade administrativa atribuída.
Administrador helpdesk  |  Pode redefinir palavras-passe para administradores não administradores e helpdesk apenas na unidade administrativa atribuída.
Administrador de Licença  |  Pode atribuir, remover e atualizar as atribuições de licença apenas dentro da unidade administrativa.
Administrador de password  |  Pode redefinir palavras-passe apenas para administradores não administradores e administradores de passwords dentro da unidade administrativa atribuída.
Administrador de Utilizadores  |  Pode gerir todos os aspetos dos utilizadores e grupos, incluindo a reposição de palavras-passe para administradores limitados apenas dentro da unidade administrativa atribuída.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Princípios de segurança que podem ser atribuídos a uma função de âmbito

Os seguintes princípios de segurança podem ser atribuídos a uma função com âmbito de unidade administrativa:

* Utilizadores
* Grupos de nuvem atribuíveis por funções (pré-visualização)
* Nome do Principal do Serviço (SPN)

## <a name="assign-a-scoped-role"></a>Atribuir uma função de âmbito

Pode atribuir uma função de âmbito através do portal Azure, PowerShell ou Microsoft Graph.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá ao **Azure AD.**

1. Selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa a que pretende atribuir um âmbito de função de utilizador. 

1. No painel esquerdo, selecione **Funções e administradores** para listar todas as funções disponíveis.

   ![Screenshot do painel "Role and administrators" para selecionar uma unidade administrativa cujo âmbito de função pretende atribuir.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Selecione a função a atribuir e, em seguida, **selecione atribuições de Adicionar**. 

1. No painel **de atribuições Adicionar,** selecione um ou mais utilizadores para serem designados para o papel.

   ![Selecione a função para o âmbito e, em seguida, selecione atribuições adicionar](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Para atribuir um papel numa unidade administrativa utilizando a Azure AD Privileged Identity Management (PIM), consulte [as funções de AD Azure em PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Pode alterar a secção realçada conforme necessário para o ambiente específico.

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

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

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Ver uma lista dos administradores com âmbito numa unidade administrativa

Pode ver uma lista de administradores com âmbito de aplicação utilizando o portal Azure, PowerShell ou Microsoft Graph.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Pode ver todas as atribuições de funções criadas com um âmbito de unidade administrativa na [secção unidades administrativas da Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. No portal Azure, vá ao **Azure AD.**

1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa para a lista de atribuições de funções que deseja visualizar. 

1. Selecione **Funções e administradores** , e, em seguida, abra uma função para visualizar as atribuições na unidade administrativa.

### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Pode alterar a secção realçada conforme necessário para o seu ambiente específico.

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Passos seguintes

- [Utilizar os grupos da cloud para gerir atribuições de funções](groups-concept.md)
- [Funções de resolução de problemas atribuídas a grupos de nuvem](groups-faq-troubleshooting.md)
