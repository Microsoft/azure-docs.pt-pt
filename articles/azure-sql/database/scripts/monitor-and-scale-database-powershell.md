---
title: Utilize o PowerShell para monitorizar e escalar uma única base de dados na Base de Dados Azure SQL
description: Utilize um script exemplo Azure PowerShell para monitorizar e escalar uma única base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 04c19ca8fbdaed85225b5af128c72d393e5350e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100573254"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Utilize o PowerShell para monitorizar e escalar uma única base de dados na Base de Dados Azure SQL

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script PowerShell monitoriza as métricas de desempenho de uma única base de dados, escala-a para um tamanho de computação mais elevado e cria uma regra de alerta numa das métricas de desempenho.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer az PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!NOTE]
> Para obter uma lista completa de métricas, consulte [as métricas suportadas.](../../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases)
> [!TIP]
> Utilize [o Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) para obter o estado das operações de base de dados e utilize [o Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity) para cancelar uma operação de atualização de base de dados.

## <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe uma única base de dados ou piscina elástica. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Mostra as informações de utilização de tamanho da base de dados.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Atualiza as propriedades da base de dados ou move a base de dados para dentro, para fora ou entre piscinas elásticas. |
| [Add-AzMetricAlertrule](/powershell/module/az.monitor/add-azmetricalertrule) | Define uma regra de alerta para monitorizar automaticamente as métricas no futuro. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure PowerShell, consulte [a documentação da Azure PowerShell](/powershell/azure/).

As amostras adicionais de scripts PowerShell podem ser encontradas nos [scripts Azure PowerShell](../powershell-script-content-guide.md).
