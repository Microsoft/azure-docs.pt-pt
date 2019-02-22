---
title: Exemplo de Script do PowerShell do Azure - restaurar cópia de segurança de aplicação para outra subscrição | Documentos da Microsoft
description: Exemplo de Script do PowerShell do Azure - restaurar uma aplicação web a partir de uma cópia de segurança noutra subscrição
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e8879b67b79523cc35fb28a707a6c77f3efb75c0
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56584984"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Restaurar uma aplicação web a partir de uma cópia de segurança noutra subscrição com o PowerShell

Este script de exemplo obtém uma cópia de segurança concluída anteriormente a partir de uma aplicação web existente e restaurá-lo para uma aplicação web noutra subscrição. 

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure. 

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Se já não precisar da aplicação web, utilize o seguinte comando para remover o grupo de recursos, a aplicação web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Adiciona uma conta autenticada a utilizar para pedidos de cmdlet do Azure Resource Manager.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtém uma lista de cópias de segurança para uma aplicação Web. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação web |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Restaura uma aplicação web a partir de uma cópia de segurança anteriormente concluída. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para as Aplicações Web do Serviço de Aplicações do Azure em [Exemplos do Azure PowerShell](../samples-powershell.md).
