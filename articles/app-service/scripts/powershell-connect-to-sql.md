---
title: Script do Azure PowerShell de exemplo - ligar uma aplicação a uma base de dados SQL | Documentos da Microsoft
description: Script do Azure PowerShell de exemplo - ligar uma aplicação de serviço de aplicações a uma base de dados SQL
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: d0c38b69d8cb2fda85a85fd27f0a5b26da51b699
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106210"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Ligar uma aplicação de serviço de aplicações para uma base de dados SQL

Neste cenário, aprenderá como criar uma base de dados SQL do Azure e uma aplicação de serviço de aplicações. Em seguida, vai ligar a base de dados SQL à aplicação com as definições da aplicação.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, a aplicação de serviço de aplicações e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação de serviço de aplicações. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor da Base de Dados SQL. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor da Base de Dados SQL. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados ou uma base de dados elástica. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modificar a configuração de uma aplicação de serviço de aplicações. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure Powershell para o serviço de aplicações do Azure podem ser encontrados no [exemplos do Azure PowerShell](../samples-powershell.md).
