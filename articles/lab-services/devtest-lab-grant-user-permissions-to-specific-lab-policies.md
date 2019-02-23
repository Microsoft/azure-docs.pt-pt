---
title: Conceder permissões de utilizador para políticas de laboratório específico | Documentos da Microsoft
description: Saiba como pode conceder permissões de utilizador para políticas específicas de laboratório no DevTest Labs com base nas necessidades de cada utilizador
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 70469a9e8737a9df18628951a061c97081c74080
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735106"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões de utilizador para políticas de laboratório específico
## <a name="overview"></a>Descrição geral
Este artigo ilustra como utilizar o PowerShell para conceder permissões de utilizadores a uma política de específica de laboratório. Dessa forma, as permissões podem ser aplicadas com base nas necessidades de cada utilizador. Por exemplo, poderá conceder a capacidade de alterar as definições de política VM, mas não as políticas de custo de um utilizador específico.

## <a name="policies-as-resources"></a>Políticas de recursos
Como explicado no [controlo de acesso baseado em funções do Azure](../role-based-access-control/role-assignments-portal.md) artigo, o RBAC permite a gestão de acesso detalhado dos recursos do Azure. Utilizando o RBAC, pode segregar funções na sua equipa de DevOps e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções.

No DevTest Labs, uma política é um tipo de recurso que permite a ação de RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada política de laboratório é um recurso no tipo de recurso de política e pode ser atribuída como um âmbito a uma função RBAC.

Por exemplo, para conceder permissão de leitura/escrita de utilizadores para o **tamanhos de VM permitidos** diretiva, criaria uma função personalizada que funciona com o **Microsoft.DevTestLab/labs/policySets/policies/** ação e, em seguida, atribuir os usuários adequados para esta função personalizada no âmbito da **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre as funções personalizadas no RBAC, veja a [controlo de acesso a funções personalizadas](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Criar uma função personalizada de laboratório com o PowerShell
Para começar, precisará [instalar o Azure PowerShell](/powershell/azure/install-az-ps). 

Depois de ter configurado os cmdlets do PowerShell do Azure, pode efetuar as seguintes tarefas:

* Listar todas as operações/ações para um fornecedor de recursos
* Ações de lista numa função específica:
* Criar uma função personalizada

O script do PowerShell seguinte ilustra os exemplos de como realizar estas tarefas:

    ‘List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>A atribuir permissões a um utilizador para uma política específica com funções personalizadas
Depois de definir suas funções personalizadas, pode atribuí-las aos utilizadores. Para atribuir uma função personalizada a um utilizador, tem de obter primeiro o **ObjectId** que representa esse usuário. Para tal, utilize o **Get-AzADUser** cmdlet.

No exemplo a seguir, o **ObjectId** da *SomeUser* utilizador é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Assim que tiver o **ObjectId** para o usuário e um nome de função personalizada, pode atribuir essa função ao utilizador com o **New-AzRoleAssignment** cmdlet:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

No exemplo anterior, o **AllowedVmSizesInLab** política é utilizada. Pode utilizar qualquer um dos seguintes políticas:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
Uma vez que conceder permissões de utilizador para políticas de laboratório específico, aqui estão alguns passos a ter em consideração:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Criar artefactos personalizados para as suas VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)

