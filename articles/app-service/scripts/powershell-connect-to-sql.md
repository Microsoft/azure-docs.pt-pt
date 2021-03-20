---
title: 'PowerShell: Ligue-se à Base de Dados SQL'
description: Saiba como utilizar o Azure PowerShell para automatizar a implementação e gestão do Serviço de Aplicações. Esta amostra mostra como ligar uma aplicação a uma Base de Dados SQL.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 3b355a830703b51264d2f4b819d5be7f2627b4e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89077794"
---
# <a name="connect-an-app-service-app-to-sql-database"></a>Conecte uma aplicação de Serviço de Aplicações à Base de Dados SQL

Neste cenário, irá aprender a criar uma base de dados na Base de Dados Azure SQL e numa aplicação de Serviço de Aplicações. Em seguida, ligará a base de dados à aplicação utilizando as definições da aplicação.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to SQL Database")]

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
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação de Serviço de Aplicações. |
| [Novo AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall ao nível do servidor. |
| [Base de Dados New-AzSQL](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados ou uma base de dados elástica. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica a configuração de uma aplicação do Serviço de Aplicações. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Amostras adicionais de Azure Powershell para o Azure App Service podem ser encontradas nas [amostras Azure PowerShell](../samples-powershell.md).
