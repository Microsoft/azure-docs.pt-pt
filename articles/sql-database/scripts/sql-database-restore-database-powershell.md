---
title: Exemplo do PowerShell - Restauro e cópia de segurança - Base de Dados SQL do Azure | Microsoft Docs
description: Script de exemplo do Azure PowerShell para restaurar uma base de dados SQL do Azure a partir de cópias de segurança georredundante
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3bfefa704fdd819b3841dcc58866c310353bfdc3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883615"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-from-backups"></a>Utilize o PowerShell para restaurar uma base de dados SQL do Azure a partir de cópias de segurança

Este exemplo de script do PowerShell restaura uma base de dados SQL do Azure a partir de uma cópia de segurança georredundante, restaura uma base de dados SQL do Azure eliminada para a cópia de segurança mais recente e restaura a base de dados SQL do Azure para um ponto específico no tempo.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o PowerShell de AZ 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. | 
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de base de dados SQL que aloja uma base de dados individual ou um conjunto elástico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados num servidor de base de dados SQL como uma autónoma ou uma base de dados em pool. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Obtém uma cópia de segurança georredundante de um autónomo ou bases de dados agrupadas. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Restaura um SQL autónoma ou bases de dados agrupadas. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Remove um Azure SQL autónoma ou bases de dados agrupadas. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém um autónomo eliminado ou bases de dados agrupadas que pode restaurar. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
