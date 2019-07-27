---
title: Exemplo PowerShell - Georreplicação ativa - Base de dados SQL do Azure única | Microsoft Docs
description: Azure PowerShell script de exemplo para configurar a replicação geográfica ativa para um banco de dados individual no banco de dados SQL do Azure e fazer failover dele.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4319a393382375b080c9d4c0d3c80aa3782c6afd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569815"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-single-database-in-azure-sql-database"></a>Usar o PowerShell para configurar a replicação geográfica ativa para um banco de dados individual no banco de dados SQL do Azure

Este exemplo de script do PowerShell configura a replicação geográfica ativa para um único banco de dados e faz o failover dele para uma réplica secundária do banco de dados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá AZ PowerShell 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-single-database.ps1?highlight=18-21 "Set up active geo-replication for single database")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de banco de dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool elástico. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Atualiza as propriedades de base de dados ou move uma base de dados para, de ou entre conjuntos elásticos. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Obtém uma ou mais bases de dados. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Muda uma base de dados secundária para primária, para iniciar a ativação pós-falha.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Obtém as ligações de georreplicação entre uma Base de Dados SQL do Azure e um grupo de recursos ou o SQL Server. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Termina a replicação de dados entre uma Base de Dados SQL e a base de dados secundária especificada. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
