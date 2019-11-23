---
title: Resolver erros comuns
description: Learn how to troubleshoot issues creating, assigning, and removing blueprints such as policy violations and blueprint parameter functions.
ms.date: 11/22/2019
ms.topic: troubleshooting
ms.openlocfilehash: 4e7ea1760e000a167c4329d6f12f3acc18d18f7c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406616"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Troubleshoot errors using Azure Blueprints

You may run into errors when creating or assigning blueprints. This article describes various errors that may occur and how to resolve them.

## <a name="finding-error-details"></a>Finding error details

Many errors will be the result of assigning a blueprint to a scope. When an assignment fails, the blueprint provides details about the failed deployment. This information indicates the issue so that it can be fixed and the next deployment succeeds.

1. Select **All services** in the left pane. Search for and select **Blueprints**.

1. Select **Assigned blueprints** from the page on the left and use the search box to filter the blueprint assignments to find the failed assignment. You can also sort the table of assignments by the **Provisioning State** column to see all failed assignments grouped together.

1. Left-click on the blueprint with the _Failed_ status or right-click and select **View assignment details**.

1. A red banner warning that the assignment has failed is at the top of the blueprint assignment page. Click anywhere on the banner to get more details.

It's common for the error to be caused by an artifact and not the blueprint as a whole. If an artifact creates a Key Vault and Azure Policy prevents Key Vault creation, the entire assignment will fail.

## <a name="general-errors"></a>General errors

### <a name="policy-violation"></a>Scenario: Policy Violation

#### <a name="issue"></a>Problema

The template deployment failed because of policy violation.

#### <a name="cause"></a>Causa

A policy may conflict with the deployment for a number of reasons:

- The resource being created is restricted by policy (commonly SKU or location restrictions)
- The deployment is setting fields that are configured by policy (common with tags)

#### <a name="resolution"></a>Resolução

Change the blueprint so it doesn't conflict with the policies in the error details. If this change isn't possible, an alternative option is to have the scope of the policy assignment changed so the blueprint is no longer in conflict with the policy.

### <a name="escape-function-parameter"></a>Scenario: Blueprint parameter is a function

#### <a name="issue"></a>Problema

Blueprint parameters that are functions are processed before being passed to artifacts.

#### <a name="cause"></a>Causa

Passing a blueprint parameter that uses a function, such as `[resourceGroup().tags.myTag]`, to an artifact results in the processed outcome of the function being set on the artifact instead of the dynamic function.

#### <a name="resolution"></a>Resolução

To pass a function through as a parameter, escape the entire string with `[` such that the blueprint parameter looks like `[[resourceGroup().tags.myTag]`. The escape character causes Blueprints to treat the value as a string when processing the blueprint. Blueprints then places the function on the artifact allowing it to be dynamic as expected. For more information, see [Syntax and expressions in Azure Resource Manager templates](../../../azure-resource-manager/template-expressions.md).

## <a name="next-steps"></a>Passos seguintes

If you didn't see your problem or are unable to solve your issue, visit one of the following channels for more support:

- Get answers from Azure experts through [Azure Forums](https://azure.microsoft.com/support/forums/).
- Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
- If you need more help, you can file an Azure support incident. Go to the [Azure support site](https://azure.microsoft.com/support/options/) and select **Get Support**.