---
title: Conceder permissões de utilizadores a políticas específicas de laboratório Microsoft Docs
description: Saiba como conceder permissões de utilizadores a políticas específicas de laboratório em Laboratórios DevTest com base nas necessidades de cada utilizador
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 976862476d25e4e9a4933d8a5319eec9d77ca39b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328475"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões de utilizadores a políticas específicas de laboratório
## <a name="overview"></a>Descrição geral
Este artigo ilustra como usar o PowerShell para conceder aos utilizadores permissões a uma determinada política de laboratório. Desta forma, as permissões podem ser aplicadas com base nas necessidades de cada utilizador. Por exemplo, pode querer conceder a um determinado utilizador a capacidade de alterar as definições de política de VM, mas não as políticas de custos.

## <a name="policies-as-resources"></a>Políticas como recursos
Como discutido no artigo do [Azure role-based access control (Azure RBAC),](../role-based-access-control/role-assignments-portal.md) o Azure RBAC permite uma gestão de recursos de acesso fino para o Azure. Utilizando o Azure RBAC, pode segregar os deveres dentro da sua equipa de DevOps e conceder apenas a quantidade de acesso aos utilizadores de que necessitam para desempenharem os seus trabalhos.

Na DevTest Labs, uma política é um tipo de recurso que permite a ação Azure RBAC **Microsoft.DevTestLab/labs/policySets/policiesSets/**. Cada política de laboratório é um recurso no tipo de recursos da Política, e pode ser atribuída como um âmbito para um papel de Azure.

Por exemplo, para conceder aos utilizadores a leitura/escrita da política **de tamanhos VM permitidos,** criaria uma função personalizada que funcionasse com o **Microsoft.DevTestLab/labs/policySets/policiesSets/policies/** action e, em seguida, atribuir os utilizadores adequados a este papel personalizado no âmbito do **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre papéis personalizados no Azure RBAC, consulte os [papéis personalizados Azure](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Criar um papel personalizado de laboratório usando o PowerShell
Para começar, terá de instalar o [Azure PowerShell](/powershell/azure/install-az-ps). 

Depois de configurar os cmdlets Azure PowerShell, pode executar as seguintes tarefas:

* Listar todas as operações/ações para um fornecedor de recursos
* Enumerar ações num papel específico:
* Criar uma função personalizada

O seguinte script PowerShell ilustra exemplos de como executar estas tarefas:

```azurepowershell
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
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Atribuir permissões a um utilizador para uma política específica usando funções personalizadas
Uma vez definidas as suas funções personalizadas, pode atribuí-las aos utilizadores. Para atribuir uma função personalizada a um utilizador, tem primeiro de obter o **ObjectId** que representa esse utilizador. Para isso, utilize o **cmdlet Get-AzADUser.**

No exemplo seguinte, o **ObjectId** do utilizador *SomeUser* é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

Uma vez que tenha o **ObjectId** para o utilizador e um nome de função personalizado, pode atribuir essa função ao utilizador com o cmdlet **New-AzRoleAssignment:**

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

No exemplo anterior, é utilizada a política **AllowedVmSizesInLab.** Pode utilizar qualquer uma das seguintes polícias:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* PermitidoVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter concedido permissões de utilizadores a políticas específicas de laboratório, eis alguns próximos passos a considerar:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Crie artefactos personalizados para os seus VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)
