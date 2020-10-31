---
title: Script PowerShell para atualizar o nível de consistência padrão numa conta Azure Cosmos
description: Amostra de script Azure PowerShell - Atualizar o nível de consistência padrão numa conta DB Azure Cosmos usando PowerShell
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: ff19d910c03c4957ca50a57db9c5a0587d435930
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099543"
---
# <a name="update-the-regions-on-an-azure-cosmos-db-account-using-powershell"></a>Atualize as regiões numa conta DB Azure Cosmos usando PowerShell
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

> [!NOTE]
> Não é possível modificar regiões e alterar outras propriedades da conta Cosmos na mesma operação. Estes devem ser feitos como duas operações distintas.
> [!NOTE]
> Esta amostra demonstra a utilização de uma conta API SQL. Para utilizar esta amostra para outras APIs, copie as propriedades relacionadas e aplique no seu script específico da API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Update an Azure Cosmos DB account")]

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
| [Atualização-AzCosmosDBBalho](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Atualize uma conta de Cosmos DB. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).