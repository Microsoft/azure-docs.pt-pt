---
title: Alterar o tipo de associação de grupo estático para dinâmico - Azure Active Directory | Documentos da Microsoft
description: Como criar regras de associação para preencher automaticamente os grupos e uma referência de regra.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d829ad9b85b6e7944f6dd534ea7fbb3f92a0d2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887922"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Alterar a associação de grupo estático para dinâmico no Azure Active Directory

Pode alterar a associação de um grupo de estático para dinâmico (ou vice-versa) no Azure Active Directory (Azure AD). O Azure AD mantém o mesmo nome do grupo e o ID no sistema, para que todas as referências existentes para o grupo ainda são válidas. Se criar um novo grupo em vez disso, terá de atualizar essas referências. Associação de grupo dinâmico elimina gestão sobrecarga adicionar e remover usuários. Este artigo mostra-lhe como converter os grupos existentes de estático para a associação de grupo dinâmica através do Centro de administração do Azure AD ou cmdlets do PowerShell.

> [!WARNING]
> Quando a alteração um grupo estático existente para um grupo dinâmico, todos os membros existentes são removidos do grupo e, em seguida, a regra de associação é processada para adicionar novos membros. Se o grupo é utilizado para controlar o acesso a aplicações ou recursos, lembre-se de que os membros originais poderão perder o acesso até que a regra de associação é totalmente processada.
>
> Recomendamos que teste a nova regra de associação com antecedência para se certificar de que a nova associação no grupo é conforme esperado.

## <a name="change-the-membership-type-for-a-group"></a>Alterar o tipo de associação para um grupo

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global ou um administrador de utilizadores no seu inquilino.
2. Selecione **grupos**.
3. Partir do **todos os grupos** lista, abra o grupo que pretende alterar.
4. Selecione **propriedades**.
5. Sobre o **propriedades** página para o grupo, selecione um **tipo de associação** de atribuído (estático), dinâmica de utilizador ou dispositivo dinâmico, consoante o tipo de associação pretendido. Para a associação dinâmica, pode usar o construtor de regra para selecionar as opções para uma regra simples ou escreve uma regra de associação. 

Os seguintes passos são um exemplo da alteração de um grupo de estático para a filiação dinâmica para um grupo de utilizadores.

1. No **propriedades** página para o seu grupo selecionado, selecione um **tipo de associação** de **utilizador dinâmico**, em seguida, selecione Sim na caixa de diálogo que explica as alterações ao grupo associação para continuar. 
  
   ![Selecione o tipo de associação do utilizador dinâmico](./media/groups-change-type/select-group-to-convert.png)
  
2. Selecione **adicionar consulta dinâmica**e, em seguida, forneça a regra.
  
   ![Introduza a regra](./media/groups-change-type/enter-rule.png)
  
3. Depois de criar a regra, selecione **adicionar consulta** na parte inferior da página.
4. Selecione **salvar** sobre o **propriedades** página para o grupo guardar as alterações. O **tipo de associação** do grupo é atualizada imediatamente na lista do grupo.

> [!TIP]
> Grupo conversão poderá falhar se a regra de associação que introduziu estava incorreta. É apresentada uma notificação no canto superior direito do portal que contém uma explicação de por que a regra não pode ser aceitos pelo sistema. Lê-lo cuidadosamente para compreender a forma como pode ajustar a regra para que seja válido. Para obter exemplos da sintaxe de regra e uma lista completa de propriedades suportadas, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).


## <a name="change-membership-type-for-a-group-powershell"></a>Alterar o tipo de associação para um grupo (PowerShell)

> [!NOTE]
> Para alterar as propriedades de grupo dinâmico, terá de utilizar os cmdlets do **a versão de pré-visualização** [Azure AD PowerShell versão 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Pode instalar a pré-visualização dos [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Eis um exemplo das funções que mudar o gerenciamento da associação de um grupo existente. Neste exemplo, é tomar cuidado para manipular a propriedade GroupTypes e preservar os valores que não estão relacionadas com associação de grupo dinâmica corretamente.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Para tornar um grupo estático:

```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Para tornar um grupo dinâmico:

```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Passos Seguintes

Estes artigos fornecem informações adicionais sobre os grupos no Azure Active Directory.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
