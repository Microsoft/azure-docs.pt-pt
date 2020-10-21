---
title: Script PowerShell para criar bloqueio de recursos para a base de dados e recolha da Azure Cosmos MongoDB
description: Criar bloqueio de recursos para a base de dados e recolha da Azure Cosmos MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: ac67e7a8e5575bcbca451335fb0ea837fb70d3d3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282734"
---
# <a name="create-a-resource-lock-for-azure-cosmos-mongodb-api-database-and-collection-using-azure-powershell"></a>Crie um bloqueio de recursos para a base de dados e recolha da Azure Cosmos MongoDB através da Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Os bloqueios de recursos não funcionam para alterações efetuadas pelos utilizadores que se ligam utilizando qualquer MongoDB SDK, Mongoshell, quaisquer ferramentas ou o Portal Azure, a menos que a conta Cosmos DB seja bloqueada pela primeira vez com a `disableKeyBasedMetadataWriteAccess` propriedade ativada. Para saber mais sobre como ativar esta propriedade ver, [Prevenindo alterações de SDKs.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-lock.ps1 "Create, list, and remove resource locks")]

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
