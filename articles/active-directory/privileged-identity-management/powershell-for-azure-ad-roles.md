---
title: PowerShell para funções de AD Azure em PIM - Azure AD / Microsoft Docs
description: Gerir as funções AZure AD utilizando cmdlets PowerShell em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a3c5274a1b63d486bb7eb48b89560d5684db2e2
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317014"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell para funções de AD Azure em Gestão de Identidade Privilegiada

Este artigo contém instruções para a utilização de cmdlets PowerShell (Azure AD) para gerir as funções de Azure AD em Gestão de Identidade Privilegiada (PIM). Também lhe diz como se configurar com o módulo Azure AD PowerShell.

> [!Note]
> O nosso PowerShell oficial só é suportado se estiver na nova versão da Azure AD Privileged Identity Management. Por favor, vá à Gestão de Identidade Privilegiada e certifique-se de que tem o seguinte banner na lâmina de arranque rápido.
> [![verifique a versão de Gestão de Identidade Privilegiada que tem](media/pim-how-to-add-role-to-user/pim-new-version.png "Selecione Azure AD > Gestão de Identidade Privilegiada")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Se você não tem este banner, por favor, espere como estamos no processo de lançar esta experiência atualizada ao longo das próximas semanas.
> Os cmdlets De Gestão de Identidade Privilegiadas PowerShell são suportados através do módulo de pré-visualização Azure AD. Se tiver usado um módulo diferente e esse módulo estiver agora a devolver uma mensagem de erro, comece a utilizar este novo módulo. Se tiver sistemas de produção construídos em cima de um módulo diferente, por favor [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) contacte.

## <a name="installation-and-setup"></a>Instalação e Configuração

1. Instale o módulo de pré-visualização AZure AD

    ```powershell
    Install-module AzureADPreview
    ```

1. Certifique-se de que tem as permissões de função necessárias antes de prosseguir. Se estiver a tentar executar tarefas de gestão como dar uma atribuição de funções ou atualizar a definição de funções, certifique-se de que tem o cargo de administrador global ou de administrador privilegiado. Se estiver apenas a tentar ativar a sua própria atribuição, não são necessárias permissões para além das permissões do utilizador predefinidas.

1. Ligue-se ao Azure AD.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Encontre o ID do Inquilino para a sua organização Azure AD indo para **a Azure Ative Directory**  >  **Properties**  >  **ID**. Na secção cmdlets, utilize este ID sempre que precisar de fornecer os recursosId.

    ![Encontre o ID da organização nas propriedades da organização AZure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> As seguintes secções são exemplos simples que podem ajudá-lo a funcionar. Pode encontrar documentação mais detalhada sobre os seguintes cmdlets em [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true](/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true) . No entanto, deve substituir "azureResources" no parâmetro providerID por "aadRoles". Também terá de se lembrar de usar o ID do Inquilino para a sua organização AZure AD como parâmetro de recursosId.

## <a name="retrieving-role-definitions"></a>Recuperação de definições de funções

Use o cmdlet seguinte para obter todos os papéis AD Azure incorporados e personalizados na sua organização AZure AD. Este passo importante dá-lhe o mapeamento entre o nome de função e o papelDefinitionId. O papelDefinitionId é usado ao longo destes cmdlets para referir um papel específico.

O papelDefinitionId é específico da sua organização Azure AD e é diferente do papelDefinitionId devolvido pela API de gestão de funções.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Resultado:

![Obtenha todos os papéis para a organização AD Azure](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Recuperação de funções

Utilize o cmdlet seguinte para recuperar todas as atribuições de funções na sua organização Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

Utilize o cmdlet seguinte para recuperar todas as atribuições de funções para um determinado utilizador. Esta lista também é conhecida como "Os meus papéis" no portal AD A. A única diferença aqui é que adicionou um filtro para o iD do sujeito. O ID do sujeito neste contexto é o ID do utilizador ou o ID do grupo.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

Utilize o cmdlet seguinte para recuperar todas as atribuições de funções para um papel específico. O papelDefinitionId aqui é o ID que é devolvido pelo cmdlet anterior.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

Os cmdlets resultam numa lista de objetos de atribuição de funções apresentados abaixo. O ID do sujeito é o ID do utilizador do utilizador a quem a função é atribuída. O estado de atribuição pode ser ativo ou elegível. Se o utilizador estiver ativo e houver um ID no campo LinkedEligibleRoleAssignmentId, isso significa que a função está atualmente ativada.

Resultado:

![Recupere todas as atribuições de funções para a organização AZure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Atribuir um papel

Utilize o cmdlet seguinte para criar uma atribuição elegível.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

O calendário, que define o início e o fim da atribuição, é um objeto que pode ser criado como o seguinte exemplo:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Se o valor do fim DoteTime estiver definido para nula, indica uma atribuição permanente.

## <a name="activate-a-role-assignment"></a>Ativar uma atribuição de funções

Utilize o cmdlet seguinte para ativar uma atribuição elegível.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

Este cmdlet é quase idêntico ao cmdlet para criar uma atribuição de funções. A diferença-chave entre os cmdlets é que para o parâmetro -Tipo, a ativação é "userAdd" em vez de "adminAdd". A outra diferença é que o parâmetro -Estado de Atribuição é "Ativo" em vez de "Elegível".

> [!Note]
> Existem dois cenários limitantes para a ativação de funções através do PowerShell.
> 1. Se necessitar do sistema de bilhetes/ número de bilhete na definição de função, não há como fornecê-los como parâmetro. Assim, não seria possível ativar o papel para além do portal Azure. Esta funcionalidade está a ser lançada para o PowerShell ao longo dos próximos meses.
> 1. Se necessitar de autenticação multi-factor para ativação de funções, não existe atualmente forma de o PowerShell desafiar o utilizador quando ativar a sua função. Em vez disso, os utilizadores terão de desencadear o desafio MFA quando se ligarem ao Azure AD, seguindo [esta publicação](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) de um dos nossos engenheiros. Se estiver a desenvolver uma aplicação para PIM, uma possível implementação é desafiar os utilizadores e reconectá-los ao módulo depois de receberem um erro "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Recuperação e atualização de definições de funções

Utilize o cmdlet seguinte para obter todas as definições de funções na sua organização Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

Há quatro objetos principais na configuração. Apenas três destes objetos são atualmente utilizados pela PIM. As definições de ativação do UserMember são configurações de ativação, as definições de atribuição de adminEligibles são definições de atribuição para atribuições elegíveis, e as AdminmememberSettings são definições de atribuição para atribuições ativas.

[![Obtenha e atualize as definições de funções.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Para atualizar a definição de funções, tem de obter o objeto de definição existente para uma determinada função e fazer alterações:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

Em seguida, pode ir em frente e aplicar a definição a um dos objetos para um papel particular, como mostrado abaixo. O ID aqui é o ID de definição de função que pode ser recuperado do resultado do cmdlet de definições de função da lista.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel personalizado AZure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configure uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função em Azure AD](../users-groups-roles/directory-assign-admin-roles.md)