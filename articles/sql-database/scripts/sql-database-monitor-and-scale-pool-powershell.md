---
title: Base de dados do SQL do PowerShell exemplo-monitor dimensionamento elástico conjunto-Azure | Documentos da Microsoft
description: Script de exemplo do PowerShell do Azure para monitorizar e dimensionar um conjunto elástico na base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/07/2019
ms.openlocfilehash: 520fb8f13bd23d2eb9c8dc383665754ae498d79a
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574972"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Utilizar o PowerShell para monitorizar e dimensionar um conjunto elástico na base de dados do Azure SQL

Este exemplo de script do PowerShell monitoriza as métricas de desempenho de um conjunto elástico, dimensiona-o para um tamanho de computação superior e cria uma regra de alerta numa das métricas de desempenho.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o PowerShell de AZ 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de base de dados SQL que aloja uma base de dados individual ou um conjunto elástico. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um conjunto elástico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados individual ou a base de dados num conjunto elástico. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Mostra as informações de utilização de tamanho da base de dados.|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Adiciona ou atualiza uma regra de alerta baseada em métrica. |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | Atualiza as propriedades do conjunto elástico |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Define uma regra de alerta para monitorizar automaticamente DTUs no futuro. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
