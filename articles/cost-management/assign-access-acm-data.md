---
title: Assign access to Azure Cost Management data
description: This article walks you though assigning permission to Azure Cost Management data for various access scopes.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: e3140ee990127db6815828314103a09dff7cf26e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219853"
---
# <a name="assign-access-to-cost-management-data"></a>Assign access to Cost Management data

For users with Azure Enterprise agreements, a combination of permissions granted in the Azure portal and the Enterprise (EA) portal define a user's level of access to Azure Cost Management data. For users with other Azure account types, defining a user's level of access to Cost Management data is simpler by using Azure role-based access control. This article walks you through assigning access to Cost Management data. After the combination of permissions is assigned, the user views data in Cost Management based the scope that they have access to and on the scope that they select in the Azure portal.

The scope that a user selects is used throughout Cost Management to provide data consolidation and to control access to cost information. When using scopes, users don't multi-select them. Instead, they select a larger scope that child scopes roll up to and then they filter-down to what they want to view. Data consolidation is important to understand because some people shouldn't have access to a parent scope that child scopes roll up to.

Watch the [How to assign access with Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) video to learn about assigning access to view costs and charges with Azure role-based access control.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Cost Management scopes

Cost management supports a variety of Azure account types. To view the full list of supported account types, see [Understand Cost Management data](understand-cost-mgt-data.md). The type of account determines available scopes.

### <a name="azure-ea-subscription-scopes"></a>Azure EA subscription scopes

To view cost data for Azure EA subscriptions, a user must have at least read access to one or more of the following scopes.

