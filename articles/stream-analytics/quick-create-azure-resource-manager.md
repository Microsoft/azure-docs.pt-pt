---
title: Quickstart - Crie um trabalho Azure Stream Analytics pelo modelo Azure Resource Manager
description: Este quickstart mostra como usar o modelo Azure Resource Manager para criar um trabalho Azure Stream Analytics.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/28/2020
ms.openlocfilehash: 0d7dc6e09ba3d045fe48b0e91faf59b50d828253
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84172543"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-the-azure-resource-manager-template"></a>Quickstart: Criar um trabalho Azure Stream Analytics utilizando o modelo de Gestor de Recursos Azure

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um trabalho Azure Stream Analytics. Uma vez criado o trabalho, valida-se a implantação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa:

* Tenha uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/).

## <a name="create-an-azure-stream-analytics-job"></a>Criar uma tarefa do Azure Stream Analytics

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste arranque rápido é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-streamanalytics-create).

:::code language="json" source="~/quickstart-templates/101-streamanalytics-create/azuredeploy.json" range="1-66":::

O recurso Azure definido no modelo é [Microsoft.StreamAnalytics/StreamingJobs:](https://docs.microsoft.com/azure/templates/microsoft.streamanalytics/2016-03-01/streamingjobs)criar um trabalho Azure Stream Analytics. 

## <a name="deploy-the-template"></a>Implementar o modelo

Nesta secção, cria-se um trabalho Azure Stream Analytics utilizando o modelo Azure Resource Manager.

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um trabalho Azure Stream Analytics.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

2. Forneça os valores necessários para criar o seu trabalho Azure Stream Analytics.

   ![Crie o trabalho Azure Stream Analytics usando um modelo de gestor de recursos Azure](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Crie o trabalho Azure Stream Analytics usando um modelo de gestor de recursos Azure")

   Forneça os seguintes valores:

   |Propriedade  |Descrição  |
   |---------|---------|
   |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
   |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md). |
   |**Região**     | Selecione **East US**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).        |
   |**Nome de trabalho de streaming analytics**     | Forneça um nome para o seu trabalho stream Analytics.      |
   |**Número de unidades de streaming**     |  Escolha o número de unidades de streaming de que necessita. Para obter mais informações, consulte [Compreender e ajustar Unidades de Streaming.](stream-analytics-streaming-unit-consumption.md)       |

3. Selecione **Rever + Criar** e, em seguida, clique em **Criar**.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar o trabalho Azure Stream Analytics ou utilizar o seguinte script Azure CLI ou Azure PowerShell para listar o recurso.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender continuar a ter tutoriais subsequentes, poderá querer deixar estes recursos no lugar. Quando já não for necessário, elimine o grupo de recursos, que elimina o trabalho Azure Stream Analytics. Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell:

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Próximos passos

Neste quickstart, criou um trabalho Azure Stream Analytics usando um modelo de Gestor de Recursos Azure e validou a implementação. Avance para o próximo artigo para aprender a exportar um modelo de Gestor de Recursos Azure para um trabalho existente usando o Código VS.

> [!div class="nextstepaction"]
> [Exporte um azure stream analytics job Azure Resource Manager modelo](resource-manager-export.md)
