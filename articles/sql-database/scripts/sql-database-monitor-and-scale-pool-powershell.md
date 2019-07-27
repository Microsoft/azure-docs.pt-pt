---
title: Exemplo do PowerShell-monitor-escala – pool elástico – banco de dados SQL do Azure | Microsoft Docs
description: Azure PowerShell script de exemplo para monitorar e dimensionar um pool elástico no banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4d096600e57d3ca94a7c32547bd546bfdbeb9487
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569934"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Usar o PowerShell para monitorar e dimensionar um pool elástico no banco de dados SQL do Azure

Este exemplo de script do PowerShell monitoriza as métricas de desempenho de um conjunto elástico, dimensiona-o para um tamanho da computação superior e cria uma regra de alerta numa das métricas de desempenho.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá AZ PowerShell 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de banco de dados SQL que hospeda um único banco de dados ou pool elástico. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool elástico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um banco de dados ou banco de dados individual em um pool elástico. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Mostra as informações de utilização de tamanho da base de dados.|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Adiciona ou atualiza uma regra de alerta baseada em métrica. |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | Atualiza as propriedades do conjunto elástico |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Define uma regra de alerta para monitorizar automaticamente DTUs no futuro. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
