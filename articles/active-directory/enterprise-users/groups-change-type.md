---
title: Alterar a adesão do grupo estático à dinâmica - Azure AD | Microsoft Docs
description: Aprenda a converter grupos existentes de membros estáticos a dinâmicos utilizando o centro de administração Admin Azure ou os cmdlets PowerShell.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a46cc3f4a0f2fb25fc693103a64a319dcec0324
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860886"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Alterar a adesão do grupo estático à dinâmica no Azure Ative Directory

Pode alterar a adesão de um grupo de estática para dinâmica (ou vice-versa) no Azure Ative Directory (Azure AD). A Azure AD mantém o mesmo nome de grupo e ID no sistema, pelo que todas as referências existentes ao grupo ainda são válidas. Se em vez disso criar um novo grupo, terá de atualizar essas referências. A adesão ao grupo dinâmico elimina a gestão de despesas adicionando e removendo os utilizadores. Este artigo diz-lhe como converter grupos existentes de estática para membro dinâmico usando o centro de administração Admin Azure ou os cmdlets PowerShell.

> [!WARNING]
> Ao mudar um grupo estático existente para um grupo dinâmico, todos os membros existentes são removidos do grupo, e então a regra de adesão é processada para adicionar novos membros. Se o grupo for utilizado para controlar o acesso a apps ou recursos, esteja ciente de que os membros originais podem perder o acesso até que a regra da adesão seja totalmente processada.
>
> Recomendamos que teste previamente a nova regra de adesão para garantir que a nova adesão ao grupo seja como esperado.

## <a name="change-the-membership-type-for-a-group"></a>Alterar o tipo de membro para um grupo

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta que é um administrador global, administrador de utilizadores ou administrador de grupos na sua organização Azure AD.
2. Selecione **Grupos**.
3. Da lista **de todos os grupos,** abra o grupo que pretende alterar.
4. Selecione **Propriedades**.
5. Na página **Propriedades** para o grupo, selecione um tipo de **Membro** de Atribuído (estático), Utilizador Dinâmico ou Dispositivo Dinâmico, dependendo do tipo de membro pretendido. Para uma adesão dinâmica, pode utilizar o construtor de regras para selecionar opções para uma regra simples ou escrever uma regra de adesão. 

Os passos seguintes são um exemplo de mudança de grupo de estática para membro dinâmico para um grupo de utilizadores.

1. Na página **Propriedades** para o seu grupo selecionado, selecione um **tipo** de Membro de **Utilizador Dinâmico,** em seguida, selecione Sim no diálogo explicando as alterações à adesão ao grupo para continuar. 
  
   ![selecionar tipo de membro de utilizador dinâmico](./media/groups-change-type/select-group-to-convert.png)
  
2. **Selecione Adicionar consulta dinâmica** e, em seguida, fornecer a regra.
  
   ![entrar na regra para o grupo dinâmico](./media/groups-change-type/enter-rule.png)
  
3. Depois de criar a regra, **selecione Adicionar consulta** na parte inferior da página.
4. **Selecione Guardar** na página **Propriedades** para que o grupo guarde as suas alterações. O tipo de **membro** do grupo é imediatamente atualizado na lista de grupos.

> [!TIP]
> A conversão do grupo pode falhar se a regra de adesão que inseriu estiver incorreta. Uma notificação é apresentada no canto superior direito do portal que contém uma explicação do porquê da regra não pode ser aceite pelo sistema. Leia-o atentamente para entender como pode ajustar a regra para torná-la válida. Por exemplo, sintaxe de regras e uma lista completa das propriedades, operadores e valores suportados para uma regra de adesão, consulte [regras de adesão dinâmica para grupos no Azure Ative Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Alterar o tipo de membro para um grupo (PowerShell)

> [!NOTE]
> Para alterar propriedades dinâmicas do grupo, terá de utilizar cmdlets a partir da **versão de pré-visualização da** Versão 2 do [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). Pode instalar a pré-visualização na [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview).

Aqui está um exemplo de funções que mudam a gestão de membros num grupo existente. Neste exemplo, é tomado cuidado para manipular corretamente a propriedade GroupTypes e preservar quaisquer valores que não estejam relacionados com a adesão dinâmica.

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
Para fazer um grupo estático:

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
