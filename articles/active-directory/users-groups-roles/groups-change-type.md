---
title: Alterar a associação de grupo estático para dinâmico – Azure AD | Microsoft Docs
description: Como criar regras de associação para preencher automaticamente os grupos e uma referência de regra.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34451fcc4597f77464e5e9566613e21e9fecdbc6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027319"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Alterar a associação de grupo estático para dinâmico no Azure Active Directory

Você pode alterar a associação de um grupo de estático para dinâmico (ou vice-versa) no Azure Active Directory (Azure AD). O Azure AD mantém o mesmo nome e ID do grupo no sistema, de modo que todas as referências existentes ao grupo ainda são válidas. Se você criar um novo grupo em vez disso, precisará atualizar essas referências. A associação de grupo dinâmico elimina a sobrecarga de gerenciamento adicionando e removendo usuários. Este artigo mostra como converter grupos existentes de associação estática para dinâmica usando o centro de administração do Azure AD ou cmdlets do PowerShell.

> [!WARNING]
> Ao alterar um grupo estático existente para um grupo dinâmico, todos os membros existentes são removidos do grupo e, em seguida, a regra de associação é processada para adicionar novos membros. Se o grupo for usado para controlar o acesso a aplicativos ou recursos, lembre-se de que os membros originais podem perder o acesso até que a regra de associação seja totalmente processada.
>
> Recomendamos que você teste a nova regra de associação antecipadamente para verificar se a nova associação no grupo está conforme o esperado.

## <a name="change-the-membership-type-for-a-group"></a>Alterar o tipo de associação de um grupo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador global ou um administrador de usuário em seu locatário.
2. Selecione **grupos**.
3. Na lista **todos os grupos** , abra o grupo que você deseja alterar.
4. Selecione **Propriedades**.
5. Na página **Propriedades** do grupo, selecione um tipo de **Associação** de um dispositivo atribuído (estático), de usuário dinâmico ou dinâmico, dependendo do tipo de associação desejado. Para associação dinâmica, você pode usar o construtor de regras para selecionar opções para uma regra simples ou escrever uma regra de associação por conta própria. 

As etapas a seguir são um exemplo de alteração de um grupo de associação estática para dinâmica para um grupo de usuários.

1. Na página **Propriedades** do grupo selecionado, selecione um tipo de **Associação** de **usuário dinâmico**e, em seguida, selecione Sim na caixa de diálogo explicando as alterações na associação de grupo para continuar. 
  
   ![Selecionar tipo de associação de usuário dinâmico](./media/groups-change-type/select-group-to-convert.png)
  
2. Selecione **Adicionar consulta dinâmica**e, em seguida, forneça a regra.
  
   ![Insira a regra para o grupo dinâmico](./media/groups-change-type/enter-rule.png)
  
3. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da página.
4. Selecione **salvar** na página de **Propriedades** do grupo para salvar as alterações. O **tipo de associação** do grupo é atualizado imediatamente na lista de grupos.

> [!TIP]
> A conversão de grupo poderá falhar se a regra de associação que você inseriu estava incorreta. Uma notificação é exibida no canto superior direito do portal que contém uma explicação de por que a regra não pode ser aceita pelo sistema. Leia com atenção para entender como você pode ajustar a regra para torná-la válida. Para obter exemplos de sintaxe de regra e uma lista completa de propriedades, operadores e valores com suporte para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Alterar o tipo de associação de um grupo (PowerShell)

> [!NOTE]
> Para alterar as propriedades do grupo dinâmico, você precisará usar cmdlets da **versão prévia do PowerShell do** [Azure ad versão 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Você pode instalar a versão prévia do [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Aqui está um exemplo de funções que alternam o gerenciamento de associação em um grupo existente. Neste exemplo, é necessário ter cuidado para manipular corretamente a propriedade GroupTypes e preservar quaisquer valores que não estejam relacionados à associação dinâmica.

```powershell
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

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Para tornar um grupo dinâmico:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Passos seguintes

Esses artigos fornecem informações adicionais sobre grupos no Azure Active Directory.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
