---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667864"
---
## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Limpar recursos utilizando o portal Azure

Eliminar os recursos no portal Azure seguindo os passos de limpeza dos [recursos.](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)

### <a name="clean-up-resources-using-powershell"></a>Limpar os recursos com o PowerShell

Se a Cloud Shell ainda estiver aberta, não precisa de copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```