---
title: Roteiro PowerShell para listar e obter operações para Azure Cosmos DB Gremlin API
description: Azure PowerShell script - Lista DB da Azure Cosmos e obter operações para a Gremlin API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: d790164b21419794f47f6fd99ac55824dc8af1a9
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489188"
---
# <a name="list-and-get-databases-and-graphs-for-azure-cosmos-db---gremlin-api"></a>Listar e obter bases de dados e gráficos para Azure Cosmos DB - Gremlin API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-list-get.ps1 "List or get databases or graphs for Gremlin API")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|**BD do Cosmos para o Azure**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Lista contas de DB cosmos ou obtém uma conta de DB cosmos especificada. |
| [Get-AzCosmosDBGremlinDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbgremlindatabase) | Lista as bases de dados da API gremlin numa conta ou obtém uma base de dados de API gremlin especificada numa Conta. |
| [Get-AzCosmosDBGremlinGraph](/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraph) | Lista os gráficos da API gremlin numa base de dados ou obtém uma tabela API de Gremlin especificada numa base de dados. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).