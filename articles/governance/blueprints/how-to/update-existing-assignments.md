---
title: Update an existing assignment from the portal
description: Learn about the mechanism for updating an existing blueprint assignment from the portal in Azure Blueprints.
ms.date: 11/21/2019
ms.topic: conceptual
ms.openlocfilehash: b4cf03d88103b85bc00dbd815816ead2740f2093
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406385"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>How to update an existing blueprint assignment

When a blueprint is assigned, the assignment can be updated. There are several reasons for updating an existing assignment, including:

- Add or remove [resource locking](../concepts/resource-locking.md)
- Change the value of [dynamic parameters](../concepts/parameters.md#dynamic-parameters)
- Upgrade the assignment to a newer **Published** version of the blueprint

## <a name="updating-assignments"></a>Updating assignments

1. Select **All services** in the left pane. Search for and select **Blueprints**.

1. Select **Assigned blueprints** from the page on the left.

1. In the list of blueprints, left-click the blueprint assignment. Then click the **Update assignment** button OR right-click the blueprint assignment and select **Update assignment**.

   ![Update an existing blueprint assignment](../media/update-existing-assignments/update-assignment.png)

1. The **Assign blueprint** page will load pre-filled with all values from the original assignment.
   You can change the **blueprint definition version**, the **Lock Assignment** state, and any of the dynamic parameters that exist on the blueprint definition. Click **Assign** when done making changes.

1. On the updated assignment details page, see the new status. In this example, we added **Locking** to the assignment.

   ![Updated an existing blueprint assignment - lock mode changed](../media/update-existing-assignments/updated-assignment.png)

1. Explore details about other **Assignment operations** using the drop-down. The table of **Managed resources** updates by selected assignment operation.

   ![Assignment operations of a blueprint assignment](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Rules for updating assignments

The deployment of the updated assignments follows a few important rules. These rules determine what happens to already deployed resources. The requested change and the type of artifact resource being deployed or updated determine which actions are taken.

- Atribuições de Funções
  - If the role or the role assignee (user, group, or app) changes, a new role assignment is created. Role assignments previously deployed are left in place.
- Atribuições de Política
  - If the parameters of the policy assignment are changed, the existing assignment is updated.
  - If the definition of the policy assignment is changed, a new policy assignment is created.
    Policy assignments previously deployed are left in place.
  - If the policy assignment artifact is removed from the blueprint, deployed policy assignments are left in place.
- Modelos do Azure Resource Manager
  - The template is processed through Resource Manager as a **PUT**. As each resource type handles this action differently, review the documentation for each included resource to determine the impact of this action when run by Blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Possible errors on updating assignments

When updating assignments, it's possible to make changes that break when executed. An example is changing the location of a resource group after it has already been deployed. Any change that are supported by [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) can be made, but any change that would result in an error through Azure Resource Manager will also result in the failure of the assignment.

There's no limit on how many times an assignment can be updated. If an error occurs, determine the error and make another update to the assignment.  Example error scenarios:

- A bad parameter
- An already existing object
- A change not supported by Azure Resource Manager

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).