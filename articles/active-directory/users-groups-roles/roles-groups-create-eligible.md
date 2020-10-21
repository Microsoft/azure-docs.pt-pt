---
title: Criar um grupo para atribuir funções no Azure Ative Directory Microsoft Docs
description: Aprenda a criar um grupo atribuível a papéis no Azure AD. Gerir as funções Azure no portal Azure, PowerShell ou Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f14960f98a4257b0c8f920915327661cd8b867c
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310396"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Criar um grupo atribuível a funções no Azure Ative Directory

Só é possível atribuir um papel a um grupo que foi criado com a propriedade 'isAssignableToRole' definida para True, ou foi criada no portal AD AZure com **funções AD Azure pode ser atribuída ao grupo** ligado. Este atributo de grupo faz do grupo um que pode ser atribuído a um papel no Azure Ative Directory (Azure AD). Este artigo descreve como criar este tipo especial de grupo. **Nota:** Um grupo com propriedade isAssignableToRole definido para verdadeiro não pode ser de tipo de membro dinâmico. Para obter mais informações, consulte [utilizar um grupo para gerir as atribuições de funções Azure AD](./roles-groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Usando o centro de administração Ad da Adure

1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **Groups**  >  **Grupos Todos os grupos**  >  **Novo**grupo .

    [![Abra o Diretório Ativo Azure e crie um novo grupo.](media/roles-groups-create-eligible/new-group.png "Abra o Diretório Ativo Azure e crie um novo grupo.")](media/roles-groups-create-eligible/new-group.png#<lightbox>)

1. No separador **Grupo Novo,** forneça tipo de grupo, nome e descrição.
1. Ligue **as funções AZure AD pode ser atribuída ao grupo**. Este switch é visível apenas para administradores de funções privilegiados e administradores globais porque estes são apenas duas funções que podem definir o switch.

    [![Tornar o novo grupo elegível para a atribuição de funções](media/roles-groups-create-eligible/eligible-switch.png "Tornar o novo grupo elegível para a atribuição de funções")](media/roles-groups-create-eligible/eligible-switch.png#<lightbox>)

1. Selecione os membros e proprietários para o grupo. Você também tem a opção de atribuir papéis ao grupo, mas atribuir um papel não é necessário aqui.

    [![Adicione os membros ao grupo atribuível por funções e atribua funções.](media/roles-groups-create-eligible/specify-members.png "Adicione os membros ao grupo atribuível por funções e atribua funções.")](media/roles-groups-create-eligible/specify-members.png#<lightbox>)

1. Depois de especificar os membros e os proprietários, selecione **Criar**.

    [![O botão Criar está na parte inferior da página.](media/roles-groups-create-eligible/create-button.png "O botão Criar está na parte inferior da página.")](media/roles-groups-create-eligible/create-button.png#<lightbox>)

O grupo é criado com quaisquer funções que lhe possas ter atribuído.

## <a name="using-powershell"></a>Utilizar o PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Instale o módulo de pré-visualização AZure AD

```powershell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, emita o seguinte comando:

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Criar um grupo que possa ser atribuído ao papel

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Para este tipo de grupo, `isPublic` será sempre falso e será sempre `isSecurityEnabled` verdade.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Copie os utilizadores e os principais de serviço de um grupo para atribuir funções

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Utilização da Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Criar um grupo atribuível a papéis em Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

Para este tipo de grupo, `isPublic` será sempre falso e será sempre `isSecurityEnabled` verdade.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel a um grupo de nuvens](roles-groups-assign-role.md)
- [Use grupos de nuvem para gerir atribuições de funções](roles-groups-concept.md)
- [Resolver problemas de funções atribuídas a grupos de cloud](roles-groups-faq-troubleshooting.md)