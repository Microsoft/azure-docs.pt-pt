---
title: Script PowerShell para criar bloqueio de recursos para base de dados e gráfico API Azure Cosmos Gremlin
description: Crie bloqueio de recursos para a base de dados e gráfico da Azure Cosmos Gremlin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 5e4486bf91742bf815515cf3b349fc9191202edb
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126382"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-powershell"></a>Crie um bloqueio de recursos para a base de dados e gráfico da Azure Cosmos Gremlin API utilizando a Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Os bloqueios de recursos não funcionam para alterações efetuadas pelos utilizadores que se ligam utilizando qualquer Gremlin SDK ou o Portal Azure, a menos que a conta Cosmos DB seja bloqueada pela primeira vez com a `disableKeyBasedMetadataWriteAccess` propriedade ativada. Para saber mais sobre como ativar esta propriedade ver, [Prevenindo alterações de SDKs.](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|**Recurso Azure**| |
| [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) | Cria uma fechadura de recursos. |
| [Get-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcelock) | Obtém uma fechadura de recursos ou lista bloqueios de recursos. |
| [Remover-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) | Remove uma fechadura de recursos. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure PowerShell, consulte [a documentação da Azure PowerShell](https://docs.microsoft.com/powershell/).

Pode ver exemplos do script do Azure PowerShell Cosmos DB adicionais nos [scripts do PowerShell do Azure Cosmos DB](../../../powershell-samples.md).