---
title: Roteiro powerShell para atualizar as regiões da conta Azure Cosmos
description: Amostra de script Azure PowerShell - Atualizar as regiões da conta Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: f89ebc6a82c8b0a5ee34f40e2a5630e1faeb6b27
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280708"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>Atualize as regiões da conta Azure Cosmos usando o PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

> [!NOTE]
> Não é possível modificar regiões e alterar outras propriedades da conta Cosmos na mesma operação. Estes devem ser feitos como duas operações distintas.
> [!NOTE]
> Esta amostra demonstra a utilização de uma conta API SQL (Core). Para utilizar esta amostra para outras APIs, copie as propriedades relacionadas e aplique no seu script específico da API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

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
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Lista contas de DB cosmos ou obtém uma conta de DB cosmos especificada. |
| [New-AzCosmosDBLocationObject](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdblocationobject) | Cria um objeto de tipo PSLocation para ser usado como parâmetro para Update-AzCosmosDBAccountRegion. |
| [Atualização-AzCosmosDBBacountRegion](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountregion) | Atualizar regiões de uma conta DB cosmos. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).
