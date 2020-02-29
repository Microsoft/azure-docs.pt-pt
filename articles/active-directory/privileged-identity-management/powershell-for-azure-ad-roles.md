---
title: PowerShell para papéis de AD Azure na PIM - Azure AD / Microsoft Docs
description: Gerir funções de AD Azure utilizando cmdlets PowerShell em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: efb85b4a54b8f61e44f1f8bc75f893f93a0feb8a
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164961"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell para funções de AD Azure na Gestão de Identidade Privilegiada

Este artigo contém instruções para a utilização de cmdlets powerShell de Diretório Ativo Azure (Azure AD) para gerir funções de AD Azure na Gestão de Identidade Privilegiada (PIM). Também lhe diz como se configurar com o módulo PowerShell Azure AD.

> [!Note]
> O nosso PowerShell oficial só é suportado se estiver na nova versão da Azure AD Privileged Identity Management. Por favor, dirija-se à Privilegiada Gestão de Identidade e certifique-se de que tem o seguinte banner na lâmina de arranque rápida.
> [![verificar a versão de Gestão de Identidade Privilegiada que tem](media/pim-how-to-add-role-to-user/pim-new-version.png "Selecione Azure AD > Gestão de Identidade Privilegiada")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Se não tiver este banner, por favor espere já que estamos em processo de lançar esta experiência atualizada ao longo das próximas semanas.
> Os cmdlets de Gestão de Identidade Privilegiada PowerShell são suportados através do módulo de pré-visualização da AD Azure. Se tiver usado um módulo diferente e esse módulo estiver agora a devolver uma mensagem de erro, comece a utilizar este novo módulo. Se tiver algum sistema de produção construído em cima de um módulo diferente, por favor contacte a pim_preview@microsoft.com

## <a name="installation-and-setup"></a>Instalação e Configuração

1. Instale o módulo de pré-visualização azure AD

        Install-module AzureADPreview

1. Certifique-se de que tem as permissões de funções necessárias antes de prosseguir. Se estiver a tentar executar tarefas de gestão como dar uma atribuição de funções ou atualizar a definição de funções, certifique-se de que tem o cargo de administrador global ou de administrador privilegiado. Se estiver apenas a tentar ativar a sua própria atribuição, não são necessárias permissões para além das permissões de utilizador predefinidas.

1. Ligue-se ao Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Encontre o seu ID de inquilino indo para **o Azure Ative Directory** > **Properties** > **Id de Diretório**. Na secção cmdlets, utilize este ID sempre que necessário fornecer o recursoId.

    ![Encontre a identificação do inquilino nas propriedades para a organização Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> As seguintes secções são exemplos simples que podem ajudá-lo a funcionar. Pode encontrar documentação mais detalhada sobre os seguintes cmdlets em https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management. No entanto, terá de substituir "azureResources" no parâmetro do fornecedor ID por "aadRoles". Você também precisa lembrar-se de usar o ID do inquilino para a sua organização Azure AD como o parâmetro de recursos Id.

## <a name="retrieving-role-definitions"></a>Recuperação de definições de papéis

Use o seguinte cmdlet para obter todas as funções de Azure AD incorporadas e personalizadas na sua organização Azure AD (inquilino). Este passo importante dá-lhe o mapeamento entre o nome da função e o roleDefinitionId. O roleDefinitionId é utilizado ao longo destes cmdlets de forma a referir uma função específica.

O roleDefinitionId é específico da sua organização Azure AD e é diferente do roleDefinitionId devolvido pela API de gestão de funções.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Resultado:

![Obtenha todos os papéis para a organização Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Recuperação de atribuições de papéis

Utilize o seguinte cmdlet para recuperar todas as atribuições de funções na sua organização Azure AD.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Utilize o seguinte cmdlet para recuperar todas as atribuições de funções para um determinado utilizador. Esta lista também é conhecida como "Os meus papéis" no portal Azure AD. A única diferença aqui é que você adicionou um filtro para o ID do sujeito. O ID do sujeito neste contexto é o ID do utilizador ou o ID do grupo.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Utilize o seguinte cmdlet para recuperar todas as atribuições de funções para um papel particular. O roleDefinitionId aqui é o ID que é devolvido pelo cmdlet anterior.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Os cmdlets resultam numa lista de objetos de atribuição de papéis mostrados abaixo. O ID do sujeito é o ID do utilizador a quem a função é atribuída. O estado de atribuição pode ser ativo ou elegível. Se o utilizador estiver ativo e houver um ID no campo LinkedERoleAssignmentId, isso significa que a função está ativada.

Resultado:

![Recuperar todas as atribuições de funções para a organização Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Atribuir um papel

Utilize o seguinte cmdlet para criar uma atribuição elegível.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

O calendário, que define o tempo de início e fim da atribuição, é um objeto que pode ser criado como o seguinte exemplo:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"

## <a name="activate-a-role-assignment"></a>Ativar uma atribuição de funções

Utilize o seguinte cmdlet para ativar uma atribuição elegível.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Este cmdlet é quase idêntico ao cmdlet para criar uma atribuição de funções. A diferença-chave entre os cmdlets é que para o parâmetro -Tipo, a ativação é "userAdd" em vez de "adminAdd". A outra diferença é que o parâmetro do Estado de Atribuição é "Ativo" em vez de "Elegível".

> [!Note]
> Existem dois cenários limitantes para a ativação de papéis através do PowerShell.
> 1. Se necessitar do sistema de bilhetes /número de bilhete na sua definição de funções, não há forma de os fornecer como parâmetro. Assim, não seria possível ativar o papel para além do portal Azure. Esta funcionalidade está a ser lançada para a PowerShell ao longo dos próximos meses.
> 1. Se necessitar de autenticação multifactor para ativação de funções, não existe atualmente forma de a PowerShell desafiar o utilizador quando ativar a sua função. Em vez disso, os utilizadores terão de desencadear o desafio MFA quando se ligarem ao Azure AD seguindo [esta publicação](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) de blogue de um dos nossos engenheiros. Se estiver a desenvolver uma aplicação para PIM, uma possível implementação é desafiar os utilizadores e reconectá-los ao módulo depois de receberem um erro "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Recuperação e atualização de definições de funções

Utilize o seguinte cmdlet para obter todas as definições de funções na sua organização Azure AD.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Há quatro objetos principais na configuração. Apenas três destes objetos são atualmente utilizados pela PIM. As Definições dos UserMemberSão configurações de ativação, As Configurações De Atribuição De Emissão AdminEeeis são definições de atribuição para atribuições elegíveis, e as Definições de Membros Do D'Ad.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Para atualizar a definição de funções, terá primeiro de definir um objeto de definição da seguinte forma:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

Em seguida, pode ir em frente e aplicar a definição a um dos objetos para um papel particular, como mostrado abaixo. O ID aqui é a definição de funções ID que pode ser recuperado a partir do resultado das definições de funções da lista cmdlet.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId ‘aadRoles’ -Id ‘ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId ‘3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId ‘2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Passos seguintes

- [Atribuir uma função personalizada azure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de funções personalizadas da AD Azure](azure-ad-custom-roles-update-remove.md)
- [Configure uma atribuição de funções personalizadas da Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de papéis em Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
