---
title: Azure PowerShell exemplo de script – conectar um aplicativo a um banco de dados SQL | Microsoft Docs
description: Azure PowerShell exemplo de script – conectar um aplicativo do serviço de aplicativo a um banco de dados SQL
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 799fb67b20c45653ec741a92ec7a75aff2f8c509
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087834"
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
