---
title: Conceder permissões de usuário para políticas de laboratório específicas | Microsoft Docs
description: Saiba como conceder permissões de usuário para políticas de laboratório específicas no DevTest Labs com base nas necessidades de cada usuário
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
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028434"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões de usuário para políticas de laboratório específicas
## <a name="overview"></a>Descrição geral
Este artigo ilustra como usar o PowerShell para conceder permissões de usuários a uma determinada política de laboratório. Dessa forma, as permissões podem ser aplicadas com base nas necessidades de cada usuário. Por exemplo, talvez você queira conceder a um usuário específico a capacidade de alterar as configurações de política da VM, mas não as políticas de custo.

## <a name="policies-as-resources"></a>Políticas como recursos
Conforme discutido no artigo sobre o [controle de acesso baseado em função do Azure](../role-based-access-control/role-assignments-portal.md) , o RBAC permite o gerenciamento de acesso refinado de recursos para o Azure. Usando o RBAC, você pode separar as tarefas em sua equipe do DevOps e conceder apenas a quantidade de acesso aos usuários de que eles precisam para executar seus trabalhos.

No DevTest Labs, uma política é um tipo de recurso que habilita a ação RBAC **Microsoft. DevTestLab/Labs/policySets/Policies/** . Cada política de laboratório é um recurso no tipo de recurso de política e pode ser atribuída como um escopo a uma função de RBAC.

Por exemplo, para conceder aos usuários permissão de leitura/gravação para a política de **tamanhos de VM permitidos** , você deve criar uma função personalizada que funcione com a ação **Microsoft. DevTestLab/Labs/policySets/Policies/** e, em seguida, atribuir os usuários apropriados ao Essa função personalizada no escopo de **Microsoft. DevTestLab/Labs/policySets/Policies/AllowedVmSizesInLab**.

Para saber mais sobre as funções personalizadas no RBAC, consulte o [controle de acesso de funções personalizadas](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Criando uma função personalizada de laboratório usando o PowerShell
Para começar, você precisará [instalar o Azure PowerShell](/powershell/azure/install-az-ps). 

Depois de configurar os cmdlets Azure PowerShell, você poderá executar as seguintes tarefas:

* Listar todas as operações/ações de um provedor de recursos
* Listar ações em uma função específica:
* Criar uma função personalizada

O script do PowerShell a seguir ilustra exemplos de como executar essas tarefas:

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

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Atribuindo permissões a um usuário para uma política específica usando funções personalizadas
Depois de definir suas funções personalizadas, você pode atribuí-las aos usuários. Para atribuir uma função personalizada a um usuário, você deve primeiro obter o **ObjectID** que representa esse usuário. Para fazer isso, use o cmdlet **Get-AzADUser** .

No exemplo a seguir, o **ObjectID** do usuário *SomeUser* é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Depois de obter o **ObjectID** para o usuário e um nome de função personalizado, você pode atribuir essa função ao usuário com o cmdlet **New-AzRoleAssignment** :

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

No exemplo anterior, a política **AllowedVmSizesInLab** é usada. Você pode usar qualquer uma das seguintes políticas:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de conceder permissões de usuário para políticas de laboratório específicas, aqui estão algumas próximas etapas a serem consideradas:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Criar artefactos personalizados para as suas VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)

