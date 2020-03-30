---
title: Mude a adesão ao grupo estático para dinâmica - Azure AD / Microsoft Docs
description: Como criar regras de adesão para povoar automaticamente grupos, e uma referência de regras.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027319"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Mude a adesão ao grupo estático para dinâmica no Diretório Ativo azure

Pode alterar a adesão de um grupo de estática a dinâmica (ou vice-versa) no Azure Ative Directory (Azure AD). A Azure AD mantém o mesmo nome de grupo e id no sistema, pelo que todas as referências existentes ao grupo ainda são válidas. Se criar um novo grupo, terá de atualizar essas referências. A adesão ao grupo dinâmico elimina a gestão de acréscimo supérbio e remoção de utilizadores. Este artigo diz-lhe como converter grupos existentes de adesão estática a membros dinâmicos usando o centro de administração Azure AD ou cmdlets PowerShell.

> [!WARNING]
> Ao mudar um grupo estático existente para um grupo dinâmico, todos os membros existentes são removidos do grupo, e então a regra de adesão é processada para adicionar novos membros. Se o grupo for utilizado para controlar o acesso a apps ou recursos, esteja ciente de que os membros originais podem perder acesso até que a regra de adesão seja totalmente processada.
>
> Recomendamos que teste previamente a nova regra de adesão para garantir que a nova adesão ao grupo seja como esperado.

## <a name="change-the-membership-type-for-a-group"></a>Alterar o tipo de membro para um grupo

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta que é administradora global ou administradora de utilizadores no seu inquilino.
2. Selecione **Grupos**.
3. Da lista de **todos os grupos,** abra o grupo que quer mudar.
4. Selecione **Propriedades**.
5. Na página **Propriedades** do grupo, selecione um tipo de **Membro** de Ambos Designados (estáticos), Utilizador Dinâmico ou Dispositivo Dinâmico, dependendo do tipo de membro pretendido. Para uma adesão dinâmica, você pode usar o construtor de regras para selecionar opções para uma regra simples ou escrever uma regra de adesão por si mesmo. 

Os seguintes passos são um exemplo de mudança de grupo de adesão estática para dinâmica para um grupo de utilizadores.

1. Na página **Propriedades** para o seu grupo selecionado, selecione um tipo de **Utilizador Dinâmico**de **Membro,** em seguida, selecione Sim no diálogo explicando as alterações à adesão ao grupo para continuar. 
  
   ![selecionar tipo de membro do utilizador dinâmico](./media/groups-change-type/select-group-to-convert.png)
  
2. Selecione **Adicionar consulta dinâmica**e, em seguida, fornecer a regra.
  
   ![entrar na regra para o grupo dinâmico](./media/groups-change-type/enter-rule.png)
  
3. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da página.
4. Selecione **Guardar** na página **Propriedades** para o grupo para guardar as suas alterações. O tipo de **membro** do grupo é imediatamente atualizado na lista de grupos.

> [!TIP]
> A conversão do grupo pode falhar se a regra de adesão que inscreveu estiver errada. Uma notificação é exibida no canto superior direito do portal que contém uma explicação do porquê da regra não poder ser aceite pelo sistema. Leia-o atentamente para entender como pode ajustar a regra para torná-la válida. Por exemplo, a sintaxe de regras e uma lista completa dos imóveis, operadores e valores suportados para uma regra de adesão, consulte regras de [adesão dinâmicas para grupos no Diretório Ativo Azure.](groups-dynamic-membership.md)

## <a name="change-membership-type-for-a-group-powershell"></a>Alterar o tipo de membro para um grupo (PowerShell)

> [!NOTE]
> Para alterar as propriedades dinâmicas do grupo, terá de utilizar cmdlets a partir **da versão de pré-visualização do** [Azure AD PowerShell Version 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Pode instalar a pré-visualização a partir da [Galeria PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Aqui está um exemplo de funções que mudam a gestão da adesão num grupo existente. Neste exemplo, é tomada a tenção para manipular corretamente a propriedade do GroupTypes e preservar quaisquer valores que não estejam relacionados com a adesão dinâmica.

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
Para fazer uma estática de grupo:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Para fazer uma dinâmica de grupo:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre grupos no Diretório Ativo Azure.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
