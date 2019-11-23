---
title: Compreender o ciclo de vida de um esquema
description: Learn about the lifecycle that a blueprint definition goes through and details about each stage, including updating and removing blueprint assignments.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: 4dd5cb7d085744377cf12998f14c994fb1dcd2d7
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404580"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Understand the lifecycle of an Azure Blueprint

Like many resources within Azure, a blueprint in Azure Blueprints has a typical and natural lifecycle. They're created, deployed, and finally deleted when no longer needed or relevant.
Blueprints supports standard lifecycle operations. It then builds upon them to provide additional levels of status that support common continuous integration and continuous deployment pipelines for organizations that manage their Infrastructure as Code – a key element in DevOps.

To fully understand a blueprint and the stages, we'll cover a standard lifecycle:

> [!div class="checklist"]
> - Creating and editing a blueprint
> - Publishing the blueprint
> - Creating and editing a new version of the blueprint
> - Publishing a new version of the blueprint
> - Deleting a specific version of the blueprint
> - Deleting the blueprint

## <a name="creating-and-editing-a-blueprint"></a>Creating and editing a blueprint

When creating a blueprint, add artifacts to it, save to a management group or subscription, and provided a unique name and a unique version. The blueprint is now in a **Draft** mode and can't yet be assigned. While in the **Draft** mode, it can continue to be updated and changed.

A never published blueprint in **Draft** mode displays a different icon on the **Blueprint Definitions** page than ones that have been **Published**. The **Latest Version** is displayed as **Draft** for these never published blueprints.

Create and edit a blueprint with the [Azure portal](../create-blueprint-portal.md#create-a-blueprint) or [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publishing a blueprint

Once all planned changes have been made to a blueprint in **Draft** mode, it can be **Published** and made available for assignment. The **Published** version of the blueprint can't be altered. Once **Published**, the blueprint displays with a different icon than **Draft** blueprints and displays the provided version number in the **Latest Version** column.

Publish a blueprint with the [Azure portal](../create-blueprint-portal.md#publish-a-blueprint) or [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Creating and editing a new version of the blueprint

A **Published** version of a blueprint can't be altered. However, a new version of the blueprint can be added to the existing blueprint and modified as needed. Make changes to an existing blueprint by editing it. When the new changes are saved, the blueprint now has **Unpublished Changes**. These changes are a new **Draft** version of the blueprint.

Edit a blueprint with the [Azure portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publishing a new version of the blueprint

Each edited version of a blueprint must be **Published** before it can be assigned. When **Unpublished Changes** have been made to a blueprint but not **Published**, the **Publish Blueprint** button is available on the edit blueprint page. If the button isn't visible, the blueprint has already been **Published** and has no **Unpublished Changes**.

> [!NOTE]
> A single blueprint can have multiple **Published** versions that can each be assigned to subscriptions.

To publish a blueprint with **Unpublished Changes**, use the same steps for publishing a new blueprint.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Deleting a specific version of the blueprint

Each version of a blueprint is a unique object and can be individually **Published**. As such, each version of a blueprint can also be deleted. Deleting a version of a blueprint doesn't have any impact on other versions of that blueprint.

> [!NOTE]
> It's not possible to delete a blueprint that has active assignments. Delete the assignments first and then delete the version you wish to remove.

1. Select **All services** in the left pane. Search for and select **Blueprints**.

1. Select **Blueprint definitions** from the page on the left and use the filter options to locate the blueprint you want to delete a version of. Select it to open the edit page.

1. Select the **Published versions** tab and locate the version you wish to delete.

1. Right-click on the version to delete and select **Delete this version**.

## <a name="deleting-the-blueprint"></a>Deleting the blueprint

The core blueprint can also be deleted. Deleting the core blueprint also deletes any blueprint versions of that blueprint, including both **Draft** and **Published** blueprints. As with deleting a version of a blueprint, deleting the core blueprint doesn't remove the existing assignments of any of the blueprint versions.

> [!NOTE]
> It's not possible to delete a blueprint that has active assignments. Delete the assignments first and then delete the version you wish to remove.

Delete a blueprint with the [Azure portal](../create-blueprint-portal.md#delete-a-blueprint) or [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Atribuições

There's several points during the lifecycle a blueprint can be assigned to a subscription. When the mode of a version of the blueprint is **Published**, then that version can be assigned to a subscription. This lifecycle enables versions of a blueprint to be used and actively assigned while a newer version is being developed.

As versions of blueprints are assigned, it's important to understand where they're assigned and with what parameters they've been assigned with. The parameters can either be static or dynamic. To learn more, see [static and dynamic parameters](parameters.md).

### <a name="updating-assignments"></a>Updating assignments

When a blueprint is assigned, the assignment can be updated. There are several reasons for updating an existing assignment, including:

- Add or remove [resource locking](resource-locking.md)
- Change the value of [dynamic parameters](parameters.md#dynamic-parameters)
- Upgrade the assignment to a newer **Published** version of the blueprint

To learn how, see [update existing assignments](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Unassigning assignments

If the blueprint is no longer needed, it can be unassigned from the management group or subscription. During blueprint unassignment, the following occurs:

- Removal of [blueprint resource locking](resource-locking.md)
- Deletion of the blueprint assignment object
- (Conditional) If a **system-assigned managed identity** was used, it's also deleted

> [!NOTE]
> All resources deployed by the blueprint assignment remain in place, but are no longer protected by Azure Blueprints.

## <a name="next-steps"></a>Passos seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).