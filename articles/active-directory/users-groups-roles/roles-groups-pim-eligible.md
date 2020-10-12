---
title: Atribuir um papel a um grupo que utilize a Gestão de Identidade Privilegiada no AZure AD ! Microsoft Docs
description: Saiba como pode atribuir um papel de Azure Ative Directory (Azure AD) a um grupo que utiliza a Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 93c97055c6e31050dbc545445ec144ba37abc865
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539899"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Atribuir um papel a um grupo que utilize a Gestão de Identidade Privilegiada

Este artigo descreve como pode atribuir um papel de Azure Ative Directory (Azure AD) a um grupo que utiliza a Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> Deve estar a utilizar a versão atualizada da Gestão de Identidade Privilegiada para poder atribuir um grupo a um papel AD Azure utilizando o PIM. Você pode estar na versão mais antiga da PIM se a sua organização Azure AD alavancar a API de Gestão de Identidade Privilegiada. Em caso afirmativo, por favor contacte o pseudónimo pim_preview@microsoft.com para mover a sua organização e atualizar a sua API. Saiba mais em [funções e funcionalidades da AD Azure em PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Usando o centro de administração Ad da Adure

1. Inscreva-se na [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) como administrador privilegiado ou administrador global na sua organização.

1. Selecione **funções de gestão de identidade privilegiada**  >  **Azure**  >  **AD**  >  **Add assignments**

1. Selecione uma função e, em seguida, selecione um grupo. Apenas são apresentados grupos elegíveis para a atribuição de funções (grupos atribuíveis por funções) e não todos os grupos.

    ![Screenshot que mostra a página "Adicionar atribuições" com as secções "Selecione função" e "Selecione membro(s)" realçadas.](./media/roles-groups-pim-eligible/select-member.png)

1. Selecione a definição de adesão desejada. Para funções que exijam ativação, escolha **elegível**. Por predefinição, o utilizador seria permanentemente elegível, mas também poderia definir um tempo de início e fim para a elegibilidade do utilizador. Assim que estiver concluído, bata em Save e Add para completar a tarefa de função.

    ![selecione o utilizador a quem está a atribuir a função](./media/roles-groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Com o PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>Descarregue o módulo PowerShell de pré-visualização Azure AD

Para instalar o módulo AD AD AD #PowerShell, utilize os seguintes cmdlets:

```powershell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, utilize o seguinte cmdlet:

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Atribuir um grupo como membro elegível de um papel

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Utilização da Microsoft Graph API

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>Passos seguintes

- [Use grupos de nuvem para gerir atribuições de funções](roles-groups-concept.md)
- [Resolver problemas de funções atribuídas a grupos de cloud](roles-groups-faq-troubleshooting.md)
- [Configurar configurações de função de administrador a Ad AD em Gestão de Identidade Privilegiada](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada](../privileged-identity-management/pim-resource-roles-assign-roles.md)
