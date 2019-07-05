---
title: Script do Azure PowerShell - Azure Cosmos DB obter débito (RU/s) para a API do Gremlin
description: Script do Azure PowerShell - Azure Cosmos DB obter débito (RU/s) para a API do Gremlin
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: 303bd36149e2a3000ecbf724970b9a43965763fe
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566846"
---
# <a name="get-throughput-rus-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Obtenha o débito (RU/s) para uma base de dados ou um gráfico para o Azure Cosmos DB - Gremlin API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-get.ps1 "Get throughput on a database or graph for Gremlin API")]

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
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Pode ver exemplos do script do Azure PowerShell Cosmos DB adicionais nos [scripts do PowerShell do Azure Cosmos DB](../../../powershell-samples.md).