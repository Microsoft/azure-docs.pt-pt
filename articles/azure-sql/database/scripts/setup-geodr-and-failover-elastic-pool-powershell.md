---
title: 'PowerShell: Configure elástico piscina geo-replicação ativa'
description: O script de exemplo azure PowerShell para configurar geo-replicação ativa para uma base de dados agrupada na Base de Dados Azure SQL e falhar.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4ac9da943f56101661da1308c393b80ccf74e871
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053448"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>Use powerShell para configurar geo-replicação ativa para uma base de dados pooled em Azure SQL Database
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script PowerShell configura geo-replicação ativa para uma base de dados pooled em Azure SQL Database e falha-a na réplica secundária da base de dados.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.ps1?highlight=17-20 "Set up active geo-replication for elastic pool")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe bases de dados e piscinas elásticas. |
| [Piscina Nova-AzSqlElasticpool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria uma piscina elástica. |
| [Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados num servidor. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Atualiza as propriedades de base de dados ou move uma base de dados para, de ou entre conjuntos elásticos. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Obtém uma ou mais bases de dados. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Muda uma base de dados secundária para primária de modo a iniciar a ativação pós-falha.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Obtém as ligações de geo-replicação entre uma Base de Dados SQL Azure e um grupo de recursos ou servidor lógico SQL. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../powershell-script-content-guide.md).
