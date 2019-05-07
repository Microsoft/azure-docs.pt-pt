---
title: Script do Azure PowerShell - eliminar uma base de dados numa conta do Cosmos do Azure
description: Exemplo do script do Azure PowerShell - eliminar uma base de dados numa conta do Cosmos do Azure
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: a8ecdb7456b13b21f814a3c2eda924aa9b3f48fa
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077729"
---
# <a name="delete-a-database-in-an-azure-cosmos-account-in-an-azure-cosmos-account-using-powershell"></a>Eliminar uma base de dados numa conta do Cosmos do Azure numa conta do Cosmos do Azure com o PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-database-delete.ps1 "Delete a database in an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|**Recursos do Azure**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Cria um recurso. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Obtém um recurso. |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Atualiza um recurso. |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Invoca uma ação num recurso. |
| [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource) | Elimina um recurso. |
|**Grupos de recursos do Azure**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Pode ver exemplos do script do Azure PowerShell Cosmos DB adicionais nos [scripts do PowerShell do Azure Cosmos DB](../../../powershell-samples.md).