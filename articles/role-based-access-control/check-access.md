---
title: Quickstart - View the access a user has to Azure resources
description: In this QuickStart, learn how to view the access a user or other security principal has to Azure resources using role-based access control (RBAC) and the Azure portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419613"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Quickstart: View the access a user has to Azure resources

You can use the **Access control (IAM)** blade in [role-based access control (RBAC)](overview.md) to view the access a user or another security principal has to Azure resources. However, sometimes you just need to quickly view the access for a single user or another security principal. The easiest way to do this is to use the **Check access** feature in the Azure portal.

## <a name="view-role-assignments"></a>View role assignments

 The way that you view the access for a user is to list their roles assignments. Follow these steps to view the role assignments for a single user, group, service principal, or managed identity at the subscription scope.

1. In the Azure portal, click **All services** and then **Subscriptions**.

1. Click your subscription.

1. Clique em **Controlo de acesso (IAM)** .

1. Click the **Check access** tab.

    ![Access control - Check access tab](./media/check-access/access-control-check-access.png)

1. In the **Find** list, select the type of security principal you want to check access for.

1. In the search box, enter a string to search the directory for display names, email addresses, or object identifiers.

    ![Check access select list](./media/check-access/check-access-select.png)

1. Click the security principal to open the **assignments** pane.

    ![assignments pane](./media/check-access/check-access-assignments.png)

    On this pane, you can see the roles assigned to the selected security principal and the scope. If there are any deny assignments at this scope or inherited to this scope, they will be listed.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Grant a user access to Azure resources using RBAC and the Azure portal](quickstart-assign-role-user-portal.md)
