---
title: 'PowerShell: Ligar à Base de Dados SQL'
description: Aprenda a usar o Azure PowerShell para automatizar a implementação e gestão do App Service. Esta amostra mostra como ligar uma aplicação a uma Base de Dados SQL.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: e050675282d8a7784d31aaf45a564da991ff6e63
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020035"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Ligue uma aplicação do Serviço de Aplicações a uma base de dados SQL

Neste cenário, você vai aprender a criar uma base de dados Azure SQL e uma app App Service. Em seguida, irá ligar a base de dados SQL à aplicação utilizando as definições da aplicação.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Após a execução da amostra de script, o seguinte comando pode ser usado para remover o grupo de recursos, app App Service e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [Novo AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação de Serviço de Aplicações. |
| [Novo AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall ao nível do servidor. |
| [Nova AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados ou uma base de dados elástica. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica a configuração de uma aplicação do App Service. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais da Azure Powershell para o Serviço de Aplicações Azure podem ser encontradas nas [amostras Azure PowerShell](../samples-powershell.md).
