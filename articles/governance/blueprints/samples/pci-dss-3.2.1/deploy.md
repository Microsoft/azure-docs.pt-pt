---
title: PCI-DSS v3.2.1 blueprint sample - Deploy steps
description: Deploy steps for the Payment Card Industry Data Security Standard v3.2.1 blueprint sample including blueprint artifact parameter details.
ms.date: 06/24/2019
ms.topic: sample
ms.openlocfilehash: f8759a1686a89f4e3f70e1876f661805e5dbda39
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404008"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Deploy the PCI-DSS v3.2.1 blueprint sample

To deploy the Azure Blueprints PCI-DSS v3.2.1 blueprint sample, the following steps must be taken:

> [!div class="checklist"]
> - Create a new blueprint from the sample
> - Mark your copy of the sample as **Published**
> - Assign your copy of the blueprint to an existing subscription

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Create blueprint from sample

First, implement the blueprint sample by creating a new blueprint in your environment using the sample as a starter.

1. Select **All services** in the left pane. Search for and select **Blueprints**.

1. From the **Getting started** page on the left, select the **Create** button under _Create a blueprint_.

1. Find the **PCI-DSS v3.2.1** blueprint sample under _Other Samples_ and select **Use this sample**.

1. Enter the _Basics_ of the blueprint sample:

   - **Blueprint name**: Provide a name for your copy of the PCI-DSS v3.2.1 blueprint sample.
   - **Definition location**: Use the ellipsis and select the management group to save your copy of the sample to.

1. Select the _Artifacts_ tab at the top of the page or **Next: Artifacts** at the bottom of the page.

1. Review the list of artifacts that make up the blueprint sample. Many of the artifacts have parameters that we'll define later. Select **Save Draft** when you've finished reviewing the blueprint sample.

## <a name="publish-the-sample-copy"></a>Publish the sample copy

Your copy of the blueprint sample has now been created in your environment. It's created in **Draft** mode and must be **Published** before it can be assigned and deployed. The copy of the blueprint sample can be customized to your environment and needs, but that modification may move it away from the PCI-DSS v3.2.1 standard.

1. Select **All services** in the left pane. Search for and select **Blueprints**.

1. Select the **Blueprint definitions** page on the left. Use the filters to find your copy of the blueprint sample and then select it.

1. Select **Publish blueprint** at the top of the page. In the new page on the right, provide a **Version** for your copy of the blueprint sample. This property is useful for if you make a modification later. Provide **Change notes** such as "First version published from the PCI-DSS v3.2.1 blueprint sample." Then select **Publish** at the bottom of the page.

## <a name="assign-the-sample-copy"></a>Assign the sample copy

Once the copy of the blueprint sample has been successfully **Published**, it can be assigned to a subscription within the management group it was saved to. This step is where parameters are provided to make each deployment of the copy of the blueprint sample unique.

1. Select **All services** in the left pane. Search for and select **Blueprints**.

1. Select the **Blueprint definitions** page on the left. Use the filters to find your copy of the blueprint sample and then select it.

1. Select **Assign blueprint** at the top of the blueprint definition page.

1. Provide the parameter values for the blueprint assignment:

   - Noções básicas

     - **Subscriptions**: Select one or more of the subscriptions that are in the management group you saved your copy of the blueprint sample to. If you select more than one subscription, an assignment will be created for each using the parameters entered.
     - **Assignment name**: The name is pre-populated for you based on the name of the blueprint.
       Change as needed or leave as is.
     - **Location**: Select a region for the managed identity to be created in. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Blueprint definition version**: Pick a **Published** version of your copy of the blueprint sample.

   - Lock Assignment

     Select the blueprint lock setting for your environment. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Leave the default _system assigned_ managed identity option.

   - Artifact parameters

     The parameters defined in this section apply to the artifact under which it's defined. These parameters are [dynamic parameters](../../concepts/parameters.md#dynamic-parameters) since they're defined during the assignment of the blueprint. For a full list or artifact parameters and their descriptions, see [Artifact parameters table](#artifact-parameters-table).

1. Once all parameters have been entered, select **Assign** at the bottom of the page. The blueprint assignment is created and artifact deployment begins. Deployment takes roughly an hour. To check on the status of deployment, open the blueprint assignment.

> [!WARNING]
> The Azure Blueprints service and the built-in blueprint samples are **free of cost**. Azure resources are [priced by product](https://azure.microsoft.com/pricing/). Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate the cost of running resources deployed by this blueprint sample.

## <a name="artifact-parameters-table"></a>Artifact parameters table

The following table provides a list of the blueprint artifact parameters:

|Artifact name|Artifact type|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Preview\] Audit PCI v3.2.1:2018 controls and deploy specific VM Extensions to support audit requirements|Atribuição de Política|List of Resource Types | Audit diagnostic setting for selected resource types. Default value is all resources are selected| 
|Localizações permitidas|Atribuição de Política|List Of Allowed Locations|List of data center locations allowed for any resource to be deployed into. This list is customizable to the desired Azure locations globally. Select locations you wish to allow.| 
|Allowed Locations for resource groups|Atribuição de Política |Allowed Location |This policy enables you to restrict the locations your organization can create resource groups in. Utilize para impor os requisitos de conformidade geográfica.| 
|Deploy Auditing on SQL servers|Atribuição de Política|Retention days|Data retention in number of days. Default value is 180 but PCI requires 365.| 
|Deploy Auditing on SQL servers|Atribuição de Política|Resource group name for storage account|Auditing writes database events to an audit log in your Azure Storage account (a storage account will be created in each region where a SQL Server is created that will be shared by all servers in that region).| 

## <a name="next-steps"></a>Passos seguintes

Now that you've reviewed the steps to deploy the PCI-DSS v3.2.1 blueprint sample, visit the following articles to learn about the overview and control mapping:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 blueprint - Overview](./index.md)
> [PCI-DSS v3.2.1 blueprint - Control mapping](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