| **Âmbito** | **Definido no** | **Required access to view data** | **Definição de pré-requisitos de EA** | **Consolidates data to** |
| --- | --- | --- | --- | --- |
| Conta de faturação<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrador da Empresa | Nenhuma | Todas as subscrições do contrato enterprise |
| Departamento | [https://ea.azure.com](https://ea.azure.com/) | Administrador do Departamento | **DA view charges** enabled | Todas as subscrições que pertencem a uma conta de inscrição que está associada ao departamento |
| Conta de inscrição<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Proprietário da Conta | **AO view charges** enabled | Todas as subscrições da conta de inscrição |
| Grupo de gestão | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Cost Management (ou Leitor) | **AO view charges** enabled | Todas as subscrições abaixo o grupo de gestão |
| Subscrição | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Cost Management (ou Leitor) | **AO view charges** enabled | Todos os recursos/grupos de recursos na subscrição |
| Grupo de recursos | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Cost Management (ou Leitor) | **AO view charges** enabled | Todos os recursos no grupo de recursos |

<sup>1</sup> The billing account is also referred to as the Enterprise Agreement or Enrollment.

<sup>2</sup> The enrollment account is also referred to as the account owner.

The following diagram illustrates the relationship between Cost Management scopes with roles and EA portal settings.

![Diagram showing the relationship between Cost Management scopes with roles and EA portal settings](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

When **DA view charges** are disabled in the EA portal, you’ll see a message stating *Costs disabled for your organization* when you try to view costs for departments and accounts.

Similarly, when **AO view charges** are disabled in the EA portal, you’ll see a message stating *Costs disabled for your organization* when you try to view costs for enrollment accounts, management groups, subscriptions, and resource groups.

## <a name="other-azure-account-scopes"></a>Other Azure account scopes

To view cost data for other Azure subscriptions, a user must have at least read access to one or more of the following scopes:

- Conta do Azure
- Grupo de gestão
- Grupo de recursos

Various scopes are available after partners onboard customers to a Microsoft Customer Agreement. CSP customers can then use Cost Management features when enabled by their CSP partner. For more information, see [Get started with Azure Cost Management for partners](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Enable access to costs in the EA portal

The department scope requires the **DA view charges** option **Enabled** in the EA portal. All other scopes require the **AO view charges** option **Enabled** in the EA portal.

To enable an option:

1. Sign in to the EA portal at [https://ea.azure.com](https://ea.azure.com) with an enterprise administrator account.
2. Select **Manage** in the left pane.
3. For the cost management scopes that you want to provide access to, enable the charge option to **DA view charges** and/or **AO view charges**.  
    ![Enrollment tab showing DA and AO view charges options](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

After the view charge options are enabled, most scopes also require role-based access control (RBAC) permission configuration in the Azure portal.

## <a name="enterprise-administrator-role"></a>Enterprise administrator role

By default, an enterprise administrator has access to the billing account (Enterprise Agreement/enrollment) and to all other scopes, which are child scopes. The enterprise administrator assigns access to scopes for other users. As a best practice for business continuity, you should always have two users with enterprise administrator access. The following sections are walk-through examples of the enterprise administrator assigning access to scopes for other users.

## <a name="assign-billing-account-scope-access"></a>Assign billing account scope access

Access to the billing account scope requires enterprise administrator permission in the EA portal. The enterprise administrator has access to view costs across the entire EA enrollment or multiple enrollments. No action is required in the Azure portal for the billing account scope.

1. Sign in to the EA portal at [https://ea.azure.com](https://ea.azure.com) with an enterprise administrator account.
2. Select **Manage** in the left pane.
3. On the **Enrollment** tab, select the enrollment that you want to manage.  
    ![select your enrollment in the EA portal](./media/assign-access-acm-data/ea-portal.png)
4. Click **+ Add Administrator**.
5. In the Add Administrator box, select the authentication type and type the user's email address.
6. If the user should have read-only access to cost and usage data, under **Read-only**, select **Yes**.  Otherwise, select **No**.
7. Click **Add** to create the account.  
    ![example information shown in the Add administrator box](./media/assign-access-acm-data/add-admin.png)

It may take up to 30 minutes before the new user can access data in Cost Management.

### <a name="assign-department-scope-access"></a>Assign department scope access

Access to the department scope requires department administrator (DA view charges) access in the EA portal. The department administrator has access to view costs and usage data associated with a department or to multiple departments. Data for the department includes all subscriptions belonging to an enrollment account that are linked to the department. No action is required in the Azure portal.

1. Sign in to the EA portal at [https://ea.azure.com](https://ea.azure.com) with an enterprise administrator account.
2. Select **Manage** in the left pane.
3. On the **Enrollment** tab, select the enrollment that you want to manage.
4. Click the **Department** tab and then click **Add Administrator**.
5. In the Add Department Administrator box, select the authentication type and then type the user's email address.
6. If the user should have read-only access to cost and usage data, under **Read-only**, select **Yes**.  Otherwise, select **No**.
7. Select the departments that you want to grant department administrative permission to.
8. Click **Add** to create the account.  
    ![enter required information in the Add department administrator box](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Assign enrollment account scope access

Access to the enrollment account scope requires account owner (AO view charges) access in the EA portal. The account owner can view costs and usage data associated with the subscriptions created from that enrollment account. No action is required in the Azure portal.

1. Sign in to the EA portal at [https://ea.azure.com](https://ea.azure.com) with an enterprise administrator account.
2. Select **Manage** in the left pane.
3. On the **Enrollment** tab, select the enrollment that you want to manage.
4. Click the **Account** tab and then click **Add Account**.
5. In the Add Account box, select the **Department** to associate the account to, or leave it as unassigned.
6. Select the authentication type and type the account name.
7. Type the user's email address and then optionally type the cost center.
8. Click on **Add** to create the account.  
    ![enter required information in the Add account box for an enrollment account](./media/assign-access-acm-data/add-account.png)

After completing the steps above, the user account becomes an enrollment account in the Enterprise portal and can create subscriptions. The user can access cost and usage data for subscriptions that they create.

## <a name="assign-management-group-scope-access"></a>Assign management group scope access

Access to view the management group scope requires at least the Cost Management Reader (or Reader) permission. You can configure permissions for a management group in the Azure portal. You must have at least the User Access Administrator (or Owner) permission for the management group to enable access for others. And for Azure EA accounts, you must also have enabled the **AO view charges** setting in the EA portal.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Select **All Services** in the sidebar, search for _management groups_, then select **management groups**.
3. Select the management group in the hierarchy.
4. Next to the name of your management group, click **Details**.
5. Select **Access Control (IAM)** from the left pane.
6. Clique em **Adicionar**.
7. Under **Role**, select **Cost Management Reader**.
8. Under **Assign access to**, select **Azure AD user, group, or application**.
9. To assign access, search for and then select the user.
10. Clique em **Guardar**.  
    ![example information in the Add permissions box for a management group](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Assign subscription scope access

Access to a subscription requires at least the Cost Management Reader (or Reader) permission. You can configure permissions to a subscription in the Azure portal. You must have at least the User Access Administrator (or Owner) permission for the subscription to enable access for others. And for Azure EA accounts, you must also have enabled the **AO view charges** setting in the EA portal.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Select **All Services** in the sidebar, search for _subscriptions_, then select **Subscriptions**.
3. Selecione a sua subscrição.
4. Select **Access Control (IAM)** from the left pane.
5. Clique em **Adicionar**.
6. Under **Role**, select **Cost Management Reader**.
7. Under **Assign access to**, select **Azure AD user, group, or application**.
8. To assign access, search for and then select the user.
9. Clique em **Guardar**.

## <a name="assign-resource-group-scope-access"></a>Assign resource group scope access

Access to a resource group requires at least the Cost Management Reader (or Reader) permission. You can configure permissions to a resource group in the Azure portal. You must have at least the User Access Administrator (or Owner) permission for the resource group to enable access for others. And for Azure EA accounts, you must also have enabled the **AO view charges** setting in the EA portal.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Select **All Services** in the sidebar, search for _resource groups_, then select **Resource groups**.
3. Select your resource group.
4. Select **Access Control (IAM)** from the left pane.
5. Clique em **Adicionar**.
6. Under **Role**, select **Cost Management Reader**.
7. Under **Assign access to**, select **Azure AD user, group, or application**.
8. To assign access, search for and then select the user.
9. Clique em **Guardar**.

## <a name="cross-tenant-authentication-issues"></a>Cross-tenant authentication issues

Currently, Azure Cost Management has limited support for cross-tenant authentication. In some circumstances when you try to authenticate across tenants, you may receive an **Access denied** error in cost analysis. This issue might occur if you configure role-based access control (RBAC) to another tenant's subscription and then try to view cost data.

*To work around the problem*: After you configure cross-tenant RBAC, wait an hour. Then, try to view costs in cost analysis or grant Cost Management access to users in both tenants.  


## <a name="next-steps"></a>Passos seguintes

- If you haven't already completed the first quickstart for Cost Management, read it at [Start analyzing costs](quick-acm-cost-analysis.md).
