---
title: Conceda permissões ao utilizador para políticas específicas de laboratório [ Microsoft Docs
description: Aprenda a conceder permissões de utilizador a políticas específicas de laboratório em Laboratórios DevTest com base nas necessidades de cada utilizador
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284216"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões de utilizador a políticas específicas de laboratório
## <a name="overview"></a>Descrição geral
Este artigo ilustra como usar o PowerShell para conceder permissões aos utilizadores para uma determinada política de laboratório. Desta forma, as permissões podem ser aplicadas com base nas necessidades de cada utilizador. Por exemplo, pode querer conceder a um utilizador em particular a capacidade de alterar as definições de política vm, mas não as políticas de custos.

## <a name="policies-as-resources"></a>Políticas como recursos
Conforme discutido no artigo de Controlo de [Acesso baseado em Papel Azure,](../role-based-access-control/role-assignments-portal.md) o RBAC permite a gestão de recursos de acesso fino para o Azure. Utilizando o RBAC, pode segregar os deveres dentro da sua equipa de DevOps e conceder apenas a quantidade de acesso aos utilizadores de que necessitam para realizar em seu trabalho.

Nos Laboratórios DevTest, uma política é um tipo de recurso que permite a ação RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada política de laboratório é um recurso no tipo de recursos políticos, e pode ser atribuído como um âmbito para um papel RBAC.

Por exemplo, para conceder aos utilizadores a permissão de leitura/escrita para a política permitida de **tamanhos vm,** criaria uma função personalizada que funciona com o **Microsoft.DevTestLab/labs/policySets/policies/action,** e, em seguida, atribuir os utilizadores apropriados a este papel personalizado no âmbito da **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre papéis personalizados no RBAC, consulte o controlo de acesso de [funções personalizadas.](../role-based-access-control/custom-roles.md)

## <a name="creating-a-lab-custom-role-using-powershell"></a>Criar uma função personalizada de laboratório usando powerShell
Para começar, terá de instalar o [Azure PowerShell](/powershell/azure/install-az-ps). 

Depois de configurar os cmdlets Azure PowerShell, pode executar as seguintes tarefas:

* Enumerar todas as operações/ações para um fornecedor de recursos
* Enumeram as ações num determinado papel:
* Criar uma função personalizada

O seguinte guião powerShell ilustra exemplos de como executar estas tarefas:

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Atribuir permissões a um utilizador para uma política específica utilizando funções personalizadas
Depois de definir as suas funções personalizadas, pode atribuí-las aos utilizadores. Para atribuir uma função personalizada a um utilizador, tem primeiro de obter o **ObjectId** que representa esse utilizador. Para isso, utilize o cmdlet **Get-AzADUser.**

No exemplo seguinte, o **ObjectId** do utilizador *SomeUser* é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Uma vez que tenha o **ObjectId** para o utilizador e um nome de papel personalizado, pode atribuir essa função ao utilizador com o cmdlet **New-AzRoleAssignment:**

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

No exemplo anterior, a política **AllowedVmSizesInLab** é utilizada. Pode usar qualquer uma das seguintes polícias:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Uma vez que você concede permissões de utilizador para políticas específicas de laboratório, aqui estão alguns próximos passos a considerar:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Crie artefactos personalizados para os seus VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)

