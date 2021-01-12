---
title: 'Quickstart: Criar uma consulta partilhada com a Azure PowerShell'
description: Neste arranque rápido, você segue os passos para criar uma consulta partilhada de Gráfico de Recurso usando Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d7efc02cad3aaa67c639a319f1a7bb455d6e04b0
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128097"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Quickstart: Criar uma consulta partilhada de gráficos de recurso usando Azure PowerShell

Este artigo descreve como pode criar uma consulta partilhada Azure Resource Graph utilizando o módulo [Az.ResourceGraph](/powershell/module/az.resourcegraph) PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Enquanto o módulo **Az.ResourceGraph** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Criar uma consulta partilhada de gráficos de recurso

Com o `Az.ResourceGraph` módulo PowerShell adicionado ao seu ambiente de eleição, é hora de criar uma consulta partilhada de Gráfico de Recurso. A consulta partilhada é um objeto Azure Resource Manager que pode conceder permissão ou executar no Azure Resource Graph Explorer. A consulta resume a contagem de todos os recursos agrupados por _localização._

1. Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para armazenar a consulta partilhada do Azure Resource Graph. Este grupo de recursos foi nomeado `resource-graph-queries` e a localização é `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Crie a consulta partilhada Azure Resource Graph utilizando o `Az.ResourceGraph` módulo PowerShell e o cmdlet [New-AzResourceGraphQuery:](/powershell/module/az.resourcegraph/new-azresourcegraphquery)

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Listar as consultas partilhadas no novo grupo de recursos. O [cmdlet Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) devolve uma matriz de valores.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Para obter apenas um único resultado de consulta partilhada, use `Get-AzResourceGraphQuery` com o seu `Name` parâmetro.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Limpar recursos

Se desejar remover a consulta partilhada do Gráfico de Recurso e o grupo de recursos do seu ambiente Azure, pode fazê-lo utilizando os seguintes comandos:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/cli/azure/group#az_group_delete)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma consulta partilhada de Gráfico de Recurso utilizando a Azure PowerShell. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)