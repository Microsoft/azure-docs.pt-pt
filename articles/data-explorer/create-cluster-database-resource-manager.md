---
title: Crie um cluster e base de dados do Azure Data Explorer utilizando um modelo de Gestor de Recursos Azure
description: Aprenda a criar um cluster e base de dados do Azure Data Explorer utilizando um modelo de Gestor de Recursos Azure
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911970"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Crie um cluster e base de dados do Azure Data Explorer utilizando um modelo de Gestor de Recursos Azure

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Pitão](create-cluster-database-python.md)
> * [Modelo Azure Resource Manager](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Para utilizar o Azure Data Explorer, primeiro crie um cluster e crie uma ou mais bases de dados nesse cluster. Em seguida, ingira (carregue) os dados para uma base de dados, de modo a poder executar consultas neles. 

Neste artigo, cria-se um cluster e base de dados do Azure Data Explorer utilizando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/management/overview.md). O artigo mostra como definir quais os recursos que são implantados e como definir parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter informações sobre a criação de modelos, consulte [os modelos de gestor de recursos azure](/azure/azure-resource-manager/resource-group-authoring-templates). Para que a sintaxe jSON e as propriedades utilizem num modelo, consulte os tipos de [recursos Microsoft.Kusto](/azure/templates/microsoft.kusto/allversions).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Modelo de Gestor de Recursos Azure para criação de cluster e base de dados

Neste artigo, você usa um [modelo de quickstart existente](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Para encontrar mais amostras de modelo, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Implementar o modelo e verificar a implementação do modelo

Pode implantar o modelo do Gestor de Recursos Azure [utilizando o portal Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) ou [utilizando powershell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Use o portal Azure para implementar o modelo e verificar a implementação do modelo

1. Para criar um cluster e base de dados, utilize o seguinte botão para iniciar a implementação. Clique à direita e selecione **Abra em nova janela,** para que possa seguir o resto dos passos neste artigo.

    [![Desdobre para Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    O botão **Implementar no Azure** leva-o para o portal do Azure para preencher um formulário de implementação.

    ![Implementar no Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Pode [editar e implantar o modelo no portal Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) utilizando o formulário.

1. Complete as secções **BASICS** e **SETTINGS.** Selecione nomes únicos de cluster e base de dados.
Leva alguns minutos para criar um cluster azure Data Explorer e base de dados.

1. Para verificar a implementação, abre o grupo de recursos no [portal Azure](https://portal.azure.com) para encontrar o seu novo cluster e base de dados. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Use powershell para implementar o modelo e verificar a implementação do modelo

#### <a name="deploy-the-template-using-powershell"></a>Desdobrar o modelo usando a powershell

1. Selecione **Experimente** a partir do seguinte bloco de código e, em seguida, siga as instruções para iniciar sessão na concha Azure Cloud.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Selecione **Copiar** para copiar o script PowerShell.
1. Clique na consola da concha e, em seguida, selecione **Paste**.
Leva alguns minutos para criar um cluster azure Data Explorer e base de dados.

#### <a name="verify-the-deployment-using-powershell"></a>Verifique a implementação utilizando o PowerShell

Para verificar a implementação, utilize o seguinte script Azure PowerShell.  Se a Cloud Shell ainda estiver aberta, não precisa de copiar/executar a primeira linha (Read-Host). Para obter mais informações sobre a gestão dos recursos do Azure Data Explorer na PowerShell, leia [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

[Ingerir dados no cluster e base de dados do Azure Data Explorer](ingest-data-overview.md)
