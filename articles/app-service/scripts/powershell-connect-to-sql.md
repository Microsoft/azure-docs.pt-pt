---
title: 'PowerShell: conectar-se ao banco de dados SQL'
description: Saiba como usar Azure PowerShell para automatizar a implantação e o gerenciamento do serviço de aplicativo. Este exemplo mostra como conectar um aplicativo a um banco de dados SQL.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: bb5fc8bcc99a4439276f53325c029635143c86c5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685391"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Conectar um aplicativo do serviço de aplicativo a um banco de dados SQL

Neste cenário, você aprenderá a criar um banco de dados SQL do Azure e um aplicativo do serviço de aplicativo. Em seguida, você vinculará o banco de dados SQL ao aplicativo usando as configurações do aplicativo.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo do serviço de aplicativo e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo do serviço de aplicativo. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor da Base de Dados SQL. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor da Base de Dados SQL. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados ou uma base de dados elástica. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica a configuração de um aplicativo do serviço de aplicativo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure PowerShell para Azure App serviço podem ser encontrados nos [exemplos de Azure PowerShell](../samples-powershell.md).
