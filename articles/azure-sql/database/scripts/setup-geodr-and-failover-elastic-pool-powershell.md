---
title: 'PowerShell: Configurar a geo-replicação ativa da piscina elástica'
description: Azure PowerShell exemplo script para configurar a geo-replicação ativa para uma base de dados agamada na Base de Dados Azure SQL e falhar.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 36e228c02474fdb20854f73724c541dc1704907a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94594060"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>Utilize o PowerShell para configurar a geo-replicação ativa para uma base de dados agimiuada na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script Azure PowerShell configura a geo-replicação ativa para uma base de dados agamada na Base de Dados Azure SQL e falha-a na réplica secundária da base de dados.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer az PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.ps1?highlight=17-20 "Set up active geo-replication for elastic pool")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe bases de dados e piscinas elásticas. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria uma piscina elástica. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados num servidor. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Atualiza as propriedades de base de dados ou move uma base de dados para, de ou entre conjuntos elásticos. |
| [Novo AzSqlDatabaseSesecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Base de Dados Get-AzSql](/powershell/module/az.sql/get-azsqldatabase)| Obtém uma ou mais bases de dados. |
| [Set-AzSqlDatabaseSesecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Muda uma base de dados secundária para primária de modo a iniciar a ativação pós-falha.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Obtém as ligações de geo-replicação entre uma Base de Dados Azure SQL e um grupo de recursos ou servidor lógico SQL. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure PowerShell, consulte [a documentação da Azure PowerShell](/powershell/azure/).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../powershell-script-content-guide.md).
