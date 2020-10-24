---
title: Script PowerShell para criar bloqueio de recursos para Azure Cosmos Cassandra API keyspace e tabela
description: Crie bloqueio de recursos para Azure Cosmos Cassandra API keyspace e tabela
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: ab8ffb41d45f60394c27eb492b19eaf280c2e64e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488304"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Crie um bloqueio de recursos para o espaço-chave Azure Cosmos Cassandra API e tabela usando Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Os bloqueios de recursos não funcionam para alterações efetuadas pelos utilizadores que se ligam utilizando qualquer Cassandra SDK, CQL Shell ou o Portal Azure, a menos que a conta Cosmos DB seja bloqueada pela primeira vez com a `disableKeyBasedMetadataWriteAccess` propriedade ativada. Para saber mais sobre como ativar esta propriedade ver, [Prevenindo alterações de SDKs.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

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
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Cria uma fechadura de recursos. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Obtém uma fechadura de recursos ou lista bloqueios de recursos. |
| [Remover-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Remove uma fechadura de recursos. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure PowerShell, consulte [a documentação da Azure PowerShell](/powershell/).