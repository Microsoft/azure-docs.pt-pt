---
title: PowerShell exemplo-monitor-escala-base de dados SQL
description: Script de exemplo Azure PowerShell para monitorizar e escalar uma piscina elástica na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 683be926a8237b78b27a50e1aee2d4f72b77af8e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053712"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Use powerShell para monitorizar e escalar uma piscina elástica na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script do PowerShell monitoriza as métricas de desempenho de um conjunto elástico, dimensiona-o para um tamanho da computação superior e cria uma regra de alerta numa das métricas de desempenho.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale an elastic pool in SQL Database")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe bases de dados ou piscinas elásticas. |
| [Piscina Nova-AzSqlElasticpool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria uma piscina elástica. |
| [Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados num servidor. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Mostra as informações de utilização de tamanho da base de dados.|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Adiciona ou atualiza uma regra de alerta baseada em métrica. |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | Atualiza as propriedades do conjunto elástico |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Estabelece uma regra de alerta para monitorizar automaticamente as métricas no futuro. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script PowerShell podem ser encontradas em [scripts Azure PowerShell](../powershell-script-content-guide.md).
