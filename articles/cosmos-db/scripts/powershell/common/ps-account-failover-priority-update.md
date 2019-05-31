---
title: Script do Azure PowerShell - prioridade de ativação pós-falha de alteração para uma conta do Cosmos do Azure
description: Exemplo do script do Azure PowerShell - prioridade de ativação pós-falha de alteração para uma conta do Cosmos do Azure
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 2e6fefbbd3db23a41fc64021b19a412d7a12b322
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247893"
---
# <a name="change-failover-priority-for-an-azure-cosmos-account-using-powershell"></a>Alterar a prioridade de ativação pós-falha para uma conta do Cosmos do Azure com o PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account")]

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
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Invoca uma ação num recurso. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Pode ver exemplos do script do Azure PowerShell Cosmos DB adicionais nos [scripts do PowerShell do Azure Cosmos DB](../../../powershell-samples.md).