---
title: Exemplo do PowerShell de auditoria e proteção avançada contra ameaças – banco de dados SQL do Azure
description: Azure PowerShell script de exemplo para configurar a auditoria e a proteção avançada contra ameaças em um banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: PowerShell
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: carlrab, vanto
ms.date: 08/05/2019
ms.openlocfilehash: 1dee8031b00e7d291844790fb86efd4d00075d87
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691741"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Usar o PowerShell para configurar a auditoria do banco de dados SQL e a proteção avançada contra ameaças

Este exemplo de script do PowerShell configura a auditoria do banco de dados SQL e a proteção avançada contra ameaças.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá AZ PowerShell 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=15-16 "Configure auditing and threat detection")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de banco de dados SQL que hospeda um único banco de dados ou pool elástico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um banco de dados individual ou pool elástico. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma Conta de armazenamento. |
| [Set-AzSqlDatabaseAuditing](/powershell/module/az.sql/set-azsqldatabaseauditing) | Define a política de auditoria para uma base de dados. |
| [Set-AzSqlDatabaseThreatDetectionPolicy](/powershell/module/az.sql/set-azsqldatabasethreatdetectionpolicy) | Define uma política de proteção avançada contra ameaças em um banco de dados. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
