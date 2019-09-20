---
title: Azure PowerShell script-Azure Cosmos DB criar o contêiner e o banco de dados da API do SQL (núcleo)
description: Azure PowerShell script-Azure Cosmos DB criar o contêiner e o banco de dados da API do SQL (núcleo)
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 29b0e81dd6eac62a18307788bb023ac3d802f11a
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154859"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-core-api"></a>Criar um banco de dados e um contêiner para a API Azure Cosmos DB-SQL (Core)

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

Esse script cria uma conta Cosmos para a API do SQL (núcleo) em duas regiões com consistência no nível da sessão, um banco de dados com taxa de transferência compartilhada e um contêiner com uma chave de partição, política de indexação personalizada, política de chave exclusiva, TTL, taxa de transferência dedicada e último gravador ganha política de resolução de conflito com um caminho de resolução de conflito personalizado que `multipleWriteLocations=true`será usado quando.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL (Core) API")]

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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Pode ver exemplos do script do Azure PowerShell Cosmos DB adicionais nos [scripts do PowerShell do Azure Cosmos DB](../../../powershell-samples.md).
